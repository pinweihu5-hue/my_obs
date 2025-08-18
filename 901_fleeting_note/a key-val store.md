
design scope
- The size of a key-value pair is small: less than 10 KB. -> key 通常不大 
- high scale -> 基本上要可以無限擴充的資料量
- 高可用
- 一致性要可以調整 (like [[Cassandra]])
- 低延遲


### alex-book
- api 設計
	- put(key, value)
	- get(key)
- 存放的 store
	- 資料可以壓縮
		- 或是用 gcp store, or S3 然後分層, 很久沒用到的就放到 cold storage
	- 前面基本上會放一層 cache
- 基本上一定是分散資料系統
- CAP 上看要 AP or CP
	- [[分布式系統 CAP]]
- 高可用, 可擴展, 資料要平均分布 -> consistent hashing 機制 
	- [[Consistent Hash]]

### 分散系統確保讀寫後的資料一致  -> 法定共識 Quorum Consensus
- 三個參數, N, W, R
- 有一個協調 node, coordinator
- W 表示 coordinator 要收到幾個 ack 才表示 寫入成功
- R 表示 coordinator 要收到幾個 ack 才表示 讀取成功
- N 表示 系統中全部的節點數量
- W, R 都是 1 -> 很快，but 一致性弱
- W or R 越大, 一致性越強, tradeoff, 看你的需求來調整
- 這是可調整的

通常包括以下幾個步驟：
1. **提議（Proposal）**：一個節點（通常稱為提議者）提出一個值或決策，並將其廣播給其他節點。
2. **投票（Voting）**：其他節點（稱為投票者）接收到提議後，根據自己的狀態和規則進行投票。每個節點可以選擇支持或反對提議。
3. **達成法定（Quorum）**：如果支持提議的節點數量達到法定的要求（例如 N/2 + 1），則該提議被認為是有效的，並且所有節點都將更新其狀態以反映這一決策。
4. **確認（Commit）**：一旦達成法定，所有節點將確認這一決策，並執行相應的操作。


通常使用 `w + r > n` 條件，用於確保在分布式系統中達成一致性。

這個條件表示：
1. **避免讀取過時數據**：當一個寫操作（例如更新數據）發生時，必須確保在讀取操作中至少有一個節點是最新的。這樣可以防止讀取到過時的數據。
2. **容錯性**：即使某些節點失效或無法通信，這個條件也能確保系統仍然能夠正常運行。通過要求寫入和讀取的節點數量之和大於總節點數量，系統能夠容忍一定數量的節點失效。
3. 
假設有一個分布式系統，總共有 5 個節點（n = 5）。如果我們設置：
- w = 3（需要寫入的節點數量）
- r = 3（需要讀取的節點數量）
那麼 `w + r = 3 + 3 = 6`，這大於 `n = 5`，因此這個條件滿足。這意味著在進行寫入和讀取操作時，系統能夠保證一致性，即使有一個節點失效，仍然能夠讀取到最新的數據。

