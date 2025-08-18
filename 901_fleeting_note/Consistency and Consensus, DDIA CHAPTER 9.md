---
aliases:
  - ddia consistent, ddia consensus
---


> **The best way of building fault-tolerant systems is to find some general-purpose abstractions with useful guarantees, implement them once, and then let applications rely on those guarantees**. This is the same approach as we used with transactions in Chapter 7: by using a transaction, the application can pretend that there are no crashes (atomicity), that nobody else is concurrently accessing the database (isolation), and that storage devices are perfectly reliable (durability). Even though crashes, race conditions, and disk failures do occur, the transaction abstraction hides those problems so that the application doesn’t need to worry about them.



> For example, one of the most important abstractions for distributed systems is **consensus**: that is, getting all of the nodes to agree on something. As we shall see in this chapter, reliably reaching consensus in spite of network faults and process failures is a surprisingly tricky problem.


如果沒有這個共識，say 你的 db 如果掛了，那要推舉哪一個 new node 出來當 leader, 如果大家有一個共識，那就簡單了。



### Consistency Guarantees

> Most replicated databases provide at least eventual consistency, which means that if you stop writing to the database and wait for some unspecified length of time, then eventually all read requests will return the same value.


> When working with a database that provides only weak guarantees (這裡指 eventual consistency), you need to be constantly aware of its limitations and not accidentally assume too much. Bugs are often subtle and hard to find by testing, because the application may work well most of the time. The edge cases of eventual consistency only become apparent when there is a fault in the system (e.g., a network interruption) or at high concurrency.


下面這段我也覺得容易搞混..作者也特別提到，幾乎都算是不同的議題
> There is some similarity between **distributed consistency models** and the **hierarchy of transaction isolation levels** we discussed previously [4, 5] (see “Weak Isolation Levels” on page 233). But while there is some overlap, they are mostly independent concerns: transaction isolation is primarily about avoiding race conditions due to concurrently executing transactions, whereas distributed consistency is mostly about coordinating the state of replicas in the face of delays and faults.


這章的重點如下：
> - We will start by looking at one of the strongest consistency models in common use, **linearizability**, and examine its pros and cons.
> - We’ll then examine the issue of ordering events in a distributed system (“**Ordering Guarantees**” on page 339), particularly around causality and total ordering.
> - In the third section (“Distributed Transactions and Consensus” on page 352) we will explore how to **atomically commit a distributed transaction**, which will finally lead us toward solutions for the consensus problem.
    


### Linearizability 線性一致性

> This is the idea behind linearizability [6] (also known as atomic consistency [7], strong consistency, immediate consistency, or external consistency [8]).
> the basic idea is to make a system appear as if there were only one copy of the data, and all operations on it are atomic. With this guarantee, even though there may be multiple replicas in reality, the application does not need to worry about them.



Alice 讀的 follower1 已經可以拿到比賽結果，但 Bob讀的 follower2, 資料還沒 replicate 過去
![[IMG-Consistency and Consensus, DDIA CHAPTER 9-20250810200232857.png|795]]


### 什麼系統才叫做線性一致?
What Makes a System Linearizable?


下圖， write 前的 read  (get 0), and write 後的 read (get 1)的結果都是確定的
但是 中間三個read, 因為和 write 'cocurrent' -> 因此結果就是 0 or 1
![[IMG-Consistency and Consensus, DDIA CHAPTER 9-20250810200233566.png]]




下圖符合 Linearizable
> In a linearizable system we imagine that there must be some point in time (between the start and end of the write operation) at which the value of x atomically flips from 0 to 1. Thus, if one client’s read returns the new value 1, all subsequent reads must also return the new value, even if the write operation has not yet completed.

就算 write 操作 is still undergoing, 我們還是一定得假設在一個時間點後，就會讀到 1, 然後後續的操作，就算來自其他客戶，都一定要讀到 1
![[IMG-Consistency and Consensus, DDIA CHAPTER 9-20250810200234593.png]]


我們來看一個更複雜的圖


其他的 `cas(x, v_old, v_new) ⇒ r` 表示 compare and set 運算。 如果 x === v_old, x 會被 set to v_new, if x !== v_old -> throw err

圖中的 垂直線，用來表示執行的順序, 類似一開始的順序是 D write -> A write -> B read -> C read -> ...

> The requirement of linearizability is that the lines joining up the operation markers always move forward in time (from left to right), never backward.

![[IMG-Consistency and Consensus, DDIA CHAPTER 9-20250810200235857.png]]

上圖， D 寫入 0, then A 寫入 1, 所以 B 讀到 1， D->A->B 跟 req sent 的順序不同，但這是一個 valid order

另外這個模型不提供 交易隔離(transaction isolation)保護。譬如， C 第一次讀到1, 後續讀到2, 因為 B 把值改為2。 我們可以 CAS (An atomic compare-and-set operation) 來保護：類似 B 讀到1, 後續他進行 CAS(x, 1,2), 因為他讀到還是1, 因此成功改為2。但是 D 他一開始把 X 改為 0 後，後續進行 CAS(x, 0,3), 因此 已經不是 0，因此噴錯。

上圖最後 B 的 read x 為 2 就不是 linearizable。這個 B 的讀和 C 最後的 CAS(x, 2,4) 重疊，另外 A 已經讀到 4, 且 B 的讀在 A的讀的後面因此 B 應該需要是4 而不是2，因此違反 linearizable。

also see:[[serializability (可串行性 )vs linearizability (線性一致性)]]


### 線性一致保證的用途
Relying on Linearizability 

主要是在說，我們有哪些東西需要使用這個保證

#### **主從複製 (single-leader replication)**

> A system that uses single-leader replication needs to ensure that there is indeed only one leader, not several (split brain). One way of electing a leader is to use a lock: every node that starts up tries to acquire the lock, and the one that succeeds becomes the leader [14]. No matter how this lock is implemented, it must be linearizable: all nodes must agree which node owns the lock; otherwise it is useless.


