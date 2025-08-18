
```ts

async selectShiftAndLoginTimeByWorkerId(workerId: number, now: Moment) {
    let sql = `WITH tmp AS (SELECT 
      "Shift"."id", 
      "workerId", 
      "workStartAt", 
      "workEndAt", 
      "workHour", 
      "officeId", 
      "shiftType", 
      "date", 
      "deletedAt",
      "office_location"."name", 
      "office_location"."loginBeforeStartAt", 
      "office_location"."reserveScooterBeforeStartAt", 
      "office_location"."logoutAfterEndAt", 
      "office_location"."releaseScooterAfterEndAt", 
      "office_location"."timezone" AS "timezone",
      ((to_timestamp(cast("date" AS text)  || ' ' || "workStartAt",  'YYYY-MM-DD HH24') - INTERVAL  '1 hour' * CAST("loginBeforeStartAt" AS numeric)) AT time zone 'utc') AT time zone "timezone" AS "loginStartAt",
      ((to_timestamp(cast("date" AS text)  || ' ' || "workStartAt",  'YYYY-MM-DD HH24') + INTERVAL  '1 hour' * CAST("workHour" AS numeric) + INTERVAL  '1 hour' * CAST("logoutAfterEndAt" AS numeric) ) AT time zone 'utc') AT time zone "timezone" AS "loginEndAt"
      FROM "${this.configService.get('mnemosyne').schema}"."Shift"
      LEFT JOIN "${this.configService.get('mnemosyne').schema}"."office_location" 
      ON "Shift"."officeId" = "office_location"."id"
      )
      SELECT * FROM tmp WHERE '${now}' BETWEEN "loginStartAt" AND "loginEndAt" AND "workerId" = '${workerId}' AND "deletedAt" is NULL`
    return await this.repo.query(sql)
  }

```


# 數據庫查詢分析

## 效能問題的可能原因

1. **複雜的子查詢和 JOIN 操作**:
    - 查詢使用了 WITH 子句創建一個複雜的臨時表（CTE）。
    - 包含了 LEFT JOIN 操作，可能會產生大量的中間結果。
2. **時間轉換和時區操作**:
    - 查詢中包含多個時間轉換和時區調整操作，這些都是計算密集型的。
    - 例如：`AT time zone 'utc'` 和 `AT time zone "timezone"` 的多次使用。
3. **字符串轉換和日期時間操作**:
    - 使用 `to_timestamp` 和字符串連接來處理日期和時間，這可能會很慢。
4. **動態 SQL**:
    - 查詢是動態構建的，這可能會阻止數據庫有效地緩存執行計劃。
5. **缺少索引**:
    - 如果相關列（如 `workerId`、`loginStartAt`、`loginEndAt`）沒有適當的索引，可能會導致全表掃描。
6. **大範圍的時間比較**:
    - `BETWEEN` 操作可能會掃描大範圍的數據。

## 優化建議

1. **索引優化**:
    - 確保 `workerId`、`date`、`workStartAt`、`workEndAt` 等列有適當的索引。
    - 考慮為常用的查詢條件創建複合索引。
2. **查詢重構**:
    - 考慮將一些計算移到應用層，減少數據庫的計算負擔。
    - 簡化 CTE，只選擇必要的列。
3. **分區**:
    - 如果數據量大，考慮按日期或 `workerId` 進行表分區。
4. **緩存**:
    - 對於頻繁查詢的數據，考慮使用應用層緩存（如 Redis）。
5. **預計算**:
    - 考慮在插入或更新數據時預先計算 `loginStartAt` 和 `loginEndAt`，存儲到表中。
6. **參數化查詢**:
    - 使用參數化查詢替代字符串拼接，以便更好地利用查詢計劃緩存。
7. **非阻塞操作**:
    - 在 Node.js 中，考慮使用非阻塞操作來執行查詢，提高並發處理能力。
8. **負載均衡**:
    - 如果可能，考慮使用讀寫分離或多個只讀副本來分散負載。
9. **監控和分析**:
    - 使用數據庫監控工具來識別具體的瓶頸點。
    - 分析查詢執行計劃，找出耗時步驟。

實施這些優化可能需要對數據庫結構和應用程序邏輯進行一些調整。建議逐步實施並測試每項更改的影響。