- related [[replication (ddia)#leader-less]]


### 當 node 上的 資料不一致時，如何解決版本衝突  可以用 vector clock

每一個 資料都有版本 Di, i is version

當資料 D 寫入 Server Si
系統記錄邏輯:
- 如果 `Di [Si, vi]` 已經存在， `vi++`
- 如果不存在，建立  `[Si, 1]`

下圖，Server X , Sx 要寫入 D5 (5 可以看成是 version 5) 時，
發現 Sy, Sz 都有1, 這時候 Sx 會需要 resolve, 因為兩邊都有 +1, 然後自己又要 +1, so 應該是 3 
-> so D5 就是 `D5([Sx, 3], [Sy, 1], [Sz, 1])`

我們等於保存每一個 server 上的資料的歷程
![[IMG-a key-val store-20250810200231296.png|541]]



### 如何偵測 node failure -> 八卦協議（gossip protocol）

工作原理如下：
• 每個節點維護一個節點成員列表，其中包含成員 ID 和心跳計數器。
• 每個節點定期增加其心跳計數器。
• 每個節點定期向一組隨機節點發送心跳，這些節點又將心跳傳播到另一組節點。
• 一旦節點接收到心跳，成員列表將更新為最新信息。
• 節點 s0 注意到節點 s2（成員 ID = 2）的心跳計數器已經很長時間沒有增加。
• 節點 s0 向一組隨機節點發送包含 s2 信息的心跳。一旦其他節點確認 s2 的心跳計數器已經很長時間沒有更新，則節點 s2 被標記為離線，並將此信息傳播給其他節點。
![[IMG-a key-val store-20250810200231606.png]]



### 處理暫時性故障  「鬆散法定人數」（sloppy quorum）
在通過八卦協議檢測到故障後，系統需要部署某些機制以確保可用性。
在嚴格的法定人數方法中，讀取和寫入操作可能會被阻塞，如法定共識部分所示。


一種稱為「鬆散法定人數」（sloppy quorum）[4] 的技術被用來提高可用性。
系統不再強制執行法定人數要求，而是選擇哈希環上前 W 個健康伺服器進行寫入，和前 R 個健康伺服器進行讀取。
離線伺服器將被忽略。如果某個伺服器因網絡或伺服器故障而無法使用，另一個伺服器將暫時處理請求。當故障伺服器恢復時，變更將被推送回去以實現數據一致性。這個過程稱為提示交接（hinted handoff）。


如果長期故障，我們需要一個有效率的機制去 sync 資料來，需要用到 Merkle tree （先略）


### Merkle tree （先略）



### High level design
![[IMG-a key-val store-20250810200232305.png|727]]

• Clients communicate with the key-value store through simple APIs: get(key) and put(key, value).
• A coordinator is a node that acts as a proxy between the client and the key-value store. • Nodes are distributed on a ring using consistent hashing.
• The system is completely decentralized so adding and moving nodes can be automatic. 
- Data is replicated at multiple nodes.
• There is no single point of failure as every node has the same set of responsibilities.


每一個 node 都有如下職責
![[IMG-a key-val store-20250810200233110.png|474]]


寫入
![[IMG-a key-val store-20250810200234166.png]]
就是一般 db 的寫入
會有 commit log 可以 recovery, replicate 等等
前面有一個 cache 層
這邊用 SStables
- see [DDIA storage and retrieval note](https://www.notion.so/nture4388/DDIA-storage-and-retrieval-722411cfae7241d8b04c6e5e030f17a5?pvs=4)


讀取
![[IMG-a key-val store-20250810200235309.png]]
bloom filter 
- 可以用更少的空間去判斷 if this key in it? (hash 也可以，但全部都要存，bloom filter 不需要存)
- 判斷是否不存在一定正確
- 判斷是否存在會誤判
- 因此先看看是否不存在，不存在就不用進入 db 看了
bloom filter can see [notion note](https://www.notion.so/nture4388/Bloom-Filter-4b88c99cc6744fe49a110e21821317c2?pvs=4)




## link
- [Amazon's Dynamo | All Things Distributed](https://www.allthingsdistributed.com/2007/10/amazons_dynamo.html)
[1] Amazon DynamoDB: https://aws.amazon.com/dynamodb/
[2] memcached: https://memcached.org/
[3] Redis: https://redis.io/
[4] Dynamo: Amazon’s Highly Available Key-value Store:
https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf [5] Cassandra: https://cassandra.apache.org/
[6] Bigtable: A Distributed Storage System for Structured Data:
https://static.googleusercontent.com/media/research.google.com/en//archive/bigtable- osdi06.pdf
[7] Merkle tree: https://en.wikipedia.org/wiki/Merkle_tree
[8] Cassandra architecture: https://cassandra.apache.org/doc/latest/architecture/
[9] SStable: https://www.igvita.com/2012/02/06/sstable-and-log-structured-storage-leveldb/ [10] Bloom filter https://en.wikipedia.org/wiki/Bloom_filter