Coordination services like Apache ZooKeeper [15] and etcd [16] are often used to implement distributed locks and leader election. They use consensus algorithms to implement linearizable operations in a fault-tolerant way (we discuss such algorithms later in this chapter, in “Fault-Tolerant Consensus” on page 364).iii There are still many subtle details to implementing locks and leader election correctly (see for example the fencing issue in “The leader and the lock” on page 301), and libraries like Apache Curator [17] help by providing higher-level recipes on top of ZooKeeper.


#### 約束和唯一性保證(Constraints and uniqueness guarantees)

> If you want to enforce this constraint as the data is written (such that if two people try to concurrently create a user or a file with the same name, one of them will be returned an error), you need linearizability


bank a/c 不會變成複數，不會兩個人一起買到同一個位子，你不會超賣..等等
> Similar issues arise if you want to ensure that a bank account balance never goes negative, or that you don’t sell more items than you have in stock in the warehouse, or that two people don’t concurrently book the same seat on a flight or in a theater. These constraints all require there to be a single up-to-date value (the account bal‐ ance, the stock level, the seat occupancy) that all nodes agree on.


#### Cross-channel timing dependencies


下圖，image resizer 和 web server 通過兩個方式溝通(file storage and queue)，因此就有了race condition
類似 queue 的 msg 到了，然後 image resizer 去拿還是舊的 image, 或是還沒到

如果有Linearizability 保證，那就有了順序性，就可以避免。
![[IMG-Consistency and Consensus, DDIA CHAPTER 9-20250810200237117.png|756]]


### 實作線性一致系統
Implementing Linearizable Systems

> Since linearizability essentially means “behave as though there is only a single copy of the data, and all operations on it are atomic,” the simplest answer would be to really only use a single copy of the data.

但是我們要考慮 fault tolerate -> 因此就需要某種 replicate 機制

下面我們來 review replica 機制：

> Single-leader replication (potentially linearizable)  (see “Leaders and Followers” on page 152)

If you make reads from the leader, or from synchronously updated followers, they have the potential to be linearizable.

Using the leader for reads relies on the assumption that you know for sure who the leader is. As discussed in “The Truth Is Defined by the Majority” on page 300, it is quite possible for a node to think that it is the leader, when in fact it is not -> this will violate linearizability

With asynchronous replication, failover may even lose com‐ mitted writes (see “Handling Node Outages” on page 156), which violates both durability and linearizability.


Consensus algorithms (linearizable)
Some consensus algorithms, which we will discuss later in this chapter, bear a resemblance to single-leader replication. However, consensus protocols contain measures to prevent split brain and stale replicas. Thanks to these details, con‐ sensus algorithms can implement linearizable storage safely. This is how Zoo‐ Keeper [21] and etcd [22] work, for example.


Multi-leader replication (not linearizable)
Systems with multi-leader replication are generally not linearizable, because they concurrently process writes on multiple nodes and asynchronously replicate them to other nodes. For this reason, they can produce conflicting writes that require resolution (see “Handling Write Conflicts” on page 171). Such conflicts are an artifact of the lack of a single copy of the data.


Leaderless replication (probably not linearizable)

For systems with leaderless replication (Dynamo-style; see “Leaderless Replication” on page 177), people sometimes claim that you can obtain “strong consistency” by requiring quorum reads and writes (w + r > n). Depending on the exact configuration of the quorums, and depending on how you define strong consistency, , this is not quite true.

“Last write wins” conflict resolution methods based on time-of-day clocks (e.g., in Cassandra; see “Relying on Synchronized Clocks” on page 291) are almost certainly nonlinearizable, because clock timestamps cannot be guaranteed to be consistent with actual event ordering due to clock skew. Sloppy quorums (“Sloppy Quorums and Hinted Handoff” on page 183) also ruin any chance of linearizability. Even with strict quorums, nonlinearizable behavior is possible, as demonstrated in the next section.



下圖， w+r > n, 是 strcit quorum
(w is 3, 3 ack for write, r is 2, 2 ack for read, n is 3 node)

writer 把x 改為 1
and send to 3 replica, replica 3 的 ack 是最快
在 replica1,2,3的寫入, ack 的過程中, concurrently, reader A, B 進行 read
A read 2 node -> get 1
B read also 2 node, get 0
既然 A 已經 read 1 , 後面的 B 確還是讀到 0
-> 就算是 strcit quorum, 還是會違背線性一致
![[IMG-Consistency and Consensus, DDIA CHAPTER 9-20250810200238182.png]]


PS: 還是可以透過 犧牲 perf 達到，但是一些 db design 決定不這樣做
> Interestingly, it is possible to make Dynamo-style quorums linearizable at the cost of reduced performance: a reader must perform read repair (see “Read repair and anti- entropy” on page 178) synchronously, before returning results to the application [23], and a writer must read the latest state of a quorum of nodes before sending its writes [24, 25]. However, Riak does not perform synchronous read repair due to the performance penalty [26]. Cassandra does wait for read repair to complete on quorum reads [27], but it loses linearizability if there are multiple concurrent writes to the same key, due to its use of last-write-wins conflict resolution.


### 線性一致的成本 
The Cost of Linearizability



多資料中心的部屬:

多領導者的 db 模型 (multi-leader database), 如果彼此的網路中斷，那因為每個 資料中心有自己的主 db, 因此線性一致還是可以確保。(兩者資料無法 sync 的問題，可以先 pass to queue, and sync later)

但如果是 single-leader db, 網路中斷的話，線性一致就一定會違背，因為沒有連到主 db 的那個 資料中心就無法寫入了。
![[IMG-Consistency and Consensus, DDIA CHAPTER 9-20250810200239203.png|716]]

這其實也不只是有關於 多資料中心的部屬問題
這本質就是 CAP 理論:

如果你的應用想要線性一致(linearizability)，但是 replica 彼此之間斷線了，你只能選擇等斷線回來(放棄分區容忍)，或是噴錯(放棄一致)

