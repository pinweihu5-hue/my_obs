


# 例子1


pricing migrate
我們在 hermes db 建立好後
用 insert 在 pricing  db  insert 貼上 
如果你到下面的地方看，你會發現 seq 是錯誤的, 是小於我們實際的值的
因此會導致無法寫入, 噴錯，因為 id 已經 duplicate

解法就是要去矯正 seq
(pg seq 是另外存的，沒有跟資料在一起)



這邊可以看 seq val
要在外層
這邊也可以改，也會 show出 改的 sql

doc: [PostgreSQL: Documentation: 17: ALTER SEQUENCE](https://www.postgresql.org/docs/current/sql-altersequence.html)

> 可選子句``RESTART [ WITH _`restart`_ ]``更改序列的目前值。這類似於使用`is_called` = `false`呼叫`setval`函數：指定的值將在_下次_呼叫`nextval`時傳回。寫入不帶_`restart`_值的`RESTART`相當於提供`CREATE SEQUENCE`記錄的起始值或`ALTER SEQUENCE START WITH`最後設定的起始值。

like below
```sql


下面的數字是 
這類似於使用`is_called` = `false`呼叫`setval`函數：指定的值將在_下次_呼叫`nextval`時傳回
因此如果目前 id = 5, 那要設定6

ALTER SEQUENCE "pricing-qat".pricing_id_seq
	RESTART 6; 

ALTER SEQUENCE "pricing-qat".pricing_timeline_id_seq
	RESTART 124;

ALTER SEQUENCE "pricing-qat".pricing_rule_id_seq
	RESTART 1945;



下面這個語法還要調整，會噴掉，問 gpt

ALTER SEQUENCE "pricing-qat".pricing_id_seq RESTART (SELECT MAX(id) + 1 FROM "pricing-qat".pricing);
ALTER SEQUENCE "pricing-qat".pricing_timeline_id_seq RESTART (SELECT MAX(id) + 1 FROM "pricing-qat".pricing_timeline);
ALTER SEQUENCE "pricing-qat".pricing_rule_id_seq RESTART (SELECT MAX(id) + 1 FROM "pricing-qat".pricing_rule);





```


![[IMG-pg-新增資料請確保不要自己建立 id-20250810210638498.png]]












---
# 例子2
## 問題說明

WeMo Pass Apollo 新增方案時無法新增，遇到 500 回應，追蹤 Kibana Log 出現以下紀錄

![[IMG-pg-新增資料請確保不要自己建立 id-20250810210639286.png]]
  

## 發生原因

1. 先前建立資料時使用下面的 insert 語句建立方案資料
```sql
INSERT INTO hermes.vip_plan

(id, title, "durationMonth", "createdAt", "updatedAt", "deletedAt")

VALUES(1, '{"en": "WeMo PASS (Monthly)", "zh-tw": "WeMo PASS 訂閱 (月)"}'::jsonb, 1, '2022-09-19 14:16:29.491', '2022-09-19 14:16:29.491', NULL);

```
2. 因為自己插入 id 導致 auto increments 的 `nextval` 沒有建立初始值/沒有正確增加。
3. 因為沒有正確建立或增加，導致 insert 時自動取得 nextval 會取到衝突的 ID 而無法寫入噴錯。

## 方法

1. 取得 sequence 的 name
1. By SQL: 
`select pg_get_serial_sequence(“tableName”, “columnName”);`
2. 校正/建立缺少的 sequence
    


Example
留意單引號包雙引號
`SELECT setval(pg_get_serial_sequence('"TimePlan"', 'id'), (SELECT MAX("id") FROM "TimePlan")+1);

## 快速解法

- 在 DBeaver 中直接新增一筆資料，但 auto increment 欄位留預設值，save 後即可解決
- 但新增資料請確保不要自己建立 id




## 確認目前 seq 的 last value
`SELECT last_value FROM "vehicle_id_seq";`
`SELECT LAST_VALUE FROM "vehicle_plate_id_seq";`





![[IMG-pg-新增資料請確保不要自己建立 id-20250810210640299.png]]

# 參考資料
- [https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-CATALOG-TABLE](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-CATALOG-TABLE)
- [https://bobcares.com/blog/postgresql-error-code-23505/](https://bobcares.com/blog/postgresql-error-code-23505/)
- [https://www.postgresql.org/docs/current/sql-createsequence.html](https://www.postgresql.org/docs/current/sql-createsequence.html)
- [https://kibana.wemoscooter.com/goto/4cbb1fc36a47daa3b38a6442cb6df0b0](https://kibana.wemoscooter.com/goto/4cbb1fc36a47daa3b38a6442cb6df0b0)
