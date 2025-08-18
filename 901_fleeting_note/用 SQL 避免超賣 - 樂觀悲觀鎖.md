ref: [[面試][資料庫] 關聯式資料庫要如何設計避免超賣？ - iT 邦幫忙：：一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10271229)


如果需要高吞吐，用樂觀鎖
如果不需要，那就用悲觀鎖來保護就可以

樂觀鎖
Optimistic lock is usually impl via adding a version number and when it going
to commit, it will first check the version and see if it can commit or abort
(a simple approach is that, if the version is getting newer, meaning other process
temper the data, so this process can't commit)

悲觀鎖
it means the the data can only modify via a process
which hold lock, the how long the lock is holding, other porcess can't modify it, 
which is the reason why this affetec the thourtput)

related: [[DDIA DB transaction 交易#序列化快照隔離 serializable snapshot isolation (SSI)]]



悲觀鎖的實作大概如下

視窗1
```sql

begin;
lock for id=1 
select num from `your_DB`.`items`
where id = 1 for update;

~~ 還沒 commit
```

視窗2 要拿，會 hold 因為上面的 tx 已經拿到 lock
```sql

select num from your_DB.`items` 
where id = 1 for update;

```

回到視窗1
```sql

update `your_DB`.`items` 
set num = num -1 where id = 1;

select num from `your_DB`.`items` where id = 1;

commit;
```

這時候如果回到視窗2
你應該看到 commend is abort since num is 0


---

樂觀鎖的實作大概如下

我們用一個 version 自己控制

at begin a, b can run this qeury to get the same result indicating it's okay to buy
```sql

SELECT num,version 
FROM `your_DB`.`happy_items` where id = 1;

```

假設 a 先跑下面這個
```sql
update `your_DB`.`happy_items` 
set num = num -1 , version = version + 1 
where id = 1 and version = 0;

```

如果 b 接著跑 一樣的指令，因為 version 已經變了，會 abort
b 不會被 block, 因為沒有 lock 
因此就是 first run win and second run abort