如果你的應用程式不需要線性一致(linearizability), 那你可以讓你的每一個replica 處理 request 獨立運作，就算是網路中斷也可運作(選擇分區容忍)，因此，如果網路中斷，你還是可以提供服務(availability), 但是你就放棄了 線性一致(linearizability)

- see [[分布式系統 CAP]]




> Although linearizability is a useful guarantee, surprisingly few systems are actually linearizable in practice.


譬如現代 CPU 的 thread 跟 memory 的運作就沒有線性一致。
> if a thread running on one CPU core writes to a memory address, and a thread on another CPU core reads the same address shortly afterward, it is not guaranteed to read the value written by the first thread (unless a memory barrier or fence [44] is used).

Why?因為速度很重要! (so this is not about trade-off b/n available and consistency)
> The reason for this behavior is that every CPU core has its own memory cache and store buffer. Memory access first goes to the cache by default, and any changes are asynchronously written out to main memory. Since accessing data in the cache is much faster than going to main memory [45], this feature is essential for good performance on modern CPUs.


很多 db 選擇放棄線性一致性，也是為了 perf, 而不完全是為了 fault-tolerance
要確保線性一致性，整個機制都會比較慢。


有辦法找到快速的線性一致性算法嗎? 可能無法。
有學者證明，線性一致性算法的速度隨著網路不穩定度的提高而提高，而我們基本上很難控制網路的中斷。

So, A faster algorithm for linearizability does not exist, but weaker consistency models can be much faster, so this trade-off is important for latency-sensitive systems.



## Ordering Guarantees 順序保證

> Let’s recap other contexts in which we have discussed ordering:


1. single-leader replication 中的 leader 起到了決定 order of writes in the replication log 的作用
2. 交易的串行性(transaction Serializability)保證，確保了執行順序依照"某個順序"進行，可能是透過真的依照順序跑，還是透過 lock or abort 來確保
3. The use of timestamps and clocks in distributed systems that we discussed in Chapter 8 (see “Relying on Synchronized Clocks” on page 291) 也是另一個讓我們確認順序性，到底哪一個 write 才是第一個



### Ordering and Causality  因果一致


> Ordering helps preserve **causality**.



我們來看看本書的一些例子

> In “Consistent Prefix Reads” on page 165 (Figure 5-5) we saw an example where the observer of a conversation saw first the answer to a question, and then the question being answered. We say that there is a causal dependency between the question and the answer.


> A similar pattern appeared in Figure 5-9, where we looked at the replication between three leaders and noticed that some writes could “overtake” others due to network delays. Causality here means that a row must first be created before it can be updated.


> In “Detecting Concurrent Writes” on page 184 we observed that if you have two operations A and B, there are three possibilities: either A happened before B, or B happened before A, or A and B are concurrent. This happened before relationship is another expression of causality


> In the context of snapshot isolation for transactions (“Snapshot Isolation and Repeatable Read” on page 237), we said that a transaction reads from a consistent snapshot. But what does “consistent” mean in this context? It means consistent with causality


>  “Write Skew and Phan‐ toms” on page 246) also demonstrated causal dependencies: in Figure 7-8, Alice was allowed to go off call because the transaction thought that Bob was still on call, and vice versa.


> In the example of Alice and Bob watching football (Figure 9-1), Alice’s exclamation is causally dependent on the announcement of the score, so Bob should also be able to see the score after hearing Alice. The same pattern appeared again in “Cross-channel timing dependencies” on page 331 in the guise of an image resizing service.


因此，我們可以說：
> Causality imposes an ordering on events: 
> cause comes before effect; 
> a message is sent before that message is received; 
> the question comes before the answer. 
> And, like in real life, one thing leads to another: one node reads some data and then writes some‐ thing as a result, another node reads the thing that was written and writes something else in turn, and so on. These chains of causally dependent operations define the causal order in the system—i.e., what happened before what.



如果一個系統符合因果性順序，那我們會說這個系統是 **因果一致的 causally consistent**

snapshot isolation 就有因果一致的保證：when you read from the database, and you see some piece of data, then you must also be able to see any data that causally precedes it.



**The causal order is not a total order**


what is total order?
給你兩個數，like 12, 3, 12>3, 可以比大小
自然數就是 total order

what is partial order
給你兩個 set  {a, b}, {b ,c}


Linearizability is total ordering ->  我們總是可以知道那個先，哪個後。

Causality 就是 partial order 
有時候可以區分前後
但是如果是 cocurrent 就無法


Git （分散版本控制系統） 就是 partial order
Git 圖就是一個 圖，呈現了 因果依賴
有時候， git 圖很簡單，前後明瞭
有時候，有多人 cocurrently work on a same feature and need to resolve conflict and merge。


**Linearizability is stronger than causal consistency**

> linearizability implies causality: any system that is linearizable will preserve causality correctly

如同上面的 queue and file storage 例子，因為有多個溝通管道，但如果系統是線性一致，那你就不用做任何事情，也可以確保 file storage 可以處理到最新的檔案。(o.w. you need to handle it)

線性一致讓系統的運行變得很簡單和直覺，但是成本確是效能或是可用性，因此很多系統選擇後者。

有中間路線可以選擇嗎?
有，使用**因果一致**

而且, 
> causal consistency is the strongest possible consistency model that does not slow down due to network delays, and remains available in the face of network failures



**Capturing causal dependencies**

我們只需要 partial order, 可以有 cocurrent ops and run in any order, 但是只要一個 op 比另一個先發生，那另一個 replica 也要是一樣的順序（不然就違背 因果一致）。

這個概念類似，如果一個 node 看到 value X, 當他要寫入 Y, 那 X and Y 就有因果關係了。

這概念跟我們在 “Detecting Concurrent Writes” on page 184 類似，where we need to detect concurrent writes to the same key in order to prevent lost updates. 

Causal consistency goes further: it needs to track causal dependencies across the entire database, not just for a single key. Version vectors can be generalized to do this [54].

