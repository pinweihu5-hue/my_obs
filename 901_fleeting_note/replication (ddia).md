---
aliases:
  - ddia replica
---

# why we want to replicate?
- 備份
    - one node fail and we got other to restore
- 延遲時間(latency)可以降低 via  geo-locate, 把 node 全球各地, user 直接去那邊拿
    - low lentency, node is just at Taiwan (even in ISP cach) for me.
- 高可用
    - since you can tolerent fail node
    - 這邊不只是備份，你要機制可以讓服務持續可以用
- 吞吐量(throughput) 可以提高
    - assume you addressed the hot-spot problem
    - so 1000 req will go each node, each node can handle 100 req
    - no waiting in queue
    - like you can handle 1000 req w/o waiting


# Replication帶來的缺點
## 需要儲存空間

想當然爾，複製了幾份的資料就需要多幾份的磁碟的空間，不過現在硬碟越來越便宜的時代， 應該不是個大問題。
## 資料的不一致

不一致的主要來源就是兩種: **replication** 和 **concurrent write**
試想一下資料如果只有單一來源，那要跟誰不一致呢？反之，因為有了replica， 每份複製要如何保持同步及一致就會是個問題? 會造成什麼consistency的問題， 後面會在陸續討論。


# How to replicate

資料要從master被複製到slave，最簡單的可能就像是直接`cp`，`scp`， 或是你寫備份script，每天固定時間將DB裡的資料寫到另一台DB。

另外現在許多資料庫都有提供**change data capture**的功能: 每當一有資料寫入時， 就會通知你，這有點像是是在application level的trigger， 像是MangoDB的Change Streams或是AWS Dynamo的Streams。

而RDBMS也有類似的功能，像是postgreSQL的WAL、MySQL的Binlog， 如果是cluster的，內部可能也是利用上述的機制來做複製及備份。


# 幾種實作 replication log 的方法

## statement-based
- 少用, 因為有下面這些問題
	- 沒有決定性 non-determinsic, statement 有包括 RAND( ), NOW( ) 
    - if the statement is depend on data
        - like auto-increment or transcaction
        - So you need to ensure the replication run sequatially
            - can’t run in parallel
    - can’t replicate the statement which have side effect

## Write Ahead Log(WAL)
- use **SSTables**, BG run log compaction
- pro: 
	- see [[storage and retrieval DB engine]]
- con:
	- SStable 是 儲存引擎，因此會管理到那一個 bytes 要存入那一個 hard disk sector
	- 那需要考慮你放資料的 leader and follower 的 server 的儲存是否可以相容
	- 也表示你需要 all server 都可以相容
    - 需要小心相容問題


## Row-based replication
- compared to WAL, 好處是可以跟儲存引擎解耦
- 很多可以不用 replicate, 只需要記錄:
	- new roecrod, 全部 cols 都要記錄
	- 如果是 deleted row 可以只記錄 pk
	- 如果是 updated row, 可以只記錄 pk, 和修改的 col
- passive and active can use dif version easily and work well in hetergerouse downstreams and cooperate with other system (like change data capture /CDC)

## trigger-based -> last resort
- above method is done in db-level, this is app-level
- if we do this in app, the way you do can be very flexible (you do what lang can do)
- with external tool, like Oracle Golden Gate or db build-in trigger or store-procedure
    - ex. say you have a write, this is a event which trigger a process to replicate
- Con:
    - higher overhead (round-trip, bandwith, memory and maintain)
    - prone to mistake
- shall not be default-opt, only use this way as last resort


# leader-follower

also called active/passive (mater/salve)


write to active and replicate to follower
![[IMG-replication (ddia)-20250810200240425.png]]



## async vs sync
![[IMG-replication (ddia)-20250810200241655.png|907]]
- follower1 is sync
	- leader 1 會等到 follower 的 200 response
	- like req-response model
- follower2 is async
    - no need to get the confirmed


- sync
    - pro:
        - make sure replicate is done and when user query, the data is synced, data lose
    - con:
        - 較慢 (need to wait sync done)

- async:
    - pro:
        - 較快，因為不需要等待 200 response back
    - con?
        - if active down before replicate, data lose

