[[_wemo_tickets]]

---



https://docs.google.com/document/d/1WLkjlXRBGLsXqY-tH-w0PfZfit3DCCGSA4ao__6ykbQ/edit?pli=1




- [ ] ask Jack help archive Janus
- [ ] 要先 archive, dump raw sql 就可以 -> 下午弄


# ceres

砍掉 ceres pt order








ceres user 表沒有 和相關表有 關連
因此我們可以直接把 code 的關聯拿掉
![[IMG-拔mongo janus-20241003104934110.png]]







# 砍 db


- [ ] if wana delete DB -> 要跟 data team 拉會確認


這三種表可以砍掉, 有順序
PointOrders
PointDetail
Mengo

PointOrders -> PointDetail -> Mengo

PS: 這三張表的最新更新記錄為 2023 May 18
![[IMG-拔mongo janus-20241003104948957.png]]


===



先改名

```sql

PointDetail -> Mengo -> PointOrders


ALTER TABLE Mengo RENAME TO Mengo_Deprecated;
ALTER TABLE PointDetail RENAME TO PointDetail_Deprecated;
ALTER TABLE PointOrders RENAME TO PointOrders_Deprecated;



如果有問題再改回來

```


系統跑一陣子， like 1-3 week in QAT -> no problem -> 1-3week in prod 
```sql

-- 不下  CASCADE  如果走狀況會提醒




```


如果都沒問題，再砍


[[db 依賴關係 dependency]]