因此， db 需要知道目前讀的資料的版本，in Figure 5-13，上一個操作的版號需要 pass 到後面，當 db 需要寫入時。A similar idea appears in the conflict detection of SSI, as discussed in “Serializable Snapshot Isola‐ tion (SSI)” on page 261: when a transaction wants to commit, the database checks whether the version of the data that it read is still up to date.


### 用序列號來排序 Sequence Number Ordering 

但是一個寫之前，可能有很多讀。然後要去 track 所有的 讀寫依賴，這個 overhead 可能太大...
有一個更好的辦法: we can use sequence numbers or timestamps to order events.

我們建立 seq num 來代表順序。如果 A 的 seq num < B, 那我們保證 A 就是比 B 發生在前面，那如果遇到 cocurrent, 那我們可能就 arbitrarily 認定一個order

db 的單領導(single leader) replication, replication log 就定義 寫入的 total order 符合 因果一致
因此 leader 把每一次的 寫操作都要 increment a counter, 然後如果 followe apply those write in order, 那 follower 的狀態就是因果一致的。



**Noncausal sequence number generators**

如果不是 singler leader, 是multiple leader, or leaderless, or paritioned case, 以下是常見建立 corss-node 的 increasing UID 生產方法：

1. 可以讓每個 node 自己生產獨立的 seq nums。如果兩個，一個odd, 一個 even。如果多個，就讓 seq number 裡面預留一些 bit 可以生產那個 node specific unique code, 以確保 seq number 不會撞到。
2. 使用加上足夠高精度的 timestamp。 This fact is used in the last write wins conflict resolution method (see “Timestamps for ordering events” on page 291).
3. 每一個 node 可以拿到一個給定的 seq number range/block. like node A 拿到 1~1000, nodeB 拿到 1000~2000, 如果用完了，有人會派發新的 range, 這樣也不會重複。

不過以上方法有個問題 -> the sequence numbers they generate are not consistent with causality

1. 一個 node生產 even, 一個 node 生產 odd, 但是如果生產的速度不同的話，你又如何確保那一個先哪一個後?
	- like 1,3,5,7,8,2,4 .... 因為 odd 生產的比較快，你的 2還在 8 後面
2. timestamp會受到 clock skew 問題影響。 see - see Figure 8-3, which shows a sce‐ nario in which an operation that happened causally later was actually assigned a lower timestamp
3. 因為每一個 node 處理的速度不同，可能 node A 拿到 3000-4000 後， node B 在被派發到到 5000-6000 但是 B 先處理。



**Lamport timestamps** - a simple method for generating sequence numbers that is consistent with causality


每一個 node 有自己的 unique identifier
每一個 node 都有一個 counter, 每一個 op 就會 +1
組成是一個 pair (counter, nodeID)

It provide total ordering: 如果兩個 node 有一樣的 counter, 大的 counter 就是比較大的，如果 counter 一樣，那麼大的 nodeID就是大的。


到這邊為止都跟上面的 case 1 一樣，不同的是：
每一個 node 和 client 都會 track 目前他看到的最大的 counter val，並且每一個 request 都會包括 這個 val
當一個 node 收到一個 counter val，如果大於 他自己目前最大 counter val -> 他會 update 自己的 couner val to that val


下圖， A 拿到 (counter=5, nodeID=2), 因此他自己的 max_counter update to 5, 然後下一輪 counter++ -> counter 就變成是 6
![[IMG-Consistency and Consensus, DDIA CHAPTER 9-20250810200240400.png|788]]

透過上面這個機制，我們把 max counter 傳遞 acorss nodes, 因此 Lamport timestamps is 因果一致。因果關係透過 這個 counter 傳遞下去。

Lamport timestamps 跟之前我們提到的 version vectors (in “Detecting Concurrent Writes” on page 184) 雖然很像，但不同。
> version vectors can distinguish whether two operations are concurrent or whether one is causally dependent on the other, whereas Lamport timestamps always enforce a total ordering. 


**Timestamp ordering is not sufficient**

雖然 lamport ts 可以提供因果一致，但還是會遇到一些分布系統的常見問題。


我們來舉一個常見的例子， user name 唯一性問題:

兩個人想要建立一樣的名字。

兩個 operaiton 是不同的 node 處理。

那如果你有 total order, 因果一致，知道哪個先，哪個後，不是可以處理嗎？你讓先的那個 win 就好。

問題是， lamport ts 的機制，你需要全部的 node 的資訊都"收集到了"，你才會知道那個先哪個後。

那你可能就需要等待很久，譬如 user 建立名字，你還要發 req 到其他所有的 node 都確認是否也有一樣的名字，然後如果又有network delay...那就更久了。

簡單說，上面的例子說明: 只有 total order of operations 不夠，你要做決定，要等到所有的 operation 都"完成"

那要如何解決呢? -> introduce  **total order broadcast**


### Total Order Broadcast 全序廣播


If your program runs only on a single CPU core, it is easy to define a total ordering of operations: it is simply the order in which they were executed by the CPU. However, in a distributed system, getting all nodes to agree on the same total ordering of operations is tricky. 

In the last section we discussed ordering by timestamps or sequence numbers, but found that it is not as powerful as single-leader replication (if you use timestamp ordering to implement a uniqueness constraint, you cannot tolerate any faults).


上面提到 lamport ts 遇到的問題。
那我們回到 sinlger-leader replication 的情況下，一個 node 當leader, 然後那個 leader 產生的 seq number 作為順序。
那我們有兩個問題
一個就是如果我們要handle 的量大於一個node 可以 handle的要怎麼辦?
另一個是如果 spof, how to handle failover?

分布系統中，這個問題叫做 total order broadcast or atomic broadcast


基本上就是透過一個 protocol

這個protocol is about how node exchanging msgs

這個算法需要滿足兩個條件

- Reliable delivery
	- 沒有 msg lost
	- 如果有msg 送出去給一個node, all node 也都要收到
- Totally ordered delivery
	- 大家，每一個 node 收到的順序都要一樣。


