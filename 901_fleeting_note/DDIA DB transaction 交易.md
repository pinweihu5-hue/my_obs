related: 
- [[db-udemy-acid, ACID]]  -> 這邊有 pg demo
- [[pg isolation level]]
- [[共享鎖 vs 獨占鎖]]

# 為何我們需要 transaction

- why need transaction?
    - db can crash in the middle of write
    - app can crash in the middle of write
    - network can disconnect in the middle of write
    - multi client concurrent write can cause trouble, 像是 read/write skew (see below for what is read/write skew) and abnormally
- db use transaction abstraction to make app can handle logic more easily
    - no need to worry about above issue
    - since one transaction is either committed or abort


# What is ACID?  atomicity, consistency, isolation, durability
- the safety guarantee of transaction

## atomicity
- in general, it means something can’t be break down in to multiple parts
- in multi-thread, it means a process op is atomic
- means the app state can only be before and after state of that atomic op, not in the middle
- 不要誤會了.. the atomicity of AICD is not about cocurrent, the cocurrent issue for ACID is achieve on isolation, I
- So, atomicity in AICD means, when something bad happen, the transaction can be either committed or aborted, only this two state. -> maybe better name is  **abortibility**

## consistency
- this idea is depend on how application define what invariant is
- tx only make db from a valid state to another valid state
- it also means there’s some invariant in ur system must always hold true before and after tx
    - like credit and debit must always hold balance after the transaction.
    - constraint like, FK constraint, pk constraint, unique constraint...etc
- about ensure data integrity before and after a tx
- like **constraint** defined in db, **cascade rolling** back to a original state
- more like an app-level guarantee


PS: 留意，consistency 這個字，在不同脈絡有不同意思
- In ACID, it’s about the some invariant need to always hold true
- in CAP, consistent means Linearlizability
	- [[分布式系統 CAP]]
- On replication, we talk about eventually consistentency, which means it will eventually finish all replication and all data copied (async replication)
- **consistent hash** is a way to rebalancing in partition
	- [[Consistent Hash]]

## isolation
- concurrently executing transaction are isolated from each other
- some textbook called this serializability 序列化
- different db engine imply different level of isolation
    - like Oracle use 快照隔離 (**snapshot isolation**), a weaker serializable guarantee (比較弱的序列化保證)
    - see [[Consistency and Consensus, DDIA CHAPTER 9|ddia consistent, ddia consensus]]

## durability
- a guarantee that after a transaction had committed, even there’s a hardware fault or db crash, the data is durable
- in a single node, it means the data had written into hd/ssd(use write ahead log, WAL)
- in a replicated system, it means the data had written into replicated db
- a db need to wait until above written completed to report the transaction is committed