## 折衷: **semi-syncrhoous**
- if we have a lot of passive node, do we need to wait sync from all passive node? Say if one passive node is down? then 我們會永遠等不到 all 200 back
- solve: only need to wait 1 or wait a certain amount of passive node done is okay, aks: **semi-syncrhoous**


## 如何新增 follower?
- got a active’s snapshot and copy that to new passive
- the passive then use the active log to get the activity after that snapshot


## how to handle node down?

passive down
- easier
- know the last transaction of the failed passive and recovery from master (get data from replication log)

active down
- more trickier
- this process called failover (**故障轉移**)
- **promote** a new passive to active
	- 我們需要 re-config, 來讓 all passive 知道 某一個 passive 被promote 為new active 
	- 另外如果 old active 回來了，他需要知道他不是 active了。
- use timeout to confirm if the passive is down, but setting this timeout is tricky.
- how to promote passive to active?
	- use concusses algo/vote -> see chapter 9 [[Consistency and Consensus, DDIA CHAPTER 9]]
- what if 那個 down 的 old active 有 data 還沒 pass to other passive 然後有 new active 被 promote?
	- what to handle those conflict data?
		- drop them? fine, but violate durability
	- a case study:
		- Github have a case that a new active been promoted
		- this new active use the already used (by old active) auto-increment counter
		- and then the external redis use that old increment counter
		- so basically, redis go to the wrong node to get the data and return to the user!
		- from the user perspective, it like you get the other ppl repo data
- what if the old active back and think he is also a active, and we have split-brain problem, which lead to data conflict
	- talk more in later chapter -> see chapter 9 [[Consistency and Consensus, DDIA CHAPTER 9]]
- many team failover **manually** → due to above mentioned complications.


# replication lag problem
- as mentioned, sync replicate is hard to use, so mostly we use async replication
- async → lead to eventually consistent problem
	- 廣義上 CDN 及 DNS 都有 replication 的概念，將檔案或 DNS record 複製到鄰近 user 的機器上， DNS 甚至複製到了 user 的本地機器裡，可以看到他們為了支持更好的 read performance， 不管是 latency 還是 throughput，而採用了這樣的架構設計，換來的是 inconsistency 的問題， 就像是 DNS record 的更新，通常要花上幾分鐘甚至是幾天才有辦法同步。
- below are some guarantee to ensure avoid anomaly


## 保證：讀到自己寫的
read your own write guarantee


異常：
你寫入 leader
active replicate to follower1, 但是還沒到 follower2, 
然後你要讀的時候去看 follower2
因為 async, follow2 還沒 replicate 到

![[IMG-replication (ddia)-20250810200243045.png|820]]

how to solve?
- 寫入後，只會從 leader 讀
- how to impl?
	- 方法一：可以透過 timestamp 來檢查，寫入後的 x 分鐘內，只會從 leader 讀取
	- 方法二：你也可以從 foloower 讀，不過讀到的資料的 timestamp 一定要 比你寫入的 timestamp 新



*Read your own write gurantee* on corss-device?
say you write in pc and read from iphone?
- this could be probelmatic since your pc use ASDL and your iphone use 5G, and they route to different data-center, and use different node
- In this case, you need to have a centaral place to save all meta-data which know this is the same user (儘管不同 device) and use 上面提到的 same trick to ensure *Read your own write gurantee*


## 保證：單調遞增讀, 這一次讀到，下一次就應該也要讀到
Monotonic reads guarantee

- to avoid time travel to old time anomly
- User 1234 insert comment 
- User 2345 query data, go to  follower 1, User 2345 see the inserted result
- User 2345 query data again,  go to  follower 2, User 2345 see the result is gone!
![[IMG-replication (ddia)-20250810200244522.png]]

solve: 
- x 分鐘內寫入的資料，User 2345 都會去 same node 讀取


## Consistent Prefix Read guarantee

觀察者看到對話的邏輯順序錯了

- 違背因果性 violation of causality
- mr.A ask: `what’s your age?` (this goes to nodeA and replicate to NodeA1)
- mr.b reponse, `20` (this goes to nodeB and replicate to NodeB1)
- due to async, nodeA1 is update latter than NodeB1
- So, 觀察者 see `20` first and then see `what’s your age` later

![[IMG-replication (ddia)-20250810200245899.png|870]]
- the reason is cuz the two msg replicate to dif node i, j
    - and second msg is replicate slower than first msg for c see this two node i and j.