就算是 node or network fail，以上條件也要成立。
當然如果有 node or network fail，自然就暫時無法達到，那算法需要有某種 retry 機制讓這個條件最終也要成立。


**使用全序廣播**
**Using total order broadcast**

Consensus services,像是 ZooKeeper, etcd 都有實作 total order broadcast 全序廣播。（Consensus and total order broadcast 之間的關聯後續要提到）


全序廣播 就是我們 db replication 需要的。每一個 msg 表示一個 db 寫入，每一個 replica process 寫入都是一樣的順序，那所有的 replicas 就會保持一致性。(又叫做 state machine replication [60], and we will return to it in Chapter 11)


全序廣播也可以用來實作串行交易(serializable transactions), as discussed in “Actual Serial Execution” on page 252。
>  if every message represents a deterministic transaction to be executed as a stored procedure, and if every node processes those messages in the same order, then the partitions and replicas of the database are kept consistent with each other [61].


一個關鍵是，全序廣播的順序，在 msg 要傳的時候，就固定了。因此，一個 node 是不可以回朔插入一個 msg 在更之前的位置(之前於其他已經傳遞出去的 msg)。


另一個角度去看 全序廣播，可以幫他看成是一種生成 log 的方式。  (as in a replication log, transaction log, or write-ahead log)


也常拿來實作 lock 服務，提供 fencing tokens (see “Fencing tokens” on page 303). 
每一個 req 可以透過 append a msg to the append-only log 來 get lock, all msg 都是有序排列在 log 中。
這個 seq number 可以拿來做為 fencing token, 因為是單調遞增的。 In ZooKeeper, this sequence number is called zxid [15].



**用全序廣播來實作線型儲存**
**Implementing linearizable storage using total order broadcast**



那 linearizable 和 全序廣播的關係?

不太一樣：
全序廣播是非同步的，msg 被保證其傳遞的順序是固定的，但是沒有保證何時會收到(因為可能有接收者 lag)
而線性一致是 "最近保證" (recency guarantee),  a read 可以確保總是可以看到最新的 write

不過，一旦你有了 全序廣播 (total order brodcast), 你就可以用它來建立一個 線性一致的儲存，然後以此來確保 上面要解決的 user name 唯一性問題。


想像你一個 log, 然後有一個 compare-and-set CAS操作。
一開始是 null
當一個使用者想要建立 username, 你執行 CAS 在那個 log 上，只有 log val is null 才會 押上 userId

如果有很多 user 同時都想要建立一樣的 username, 因為 CAS, 那也只會有最快的那個會成功

具體來說:
1. append a msg on log, 並押上你要的 username
2. 讀取 log, 然後等待 log 回傳 msg 給你
3. 收到 msg 後，如果第一個收到的 msg 是你自己的, 那你就成功了，可以commit，如果你收到的 msg 不是你自己的 msg, 是其他人的 msg, 表示你失敗了， abort。

因為 log 傳給所有的 node 的 order 都會一樣(assume 全序廣播 in place)，因此如果有很多cocurrent寫入，大家都會用一樣的順序收到，因此大家也都會一致的同意是哪一個 node 成功寫入，剩下沒有成功的就需要 abort. 
我們也可以用類似的方法在這個log之上去實作 serializable multi-object transactions。


上面這個方法保證線性寫，但沒有保證線性讀。 
因為如果你讀的資料來自於一個 store, 那這個 store 是非同步去拿到這個 log 的資料的話，那你讀的資料就過期了。


While this procedure ensures linearizable writes, it doesn’t guarantee linearizable reads — if you read from a store that is asynchronously updated from the log, it may be stale. (To be precise, the procedure described here provides sequential consistency [47, 64], sometimes also known as timeline consistency [65, 66], a slightly weaker guarantee than linearizability.) 



要達到線性讀，下面有幾個方法：

- You can sequence reads through the log by appending a message, reading the log, and performing the actual read when the message is delivered back to you. The message’s position in the log thus defines the point in time at which the read happens. (Quorum reads in etcd work somewhat like this [16].)
    
- If the log allows you to fetch the position of the latest log message in a linearizable way, you can query that position, wait for all entries up to that position to be delivered to you, and then perform the read. (This is the idea behind ZooKeeper’s sync() operation [15].)

- 讓你讀的來源/replica 是用同步的方式。就是資料寫到 log, 你要等到資料讀回來，整個才會一個 operation. This technique is used in chain replication [63]; see also “Research on Replication” on page 155.)



**用線型儲存來實作全序廣播**   
**Implementing total order broadcast using linearizable storage**


我們也可以反過來，如果我們有了 線型儲存，如何去實作全序廣播


我們可以假設，我們有一個線型儲存, 裡面有 int, 然後有一個 原子性的  increment-and-get operation [28].
也可以是一個原子性的 compare-and-set operation


算法很簡單：你要傳出去的每一個 msg, 你用 "原子性的  increment-and-get operation" 拿到一個 int, 把這個 int 作為 msg 的 序號(seq number)。
然後你就把這個 msg 傳給 all node(resend if msg lost), 然後接受的 nodes 使用這個序號當作為 msg 的 傳遞順序 and deliver to client。

你從  線型儲存 拿到的 seq no 不會有 "gap"，這點不同於  Lamport timestamps
因此如果一個 node 傳了 msg4, 然後他拿到了 msg6, 他會知道他需要等 msg5傳出去才對。



那麼，我如果要製造一個線型儲存和 atomic increment-and-get operation 容易嗎?
如果網路/node都不會出問題，那你只需要在一個 node 上 用一個 var 去產即可。 but this is not the real world XD

最後都會回到 共識相關的算法。

已經有學者證明，a linearizable compare-and-set (or increment-and-get) register  和   total order broadcast  的問題， are both equivalent to consensus [28, 67] 問題。 因此如果你解了其中一個，you can transform it into a solution for the others!

下面，我們會開始看看 共識問題。


## Distributed Transactions and Consensus


Consensus is one of the most important and fundamental problems in distributed computing.