- 100% durability?
    - even the data is written into disk and machine crash, ur service is still down until u can repair the machine or move the disk to anothe running system -> failover 機制   see [[replication (ddia)#how to handle node down?|ddia replica]]
    - a black out can make ur data-in-memory gone. 
	    - So, in-memory-db may still need to write into disk (like redis)
    - in async replication sys, if active down, and if the data is not replica to follower, the data is lost
	    - see [[replication (ddia)|ddia replica]]
    - so we need to have replica, since
	    - the data in ur ssd will be gradually malfunction
	    - ssd will have bad block for the first 4 year
	    - if power outage happened in ssd, due to temp changes, ssd could lose data


# Single object and Multiple object operation

**if u modify multiple obj concurrently, u need multi-obj transactions**


say below query is to find unread mail
```sql

SELECT COUNT (*)
FROM emails
WHERE recipient_id = 2 AND unread_flag = true

```

因為 we think this query is slow, so we maintain another var called `unread`, which will +1 when new mail arrvied and if you read it, it will -1



髒讀例子, user2 讀到 user1 還沒 commit 的 write
- since user1 has not update the unread variable yet but user2 read it
- memonic: read skew, meaning this read is wrong, called dirty read (this read is dirty or bad)
- one sentence of dirty read:
	- reads data written by a concurrent uncommitted write
![[IMG-DDIA DB transaction 交易-20250810210633908.png]]

- so we need isolation user1 all needed op from user2
    - user1 need to complete all its transaction and then data then can be visible to user2
- for SQL Syntax, the operation b/n `BEGIN TRANSACTION` and `COMMIT` is one transaction



for single-object write ⇒ still to have atomicity and isolation

假設 app write to db 20kb img data, and then network failure at the time when only 10 kb only, which is unusable for half img data
what we can do?
- we can use write-ahead-log to repaire the rest of the data (assume WAL had written )
- to prevent dirty read, we use lock to allow only one thread can access that object


we also can use CAS (compare and set), check if the value is not being modifed by other before set
	- like t1 and t2, 2 thread cocurrent modify obj, t1 finish first, and when t2 try to finish its own change, it first compare the obj1 to its original state and found that the obj is changed, so t2 abort the modification.


for multi-object transaction
- it’s hard to do but it’s still important, like relational db use JOIN
- graph mode, many-to-many, which we can use multi-object transaction
- in document db, many info close together and it encourage denormalization, and those denormalization need to update which will be like multi-object transaction
- secondary idx in db, which is like another object in db


atomic is good (commit or abort), but there’s some drawback:
- say your req is success but it fail to response back 200, so you have to retry, so the op need to be idempotent
- if error is cause by overloading, then retries make the situation worse
	- need to apply exponential backoff and introduce randomness to avoid thundering herd
- it’s good to retry if the error is temporary (e.g. deadlock, temporary network fail, isolation violation, failover), if it’s a permanent error (violate constraint), then retry is meaningless
- if the transaction have side effect outside the db, then even ur abort the transaction, the side effect remain and you need to handle it.
	- like if fail, u send the mail to notify, so u don’t want to send each mail for each retry. (2 phrase commit could help)



# 盤點 DB 併發問題  (go deep with isolation )

this is about I of ACID


(How db solve concurrent issue)

The SQL standard defines four levels of transaction isolation.*
- *The most strict is Serializable: any concurrent execution of a set of Serializable transactions is guaranteed to produce the same effect as running them **one at a time** in some order.*
- *The other three levels are defined in terms of phenomena, resulting from interaction between concurrent transactions, which must not occur at each level.*


x軸: 併發異常行為
y軸: DB一般會提供的隔離等級

X 表示不會發生
因此下面最後一個 Serializable (序列化)可以預防全部的情況 (SQLite 的 default 好像就是這個)
然後， read commited 可以預防髒讀寫  (這個好像是pg 的 default)
relatedable read, 預防髒讀寫，和無法重複讀 (pg support this)

![[IMG-DDIA DB transaction 交易-20250810210634359.png]]

PS:
- 上面沒有 dirty write, 因為基本上這是最最最基本的保證，寫入不會互相影響，不會是你可以調整的 level 選項之一。
- 下面的 phantom read 我自己給他另一個別名，叫做 condition break, 比較好記憶，因為就是交易前的結果改變了交易的條件


隔離級別越高， overhead 越高， so we trade weaker level of isolation for higher availability or performance
ps: [[pg isolation level]]


下面來看看併發異常行為
## 1. 髒寫 
dirty write
write into other dirty data, dirty data means it is undergoing tx
基本上，no any isolation about transaction

example
- two ppl want to buy car
- to buy a car, need to 2 op in one transaction:  update product table and then update invoice table
- here, we use Last write win, so Bob show the product bought
- but here alice transcation-second-op is slower, so invoice send to alice
- dirty write, since Bob write into the dirty data, which shall not happen
![[IMG-DDIA DB transaction 交易-20250810210634896.png]]
- dirty write is the first need to address problem in transaction current issue
- Write operations across take shall wrap into one tx
- if you got dirty write ⇒ the sys don’t have any transaction isolation in place


## 2. 髒讀 
dirty ready - B 可看到 A 還沒 commit 的結果

- dirty write means, write A is not commit yet, while A is in the middle of transaction, but B write took effect and mess up the result
- so dirty read means, write A is not commit yet, while A is in the middle of transaction, but B can read and see the A’s half result
- again, dirty read and dirty write are rather basic level of transaction isolation guarantee
- Below flow: no dirty read, user1 update x to 3 but user2 still see 2. user2 only see x is 3 after user 1 commit.
![[IMG-DDIA DB transaction 交易-20250810210635643.png]]
- if user2 can see x = 3 before user1 commit and if user 2 use x = 3 as condition to somthing….but user 1 abort for whatever reason and rollback, what are you doing to handle user 2 actions (based on x=3), this is also a very big mess up

## 3. 不可重複讀 
non-repeatable read

- even you prevent dirty read and write....you can still have this case: read a row and found out it has been modified and result in a invalid state




- see below, at begin, both a/c are 500, and total is 1000, transfer will make a tx: transfer 100 from a/c 1 to a/c 2
- Alice see the a/c 1 is 500, at the same time or con-currently , the tx just begin (but not make it 600 yet)
- and after transaction finish, Alice check a/c 2, which is 400
- for Alice POV, it’s 500+400=900, 100 is vanish!
- what if Alice check a/c 1 again?  she will see 600, but the above case is still no acceptable
![[IMG-DDIA DB transaction 交易-20250810210636439.png]]



- **A non-repeatable read occurs, when during the course of a tx, a row is retrieved twice and the values within the row differ between reads**
    - in this case, the row is about the total-balance of alice account, which result a invalid state (vanish 100 during a tx)
- A transaction re-reads data it has previously read and finds that data has been modified by another transaction (that committed since the initial read).


## 4. 條件違背
幻寫, 寫偏序, 條件違背 phantom read/ write skew

- even you can prevent read-skew (we will mentioned how to solve it via snapshot isloation later)……you will still have phantom read



- the contraint is that hospital must have 1 doc oncall
- alice and bob, cocurrently call the if statement to check and it said there’s ≥2 doc at the moment…they can commit and take a day off!
- the result is no doc oncall
![[IMG-DDIA DB transaction 交易-20250810210637395.png|714]]


this problem arise due to they both reply on a condition to know if they can conti to commit or not but the commit result change the condition they checked **


- many similar case:
    - In sign-up process, users concurrently check if they can use the the same username
    - only 100 left in back a/c and two write concurrently want to withdraw 100
- the principle of this is about the one transaction result break the the condition to run tx
- In sum, a tx re-executes a query returning a set of rows that satisfy a search condition and finds that the set of rows satisfying the condition has changed due to another recently committed tx.
- we need serializable level (序列化等級的) of isolation to solve this


---


# 如何解決 DB 併發問題
##  預防髒讀/寫 -> 提交讀 (Read Committed level)

use Read Committed level
- prevent dirty write
    - row-level lock when write
    - meaning to modify that row, u need to get the write lock first and no other process can modify that row without getting lock
- prevent dirty read
    - row-level lock when read
    - what if many rows got modified and you need to take lots of time to get all rows lock for read, which make read slow?
    - see below 原文

> for every object that is written, the database remembers both the old committed value and the new value set by the transaction that currently holds the write lock. While the transaction is ongoing, any other transactions that read the object are simply given the old value. Only when the new value is committed do transactions switch over to reading the new value.


## 預防無法重複讀 ->快照隔離 (Multi Version Concurrent Control (MVCC), snapshot isolation)


- repeatable read problem is go back to normal if Alice check a/c 1 again, so what’s the big deal?
    - say in the backup process and you have this repeatable read problem, so this “temporary” problem become “permanent” problem and saved into ur snapshot
    - it’s possible this repeatable read problem happen in your analytic query result or integrity check result, you do not want that happen
- solve: repeatable read level, which use snapshot isolation, read only from the snapshot

我們來看看實作上的概念是如何

**db maintain different version of a object, each cp-current tx will need to see different version of the object at different point, this is so called MVCC, multi version cocurrenty control**



- below show how PostgreSQL impl MVCC
- when each tx write into db, it has a transaction id, txid, this txid will decided if the process can see it or not
- so when txid 12 want to check the a/c 1, it will check the previous snapshot, which is still 500, not 400,  problem solved!!
    - the reason txid didn’t see 400 is cuz 400 will only possible been seen on txid13, which make the transfer. And since txid13 is not committed yet, txid 12 won’t see that version.
- Below graph also show that we also can not delete the row right aways, we just mark it and only visible when txid 13 successfully committed and later the Postgres will delete it by its vacuum bg process when there’s no other txid might need it
![[IMG-DDIA DB transaction 交易-20250810210638511.png|854]]
- the ideas is: if txid12 start, it will ignore other concurrently tx write (whether other tx abort or not).
    - So, tx12 just read its own snapshot during the tx period
    - tx12 just use its snapshot to have a consistent read result
- The visibility rule:
    - when reader tx start, it can only see other objs(created by other tx), if **both** following are true
        - already committed
        - the obj is not marked for deletion OR the obj is marked deletion but the deletion is not yet committed

## 預防幻寫, 寫偏序, 條件違背 -> Serializable 線性一致保證
(prevent write skew / phantom write / condition break))


三種常見作法：Actual Serial Execution, two-phrase locking and serializable snapshot isolation


### Actual Serial Execution  (redis is a good example)
(redis is a familiar example using Lua)

- just pull all stuff in a single thread/process and use this process to do it, and then it will just do transaction one by one
- it’s possible now since
    - ram is cheaper, affordable to put many things in a thread and since it’s in ram, it is faster enough to do it
    - OLTP, compared to OLAP, is not involve too much data moving, so it could be acceptable to do that
- put transaction in “store procedure”

like below, the app must submit the entire tx / transaction code into db ahead of time, as a store procedure. All data in tx is in memory, no slow disk and network io req.
![[IMG-DDIA DB transaction 交易-20250810210639392.png|772]]
- con for store procedure?
    - some Syntax for store procedure is out date, it’s not a beauty syntax to work w/ (Oracle → PL/SQL, SQL Server → T-SQL, PostgreSQL → PL/pgSQL)
    - compared to code in app, use db to run up the store procedure is more difficult to debug, mgmt, ver control and deploy
    - the perf will be more critical in this store procedure code, if the code is not write well, will have more impact
- however, nows days, we have more beauty store procedure code like , VoltDB use Java/Groovy, Datomic use Java/Clojure, Redis use Lua
- partition
    - since it’s only in a single thread, so the throughput is limited by the speed of a single thread
    - if mostly is read req, we can use snapshot across machines to handle
    - if mostly is write req, we can’t. the single thread speed could be the button-neck
    - if you can find a good way to partition and also each tx in each partition using its own thread to do job, that’s good, the throughput is linearly grow to how many cpu core / machine (say 1 core in 1 machine) u have
    - but if your tx need to go across different partition, then u need a coordinator to help and it need to lock some part of partition for a tx during the tx process, which will have much larger overhead
        - this also depend on how ur app data structure look like, for a simple one, key-val, could be easier
        - but if u have something like global secondly idx, need to coordinate across partition, that’s have much overhead
- sum:
    - each tx need to be small and fast, since u only got one thread
    - mostly need to do all job in memory, if it involve go to disk/network I/O, it become slow
    - throughput need to handle in single core
        - or you can find way to partition and no need to coordinate one tx across different partition


### two-phrase locking (2PL), 共享和獨佔鎖


不要跟 2PC(2 phrase commit, in Chap9 ) 搞混，兩個不同的事情。

- 2 phase locking means:
    - if tx1 is reading this obj, and tx2 want to write it →  Can’t.   tx2 need to wait until tx1’s read is committed/abort
    - if tx1 is writing this obj, and tx2 want to read it - > Can’t.  tx2 need to wait until tx1’s write is committed/abort
    - a write will block all other read/write access of other process
    - a read will block all other read processes
    - 2PL solve the read skew/repeatable read

- 2PL 表示第一階段拿 lock, do stuff , 最後 release lock
    - it’s about having 2 kind of lock: sharing and exclusive, 共享鎖和獨佔鎖
    - if a tx want to read, it need to get 共享鎖
    - 共享鎖, 顧名思義，可以讓很多 processes/tx 一起拿
    - if any process/tx got a 獨佔鎖 for an obj, no other lock can access it
    - if a tx want to write, it need to get exlcusive lock
        - other tx can’t get the lock for this obj, either sharing lock or exclusive lock, need to wait
    - if a tx read and then write, it will get sharing lock and then update to exlcusive lock
        - the process to get the exclsive lock is the same as above (need to wait if another tx got exclusive lock)
    - once a tx get a lock, it will take the lock and release it until the tx is done
    - the naming of 2PL means, at the first phase, it get the lock, the second phrase it release the lock
    - dead lock can happen (when tx_a wait tx_b and  tx_b wait tx_a)
	    - db will auto detect deadlock and resolve it but it produce overhead

- 效能?
    - not that good for latency and thoghtput, due to lock
    - it take time to get lock if u need to wait someone to release it
    - sometimes, it create unstable case, like a p95/p99 case that run slow and it slow down/block all other to wait it to release the lock


- predicate lock 斷言鎖
    - to lock object(s) matching certain condition
        - this is to solve write skew/phantom read issue (1 tx change another tx search result)

say we want to book a meeting room with below query
```sql
SELECT * FROM bookings
WHERE room_id = 123 
AND end_time > '2018-01-01 12:00' 
AND start_time < '2018-01-01 13:00';
```
- 我們可以用斷言鎖來 block 所有的 object(records) as long as this object 滿足上面這個 where condition
- if tx1 want to get the any object that match the where condition,  and if tx0 already get the predicate lock for those object (matching condition), tx1 need to wait
- even tx3 want to create a object which will be fall into these condition, tx3 need to wait as well
- this is to solve phantom read -> 還記得 phantom read  又叫做 condition break, 現在我們有 lock for this XD


但是如果斷言鎖 block 一堆 record 且又很 active, 那會有很大的 perf 問題

根據上面的例子，我們要找這三個 condition
```sql
room_id = 123
eend_time > '2018-01-01 12:00'
sstart_time < '2018-01-01 13:00'
```

我們只鎖第一個 condition, 因此是 room_id, which 可以上 idx, 會很快
會不會因為我沒有 lock 另外兩個因此有 race condition..會, 但看機率
這是一個為了 perf 的 trade-off




### 序列化快照隔離 serializable snapshot isolation (SSI)

- 什麼是悲觀鎖和樂觀鎖 (pessimistic lock and optimistic lock)?
	- also see [[tx and lock]]
- 2PL is like 悲觀鎖, meaning, if there’s possible to have problem, we lock them and can’t do anything
- 樂觀鎖, we will let concurrent happen and even we know there’s something is writing to it, and **we only stop the commit when we know bad thing will happen**
- so optimize lock will need a mechanism/algo to check if there’s something bad will happen before commit



- how db know the query result (the user case of doctor on-call) is changed?
	- 偵測是否讀取MVCC Obj Version 中的過期數據 (the uncommitted write occur before the read)
    - 偵測會影響到之前讀取的寫入   (the write occur after the read)

例子
- 偵測是否讀取MVCC Obj Version 中的過期數據
	- 下面 tx 43, 會偵測到前提已經改變(因為 tx 動到一樣的 obj 而且改變了), 因此 tx43 最後會 abort
	- 不會一開始就 abort, 偵測到了才會 abort, hense 樂觀 lock
![[IMG-DDIA DB transaction 交易-20250810210640405.png]]


- 偵測會影響到之前讀取的寫入
	- db 可以用上面提到的 id-range lock(b-tree idx) 很快知道 idx 1234 被 哪些 tx access(here, tx42 and tx43)
	- 當 tx 42 最後進行 寫入, tx manager 會 讓其他 tx 也在這個 idx 上的 都 abort
![[IMG-DDIA DB transaction 交易-20250810210641544.png|848]]

- the advantage of SSI is that we don’t block concurrent from the begin, we abort if the result could be bad
- **this make read is way faster, since read is not effect the premise as mentioned and abort other**
- 如果一天到晚都在 abort, 那也是會影響 SSI

> For example, a transaction that reads and writes data over a long period of time is likely to run into conflicts and abort, so SSI requires that read-write transactions be fairly short (long- running read-only transactions may be okay).


- 總的來說，對於慢tx 的影響, SSI 還是比 2PL and actual series execution 還比較不敏感的。
- see Postgres impl: [SSI - PostgreSQL wiki](https://wiki.postgresql.org/wiki/SSI)


如果要在 typeorm 使用 SSI , 使用 `SERIALIZABLE`

ref: [Transactions | typeorm](https://orkhan.gitbook.io/typeorm/docs/transactions#specifying-isolation-levels)

```js fold


await myDataSource.manager.transaction(
    "SERIALIZABLE",  // `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ`, `SERIALIZABLE`
    (transactionalEntityManager) => {},
)

```