Solution?
> One solution is to make sure that any writes that are causally related to each other are written to the same partition—but in some applications that cannot be done efficiently. There are also algorithms that explicitly keep track of causal dependencies, a topic that we will return to in “The “happens-before” relationship and concurrency” on page 186.



related: [[youtube  的 db 是如何去優化的]]

# multi-leader

機制比較複雜，多數用在 多資料中心的情境


多資料中心
- 資料中心內部，用會自己的高速網路，會快很多
- 可以看到每個資料中心都是一個 single-leader mode, 然後用 async way to replicate/sync data across 資料中心




offline mode 也是多領導例子
- like your mail and calendar, you edit and only update to the server (another leader) when you online again
- so each device act as a leader db
- eg CouchDB



Collaborative-editing, GoogleDoc, Wiki
- you can’t use single-leader in this case, you can’t use lock to only allow one person editing
- How to handle conflict? 如下

![[IMG-replication (ddia)-20250810200247410.png]]
- 有三種解法
	- avoid it at the first place: a piece of data always at same leader node to avoid multiple copy of data in different leader ⇒ hard to resolve later
	- if multiple people edit the same piece of data, we can use Last Write Win
		- 但這個方法的缺點是會丟資料，first write 就丟資料了
	- we can try to merge like B/C and show both and let reader decide how to merge (merge it when next time we read it, or when db detect the write conflict)
		- like git



**multiple leader topologies: circular, star and all-to-all**
![[IMG-replication (ddia)-20250810200248997.png]]
- circular & start 有一個大問題:
	- need to handle when one node fail when it is replicating info one by one, need to mark which node had been replicated (to avoid infinity loop)
- mostly we use all-to-all
- 也會遇到和需要處理 上面提到的 replication lag 問題


replication lag 問題 會有類似下面這樣
A 寫入的第一筆 update val to 1
B 想要 val++
最後應該是 2

但是 A寫入到 leader 2 的那個比較慢
因此又把 val 改回 1
![[IMG-replication (ddia)-20250810200250192.png]]
解法可以用 use version vectors 
[[a key-val store#當資料不一致時，如何解決版本衝突 可以用 vector clock]]

# leader-less
PS: also called Dynamo style (no the same as dynamoDB in AWS), eg, Riak, Cassandra, Voldemort

沒有領導者，因此我們需要協調者來協調





下面 user1234 寫入三個 node
但是 node3 offline
1, 2 node,寫入都 okay

當 user2345 要讀，怎麼知道那一個對?
寫入會加入 version
用 version 判斷

![[IMG-replication (ddia)-20250810200251189.png|928]]
那 replica3 online 後要怎半?
- 寫入修復：when client b read c, fix node c with newer ver. val
- anti-entropy: 使用 bg process 來矯正


**使用法定共識**

- you config w, r, n
- w 表示寫入已經被 ack 成功, r 表示讀取已經被 ack 讀取
- 常用 w+r > n (w is success written , r is success read, n is # of node)
- the w is quorum write and r is quorum read. ps: quorum(**法定人數)**
- below case is n=5, r=3, w=3, we can tolerant 2 fail
- usually, we use `w = r = (n+1)/2` to max fail node tolerance
	- 法定的大小是系統中節點總數的一半加一（N/2 + 1），這樣可以確保即使有一半的節點失效，仍然能夠達成共識。
- you can even use `w + r <= n`, 但這個設置很容易有過期資訊
- also see [[a key-val store#法定共識 Quorum Consensus]]

![[IMG-replication (ddia)-20250810200253346.png]]



偵測併發寫入不一致
- when client A send to node 1,2,3 
- at the same time, client b also send to node 1,2,3 but node 1 fail when client b send it. 
- Result, we have conflict b/n nodes
![[IMG-replication (ddia)-20250810200255462.png|965]]
as mentioned, we can use last-win-write but sacrifice durability



use version to handle: 
each write will have its version and server will keep it and when client read
client need to merge them together. (for delete, we use tombstone mark) 
![[IMG-replication (ddia)-20250810200256776.png]]
if we have many replicate we need to track version on them ⇒ version vector
also see: [[a key-val store#當資料不一致時，如何解決版本衝突 可以用 vector clock]]