目標就是讓 serveral nodes 都 agree on somthing


為何需要分散事務?

Leader election  領導者選舉
failover 需要 followers 對於 leader 是那一個需要"都"同意

> **In a database with single-leader replication, all nodes need to agree on which node is the leader.** The leadership position might become contested if some nodes can’t communicate with others due to a network fault. In this case, **consensus is important to avoid a bad failover**, resulting in a split brain situation in which two nodes both believe themselves to be the leader (see “Handling Node Outages” on page 156). If there were two leaders, they would both accept writes and their data would diverge, leading to inconsistency and data loss.



跨 node, partition 的 原子提交 

如果成功或失敗，每一個 node/partition 需要大家"都"同意


> In a database that **supports transactions spanning several nodes or partitions**, we have the problem that a transaction may fail on some nodes but succeed on others. If we want to maintain transaction atomicity (in the sense of ACID; see “Atomicity” on page 223), **we have to get all nodes to agree on the outcome of the transaction: either they all abort/roll back (if anything goes wrong) or they all commit (if nothing goes wrong).** This instance of consensus is known as the atomic commit problem 


In this section we will first examine the atomic commit problem in more detail. 


我們先介紹 two-phase commit (2PC) algorithm 很常見也常用，但是不是夠好。 [70, 71].

我們會介紹更好的共識算法(consensus algorithms), , such as those used in ZooKeeper (Zab) and etcd (Raft).



### 2階段提交 Atomic Commit and Two-Phase Commit (2PC)



tx 我們需要全有或是全無，這是很重要的保證。

對於  multi-object transactions (see “Single-Object and Multi-Object Operations” on page 228) 和 需要維護 secondary indexes 的 db 更是重要。


