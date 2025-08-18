[[db_idx]]
[[db-udemy-acid, ACID]]
[[共享鎖 vs 獨占鎖]]
[[DDIA DB transaction 交易]]

---
ref: [Transactions Are Not Locks](<https://www.benburwell.com/posts/transactions-are-not-locks/>)



TX 是 交易
交易裡面有 ACID 保證

Lock 是實現 cocurrent 機制的手段
- 有樂觀鎖(like MVCC)，悲觀鎖(like 基本 2PC)
- 有共享鎖，獨佔鎖
- 等等觀念



- TX 有 包括 ACID 保證
	- A of ACID
		- 是確保一籃子的操作一起成功或一起失敗
			- 一起成功或一起失敗 called atomic
			- in DB, a transaction(tx) ensure atomic
		- 類似你要轉帳, 確保轉出和轉入都一起成功或失敗
	- I of ACID
		- 比較有關預防 cocurrent
			- [[pg idx use cocurrent]]
			- [[pg isolation level]]
	    - 確保同時間只能有一個程序在碰到這段code
	    - 預防app/前端的 retry 機制打一堆過來，但我們ensure only one process run it
	    - DB 的 tx 可以設定不同的 isolation level


````markdown
```js

// 一個基本的 tx, 可以確保 A 有扣到，同時 B 有轉入 才算成功
function moveMoney(from, to, amount) {
   // Start a transaction.
   txn = db.begin()
   // Update the balances.
   txn.execute('update accounts set balance = balance - $amount where name = $from')
   txn.execute('update accounts set balance = balance + $amount where name = $to')
   // Commit the transaction.
   txn.commit()
}

// 加上更多 edge case 的保護
function moveMoney_more_safer(from, to, amount) {

   // 也可以處理 amount < 0 的情況
   // Moving a negative amount of money from A to B is equivalent to moving the
   // corresponding positive amount from B to A.
   if (amount < 0) {
      moveMoney(to, from, -1 * amount)

      return
   }

   txn = db.begin()
   // 如果不夠錢就可以拋錯
   currBalance = txn.query('select balance from accounts where name = $from')
   if (currBalance < amount) {
      txn.rollback()
      throw exception

   }
   txn.execute('update accounts set balance = balance - $amount where name = $from')
   txn.execute('update accounts set balance = balance + $amount where name = $to')

   txn.commit()

}
````

上面兩種都無法處理 co-current 的各種情境 想要完全移除 co-current的話， 用 for-update -> row lock OR constraints see this [https://www.benburwell.com/posts/transactions-are-not-locks/](https://www.benburwell.com/posts/transactions-are-not-locks/)

```sql
begin transaction
select balance from accounts where name = 'A' for update;
update accounts set balance = balance - 10 where name = 'A';
update accounts set balance = balance + 10 where name = 'B';
commit;

```

```sql
begin transaction isolation level serializable;
select balance from accounts where name = 'A';
update accounts set balance = balance - 10 where name = 'A';
update accounts set balance = balance + 10 where name = 'B';
commit;

```