可以回去看 secondary idx 那章，如果你 update  pk 的資料，很多對應的 sk 都需要在不同的 node/parition 去 update, 確保資料全部都成功，或是全部都失敗很重要 
see [[partition#Partitioning and Secondary Indexes]]

我們需要一個分布系統下的原子提交


**From single-node to distributed atomic commit**



單點的 tx 的 原子性，透過 db engine 的 WAL 搞定。
如果中途失敗的話
- 如果資料都已經寫入 WAL(in disk, durable) -> 表示 commited -> 可以用WAL 重建
- 如果還沒  -> 也還沒 commit -> abort, 回到原本寫入前。



通常資料全部都寫入 disk, 還會有一個 提交記錄 commit record [72].
一旦這個提交記錄寫入 disk -> 算提交。 
就算 crash 也算 commit
o.w. 算還沒提交，如果 crash -> 會 abort



那如果遇到需要多 node 進行提交怎半?
一些 noSQL db 沒有提供這個功能
但是一些集群關連性 db有提供 (see blow “Distributed Transactions in Practice”).




如果我們讓 node成功就可以commit, 因為很多原因(網路暫時中斷，node暫時down, 或是timout導致問等等), 會發生有些 node commit 成功，有些失敗的情況。這樣資料就不一致了。

因此我們一定得，當一個 node 要 commit 時，一定得大家都要一起 commit 才行。





一旦提交，就不可以撤銷。
這是基本原則。

因為如果你提交後又撤銷，那其他tx使用你交易後的資料繼續去進行操作怎麼辦?

這也是交易的基礎，就像是我們提到的 “Read Committed” 保證: no dirty read/write.
就是只要提交，才可以讀和寫。
因此就是提交後的資料就是可以安全使用的。


PS: It is possible for the effects of a committed transaction to later be undone by another, compensating transaction [73, 74]. However, from the database’s point of view this is a separate transaction, and thus any cross-transaction correctness requirements are the application’s problem.



**Introduction to two-phase commit**

PS: Two-phase commit (2PC) and two-phase locking (see “Two-Phase Locking (2PL)” see [[DDIA DB transaction 交易]]) 是完全沒關連的，不要搞混。


二階段提交是一個分散提交的經典算法 [13, 35, 75]
這個算法確保 all node 都一起 commit or abort


有些 DB 內部會實作
不過也可能會以 XA 交易 [76, 77] (which are supported by the Java Transaction API, for example) 的形式在 應用層使用
或是 SOAP web services [78, 79] 提供的 WS-AtomicTransaction


見下圖。
顧名思義，就是分兩個階段。
![[IMG-Consistency and Consensus, DDIA CHAPTER 9-20250810200241626.png|881]]


基本上會透過一個管理者 or 協調者 (also known as transaction manager). 
這個協調者可以是 lib 被使用在 app 中，當要發起tx 時可以用它。(e.g., embedded in a Java EE container)
也可以是一個另一個分開的 程序或是服務 (Examples of such coordinators include Narayana, JOTM, BTM, or MSDTC.)

流程是這樣：

階段1:
當 app 開始要 提交時，協調者會 send req 問 all nodes, 大家是否都 okay

階段2:
if 大家都 okay -> 那協調者會 send req to let every node 說可以，大家就提交。
如果 anyone 說 no ->  那協調者會 send req to let every node 說大家需要 abort.



**A system of promises**

更細部的流程如下：

當 app 開始進行 2pc, 大家都需要跟協調者要一個 uniqe 的 tx_id,


大家可以用這個tx_id 去進行一個 node 內的讀寫交易。
如果這時候有req timeout or node crash -> 那協調者會知道，那就大家都會 abort


如果有一個交易最後要提交，協調者會 send all nodes "準備req" (with tx_id)
如果這些準備req fail or timeout -> 那 協調者會 send all nodes "abort req"


當ndoe 收到 "準備req", node 要做好所有提交的準備 (a crash, a power failure, or running out of disk space is not an acceptable excuse for refusing to commit later), 如果等等提交，那一定可以有辦法提交 (類似已經把 all data write into WAL disk), 然後再回給協調者 yes


這時候node還沒提交


當協調者收到 大家都回給他 yes, 這時候他就下決定說要提交了，他會把這個決定也寫入他的 tx_log (in disk), 這時候也叫做 commit point



因為這個 commit point 下了決定，不可以反悔喔。
這時候 協調者會 send all 提交req 給 all node, 請大家提交 
這個時候如果協調者掛了，提交req timeout 等等，都會一直retry到成功。
（決定已經寫入 tx_log 因此如果crash, 起來後又需要從那邊去拿來進行動作）


node 也是一樣，一旦收到了，就需要進行 commit,如果 crash, 也會因為已經寫持久log可以 retry



==



整個協議的重點在於兩個 “points of no return”

一個是每個 node 會 vote yes, -> 這時候已經寫入自己的 LOG, 儘管協調者不一定會 send yes req
另一個是協調者的 yes



**Coordinator failure**

我們假設協調者送出 prep req, 然後 all node say yes

這時候 協調者送出 commit-req 給 db2, 但是還沒送給 db1時， 協調者 crash

這時候 db1只能等

如下
![[IMG-Consistency and Consensus, DDIA CHAPTER 9-20250810200243024.png|863]]

db1 可以問db2??

ㄟ，這不是 2pc 協議的設計。

這時候， db1只能等 協調者活起來，讀他已經存起來的tx_log, 然後回到原本狀態，補送 db1 req

因此這就是上面提到的， 一旦協調者決定大家要 commit or not, 第一個事情就是存到log 


**Three-phase commit is blocking atomic commit **

這也是為何有人把 2pc 叫做  blocking 原子提交

> In theory, it is possible to make an atomic commit protocol nonblocking, so that it does not get stuck if a node fails. However, making this work in practice is not so straightforward.


有另一個算法叫做 3PC [13, 80], 是 nonblocking 提交。
不過 3PC 假設 網路delay 和 req timeout 都是 bounded, 這跟現實不太一樣, 所以更多是理論討論。
簡單來說，nonblocking 提交，需要一個100%正確的錯誤node失敗偵測器，這個偵測器又需要 bounded 架設，所以大家都還是用 2pc

### 分散式交易的實踐  Distributed Transactions in Practice


Distributed transactions, especially those implemented with two-phase commit, have a mixed reputation. On the one hand, they are seen as providing an important safety guarantee that would be hard to achieve otherwise; on the other hand, they are criti‐ cized for causing operational problems, killing performance, and promising more than they can deliver [81, 82, 83, 84]. Many cloud services choose not to implement distributed transactions due to the operational problems they engender [85, 86].

Some implementations of distributed transactions carry a heavy performance penalty —for example, distributed transactions in MySQL are reported to be over 10 times slower than single-node transactions [87], so it is not surprising when people advise against using them. Much of the performance cost inherent in two-phase commit is due to the additional disk forcing (fsync) that is required for crash recovery [88], and the additional network round-trips.

However, rather than dismissing distributed transactions outright, we should exam‐ ine them in some more detail, because there are important lessons to be learned from them. To begin, we should be precise about what we mean by “distributed transac‐ tions.” Two quite different types of distributed transactions are often conflated:


Database-internal distributed transactions

Some distributed databases (i.e., databases that use replication and partitioning in their standard configuration) support internal transactions among the nodes of that database. For example, VoltDB and MySQL Cluster’s NDB storage engine have such internal transaction support. In this case, all the nodes participating in the transaction are running the same database software.

Heterogeneous distributed transactions  
In a heterogeneous transaction, the participants are two or more different tech‐ nologies: for example, two databases from different vendors, or even non- database systems such as message brokers. A distributed transaction across these systems must ensure atomic commit, even though the systems may be entirely different under the hood.

Database-internal transactions do not have to be compatible with any other system, so they can use any protocol and apply optimizations specific to that particular tech‐ nology. For that reason, database-internal distributed transactions can often work quite well. On the other hand, transactions spanning heterogeneous technologies are a lot more challenging.


**Exactly-once message processing**

Heterogeneous distributed transactions allow diverse systems to be integrated in powerful ways. For example, a message from a message queue can be acknowledged as processed if and only if the database transaction for processing the message was successfully committed. This is implemented by atomically committing the message acknowledgment and the database writes in a single transaction. With distributed transaction support, this is possible, even if the message broker and the database are two unrelated technologies running on different machines.

If either the message delivery or the database transaction fails, both are aborted, and so the message broker may safely redeliver the message later. Thus, by atomically committing the message and the side effects of its processing, we can ensure that the message is effectively processed exactly once, even if it required a few retries before it succeeded. The abort discards any side effects of the partially completed transaction.

Such a distributed transaction is only possible if all systems affected by the transac‐ tion are able to use the same atomic commit protocol, however. For example, say a side effect of processing a message is to send an email, and the email server does not support two-phase commit: it could happen that the email is sent two or more times if message processing fails and is retried. But if all side effects of processing a message are rolled back on transaction abort, then the processing step can safely be retried as if nothing had happened.

We will return to the topic of exactly-once message processing in Chapter 11. Let’s look first at the atomic commit protocol that allows such heterogeneous distributed transactions.


**XA transactions**


X/Open XA (short for eXtended Architecture) is a standard for implementing two- phase commit across heterogeneous technologies [76, 77]. It was introduced in 1991 and has been widely implemented: XA is supported by many traditional relational databases (including PostgreSQL, MySQL, DB2, SQL Server, and Oracle) and mes‐ sage brokers (including ActiveMQ, HornetQ, MSMQ, and IBM MQ).

XA is not a network protocol—it is merely a C API for interfacing with a transaction coordinator. Bindings for this API exist in other languages; for example, in the world of Java EE applications, XA transactions are implemented using the Java Transaction API (JTA), which in turn is supported by many drivers for databases using Java Data‐ base Connectivity (JDBC) and drivers for message brokers using the Java Message Service (JMS) APIs.

XA assumes that your application uses a network driver or client library to commu‐ nicate with the participant databases or messaging services. If the driver supports XA, that means it calls the XA API to find out whether an operation should be part of a distributed transaction—and if so, it sends the necessary information to the database server. The driver also exposes callbacks through which the coordinator can ask the participant to prepare, commit, or abort.


The transaction coordinator implements the XA API. The standard does not specify how it should be implemented, but in practice the coordinator is often simply a library that is loaded into the same process as the application issuing the transaction (not a separate service). It keeps track of the participants in a transaction, collects partipants’ responses after asking them to prepare (via a callback into the driver), and uses a log on the local disk to keep track of the commit/abort decision for each trans‐ action.

If the application process crashes, or the machine on which the application is running dies, the coordinator goes with it. Any participants with prepared but uncommitted transactions are then stuck in doubt. Since the coordinator’s log is on the application server’s local disk, that server must be restarted, and the coordinator library must read the log to recover the commit/abort outcome of each transaction. Only then can the coordinator use the database driver’s XA callbacks to ask participants to commit or abort, as appropriate. The database server cannot contact the coordinator directly, since all communication must go via its client library.


**Holding locks while in doubt**

Why do we care so much about a transaction being stuck in doubt? Can’t the rest of the system just get on with its work, and ignore the in-doubt transaction that will be cleaned up eventually?

The problem is with locking. As discussed in “Read Committed” on page 234, data‐ base transactions usually take a row-level exclusive lock on any rows they modify, to prevent dirty writes. In addition, if you want serializable isolation, a database using two-phase locking would also have to take a shared lock on any rows read by the transaction (see “Two-Phase Locking (2PL)” on page 257).

The database cannot release those locks until the transaction commits or aborts (illustrated as a shaded area in Figure 9-9). Therefore, when using two-phase commit, a transaction must hold onto the locks throughout the time it is in doubt. If the coor‐ dinator has crashed and takes 20 minutes to start up again, those locks will be held for 20 minutes. If the coordinator’s log is entirely lost for some reason, those locks will be held forever—or at least until the situation is manually resolved by an admin‐ istrator.

While those locks are held, no other transaction can modify those rows. Depending on the database, other transactions may even be blocked from reading those rows. Thus, other transactions cannot simply continue with their business—if they want to access that same data, they will be blocked. This can cause large parts of your applica‐ tion to become unavailable until the in-doubt transaction is resolved.


**Recovering from coordinator failure**

In theory, if the coordinator crashes and is restarted, it should cleanly recover its state from the log and resolve any in-doubt transactions. However, in practice, orphaned in-doubt transactions do occur [89, 90]—that is, transactions for which the coordina‐ tor cannot decide the outcome for whatever reason (e.g., because the transaction log has been lost or corrupted due to a software bug). These transactions cannot be resolved automatically, so they sit forever in the database, holding locks and blocking other transactions.

Even rebooting your database servers will not fix this problem, since a correct imple‐ mentation of 2PC must preserve the locks of an in-doubt transaction even across restarts (otherwise it would risk violating the atomicity guarantee). It’s a sticky situation.

The only way out is for an administrator to manually decide whether to commit or roll back the transactions. The administrator must examine the participants of each in-doubt transaction, determine whether any participant has committed or aborted already, and then apply the same outcome to the other participants. Resolving the problem potentially requires a lot of manual effort, and most likely needs to be done under high stress and time pressure during a serious production outage (otherwise, why would the coordinator be in such a bad state?).

Many XA implementations have an emergency escape hatch called heuristic decisions: allowing a participant to unilaterally decide to abort or commit an in-doubt transac‐ tion without a definitive decision from the coordinator [76, 77, 91]. To be clear, heu‐ ristic here is a euphemism for probably breaking atomicity, since it violates the system of promises in two-phase commit. Thus, heuristic decisions are intended only for getting out of catastrophic situations, and not for regular use.



Limitations of distributed transactions

XA transactions solve the real and important problem of keeping several participant data systems consistent with each other, but as we have seen, they also introduce major operational problems. In particular, the key realization is that the transaction coordinator is itself a kind of database (in which transaction outcomes are stored), and so it needs to be approached with the same care as any other important database:

- If the coordinator is not replicated but runs only on a single machine, it is a sin‐ gle point of failure for the entire system (since its failure causes other application servers to block on locks held by in-doubt transactions). Surprisingly, many coordinator implementations are not highly available by default, or have only rudimentary replication support.
    
- Many server-side applications are developed in a stateless model (as favored by HTTP), with all persistent state stored in a database, which has the advantage that application servers can be added and removed at will. However, when the coordinator is part of the application server, it changes the nature of the deploy‐ ment. Suddenly, the coordinator’s logs become a crucial part of the durable sys‐ tem state—as important as the databases themselves, since the coordinator logs are required in order to recover in-doubt transactions after a crash. Such applica‐ tion servers are no longer stateless.

- Since XA needs to be compatible with a wide range of data systems, it is necessar‐ ily a lowest common denominator. For example, it cannot detect deadlocks across different systems (since that would require a standardized protocol for systems to exchange information on the locks that each transaction is waiting for), and it does not work with SSI (see “Serializable Snapshot Isolation (SSI)” on page 261), since that would require a protocol for identifying conflicts across dif‐ ferent systems.
    
- For database-internal distributed transactions (not XA), the limitations are not so great—for example, a distributed version of SSI is possible. However, there remains the problem that for 2PC to successfully commit a transaction, all par‐ ticipants must respond. Consequently, if any part of the system is broken, the transaction also fails. Distributed transactions thus have a tendency of amplifying failures, which runs counter to our goal of building fault-tolerant systems.
    
    Do these facts mean we should give up all hope of keeping several systems consistent with each other? Not quite—there are alternative methods that allow us to achieve the same thing without the pain of heterogeneous distributed transactions. We will return to these in Chapters 11 and 12. But first, we should wrap up the topic of consensus.
### 容錯共識 Fault-Tolerant Consensus


### 成員和協調服務 Membership and Coordination Services


## Summary





# resource
notion: [跨db的交易處理](https://www.notion.so/nture4388/db-88ed30cc1382487cbf71afc54797d987?pvs=4)