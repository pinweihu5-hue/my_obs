---
aliases:
  - ddia partition
---



# partition ddia

# What is partition
- 資料量太大了，我們就會進行分區 partition
- 每種 db 都有自己對於分割的叫法:
    - mongoDB, elasticSearch, solr : shard
    - HBase: region
    - Cassandra, Riak: vnode
    - couchbase: vBucket
- 一般來說，我們 partition and replication 會一起用
	- 下面的配置，兩個 node 掛了，全部的資料還在
    - you partition data into 4 node and replicate across node
    ![[IMG-partition-20250810200239240.png|671]]

# how to 分割
- 分割原則 ->  balance the load, avoid hot spot
    

### by range
- say to find Ben_Hu, it is at second partition
![[IMG-partition-20250810200240418.png|586]]
- in each partition, we can use SSTable or LSM-tree as store engine
- 假設你放的資料是很多 IoT sensor 讀到的某個數據
- partition key 是 timestamp
- 1~12 個 key 也是 sorted
- pro
	- 你要拿最近3個月的資料很快, key1~key3, 前三個 node 去拿, 不需要去其他 node 找
	- 且這三個 node 還是放在一起，有序。
- con 
	- 會出現 hotspot 問題，因為你最近一個月的寫入，全部只會打一個 parition
	- 基本上我們沒有讓資料均勻分布 by 某個 attr
- 怎麼解決上面的問題?
	- 分割key改為  sensor_id + timestamp
	- 這樣就不會只會寫入一個 partition
	- 但是你這樣如果要 query 最近一個月的，你就會需要去很多paritition 拿
- 你要根據你的 access pattern, 讀寫的 性能spec，來決定 partition key


### by hash 
![[IMG-partition-20250810200241640.png]]  
- 用 hash 來均勻分布
- 把你要分割的欄位(e.g. timestamp here) hash, 然後放入 node
	- In MongoDB, 你可以啟動 hash-based mode, 這樣 range query 會進入很多分區
- pro
	- 因為不是 range based, 因此如果你用 ts 做 hash, 這個月的 query 也一樣機率上分布在很多分區上
- con
	- 損失某些資料既有的連續性
		- 無法有效率的進行 range query
		- key 也不會 sort 
		- e.g. Range queries on the primary key are not supported by Riak, Couchbase , or Voldemort.



複合鍵 as 折衷 
- 第一個鍵, pk as hashed pay, 第二個鍵, sk as sorted key 可做 range search
- 譬如你要拉某個 user 的很多 comment 記錄，然後你要拉過去 10個月內
	- 這樣設計 pk, sk -> (user_id, comment_timestamp)
	- 可以很快的從一個分區拉出這個 user
	- 然後對這個 user 你可以很快的(鄰近小數量的分區)拉出 10個月內的資料

- Cassandra
	- 只有主鍵(PK)的第一部分會被雜湊，用於決定分區，但其他欄位會作為串接索引，用於在 Cassandra 的 SSTable 中排序資料。
	- 因此，查詢無法在複合主鍵的第一個欄位內搜尋值的範圍，但如果它指定了第一個欄位的固定值，則可以在主鍵的其他欄位上執行有效的範圍掃描。
	- 這種串接索引方法為一對多關係提供了一個優雅的資料模型。
	- 例如，在社群媒體網站上，一個使用者可能會發布許多更新。如果更新的主鍵選擇為 (user_id, update_timestamp)，那麼你可以有效地擷取特定使用者在某個時間間隔內發布的所有更新，並按時間戳排序。
	- 不同的使用者可能會儲存在不同的分區上，但在每個使用者內，更新會按時間戳排序，儲存在單一分區上。
	- Ben note: DynamoDB 好像也是類似的作法

- you will still have a hot-spot problem, like a user is very popular
	- you can solve this at app-level 
		- identify this kind of user and add 2 random digit to the pk
		- this user_id will go into many partition
		- although u will need to go thru 100 partition and merge when you need to get this user data.
		- 反正如果你要解決hotspot, 你要拿的時候就得去很多分割然後 merge, 這兩者是tradeOff


### pk and sk 的 底層實作
Partitioning and Secondary Indexes

兩種作法
document-based, term-based (global-based)


document-based
- say PK 切 0~1000
- 0~500 放 par 0, 500~1000放 par1
- 會建立一個 secondry idx 來讓你 filter color 和 make
- 當你 insert, pk idx and sk idx都會建立
- 每一個 partition 維護自己的 sk idx, 像是下面的 color:red, 第一個par 只有 191, 306, 第二個 par 有 768, 分開管理
- 缺點是你要拿 all red car, 你要去全部的分區，又叫 scatter/gather (分散拿)
- used in MongoDB, Riak, Cassandra, ElasticSearch, SolrCloud and VoltDB
![[IMG-partition-20250810200243039.png]]


global based / term-based
- 如下圖， all red car 的 sk idx 只有在第一個分區
- query all red car 會比 term-based 還快，因為只需要去一個分區拿
- 缺點：讀比較快的缺點 -> 寫比較慢。你要寫入需要去很多分區 insert sk idx。譬如下面要寫入 893, 需要去兩個分區寫入 sk idx
	- 一個改進就讓 sk idx 寫入是非同步
- eg: Amazon DynamoDB, Riak search feature,  Oracle data warehouse
![[IMG-partition-20250810200244514.png]]


### rebalance
- overtime, you need to reblance.......因為 more query coming in, add more data, machine failed,
- GOAL: 
	- after rebalance, we hope the load is still even, read and write work properly, move as less data around as possible

四種 rebalance 作法
- 天真作法:  `hash(key) % N` ⇒ not going to work, add or remove node will make your data moving around a lot

- 預先把每一個 node 建立夠多的 parition
    - say 10 node, and we create 1000 partition, so each node handle 100 partition
    - for adding new node, like node4, only need to take small amount of partition for node0~3 to the new node 4, like below
    - 缺點：一開始比較難去評估要建立多少 parition
![[IMG-partition-20250810200245892.png|793]]


- way3: 動態分割
	- 當也分割太大，我們把他切一半，如果太小，我們把他合併。
    - also can work like way2, we can have multiple partition in one node and no need to move around many data during merge and split process
    - pro: you don’t need to have a fixed partition number at the beginning
    - HBase and RethinkDB use this.
- way4: partition proportionally to nodes
    - e.g. Cassandra, Ketama
    - each node has fixed amount of paritions
    - when data grow, parition size grow, node # is the same.
    - if you increase node #, the partition shrink
    - 這一點不太懂，有空去看原文。
- 手動還是自動?
	- rebalance 通常風險很高，大多數都是手動。
	- 想像一個情境：你因為 node loading 高，因此你要把資料移出去，然後你自動移出去的操作又會造成這個 node 的 loading 更高，因此自動 rebalance 基本上還要考慮到這一點。
    - Couchbase, Riak, and Voldemort 會幫我們建立 "分割規劃建議"。


##  服務發現  (資料分區後，要去哪裡拿資料?)
Service Discovery: request routing



When request is coming, how it know where to get data ⇒ service discovery

三種作法
1. 隨機送到一個 node (maybe use Round Robin to assign) and then go to other node if can’t find
2. 路由層 a routing tier (coordinate server or gossip protocol) to handle it, see next section
3. cleint side need to know where to send the request (maybe a hard coded mapping table in mem or in config)
![[IMG-partition-20250810200247407.png]]


### use a separate coordinate server, e.g. ZooKeeper
- cs (coordinate server) 會記錄你要查的資料在那一個 node 的那一個 parition, node 的 ip 位址等等
- 如果有 node / parition 的異動, cs 也會 update

如下圖
![[IMG-partition-20250810200248985.png]]
- HBase, SolrCould, Kafka use zooKeeper
- MongoDB use its own config server
- LinkedIn expresso DB use Helix
- Cassandra and Riak use different approach:
    - use gossip protocol to spread the changes of nodes, it is more complex but no need to use a coordinate server
    - ref: [[a key-val store#如何偵測 node failure -> 八卦協議（gossip protocol）]]



## Q & A
### 請問 partition 和 sharding 的意思一樣嗎？
parition 比較泛稱
簡單說可以區分為垂直和水平分割
sharding 就是水平分割，就是把同一個資料集分散到很多資料庫實例中
like User table 切 10區，放在10個 db instance 中




# 垂直和水平 and 單機和分散式 兩個維度
我們可以用 垂直和水平分割 and 單機和分散式 兩個維度來看 分區/分割
ref: [\[System Design\] 淺談Database Partition. Centralized and Distributed. | HoMuChen](https://homuchen.com/posts/what-is-database-partition-sharding/)
![[IMG-partition-20250810200250188.png]]


## [1] 單機＋垂直：就是拆表
類似 rent -> rentConfig, rentReading


## [2] 單機＋水平：table partition
**應用場景**: 最常見的就是選擇**時間**相關的欄位來作為partition key

以下以postgresql為例， 使用`measurement` table中的 `log_date` 作為partition key:
```sql

CREATE TABLE measurement (
  city_id         int not null,
  log_date         date not null,
  peaktemp        int,
  unitsales       int
) PARTITION BY RANGE (log_date);


CREATE TABLE measurement_y2021m09 PARTITION OF measurement
    FOR VALUES FROM ('2021-09-01') TO ('2021-10-01');

CREATE TABLE measurement_y2021m10 PARTITION OF measurement
    FOR VALUES FROM ('2021-10-01') TO ('2021-11-01');

CREATE TABLE measurement_y2021m11 PARTITION OF measurement
    FOR VALUES FROM ('2021-11-01') TO ('2021-12-01');

```

- 按每個月的range創建partition。
    
- **優點**: 
	- 常見的access patern可能會是讀取最近的資料，對於比較久以前的資料很少去讀取，所以不需要每次讀取都在一個大的table裡尋找
	- 藉由partition創造出多的小的table，改善效能。 
	- 另外像是如果你有保留政策(retention policy)的話，也變得相當容易去管理，比如說你只保證保留近一個月的資料， 所以超過一個月的partition就直接DROP掉就好了！


## [3]  分散＋水平：Multiple RDBMS servers



分散架構：
把一份大的檔案分拆成許多小份，當然也有許多益處， 讀取效能的優化、更易於管理等等
但隨著資料的長大，資料增加的速度越來越快， 總有一天一台機器還是會遇到瓶頸，此時就有了將資料放在多台機器的想法。

原理都是一樣的，選定一個欄位作為shard key，你的application server將決定這筆資料的要放到哪台DB上，

![[IMG-partition-20250810200251154.png]]


- **優點**: 增加了throughput，不管是卡在Disk或是CPU，一台機器不夠寫不夠讀，那你有試過兩台三台十台嗎～
- **缺點**: application code的複雜度變大了是一定的，除此之外，每個partiton現在已經各自獨立， 跨partition沒有unique constraint、reference constraint，更不可能 JOIN secondary indexes也必須各自去建立，幾乎許多RDBMS提供的好用的功能，在跨partition時都不能用了， 只能在自己的application code上去實現。

    
    比如說你想做JOIN，但被referenced的table已經被我們做partition了，application code就必須去到每一台機器裡去找， 然後在程式裡面把資料做結合。


通常還是會搭配相關的套件, like 
- [[partition#Gizzard：a middlewarte to 建立分散式資料儲存]]
- [myCat](https://ithelp.ithome.com.tw/articles/10228006)
- [Vitess | Scalable. Reliable. MySQL-compatible. Cloud-native. Database.](https://vitess.io/), 等等


## [4] 分散＋水平：sharding in NoSQL

在大數據時代，為了處理大量資料而冒出的許多NoSQL都有的內建功能， 就跟上面講的原理一樣，只不過這次資料庫本身就幫我們把sharding這件事都處理好拉～ 不需要我們寫任何一行的code，只需要在configuration file或UI上設置一下就好。

而面對的問題還是一樣的，所以大部分的NoSQL提供的API都比較簡單， 不像RDBMS有各種constraint，可以JOIN，transaction可以用。

關於NoSQL怎麼實作sharding的，每種產品也都不同，大家要自己去看他們是如何實作的，shard key怎麼選的，可以自己選嗎? 是range還是hash partition，如何動態增加或減少shard的數量(這部分這篇文章裡沒有討論到)。

比如說MongoDB可以自己選擇shard key，也可以選擇shard strategy， 大家可以根據自己的use case來調整，詳見[MongoDB sharding](https://docs.mongodb.com/manual/sharding/)


## [5] 可以想成是 一些放 S3, 一些放 nosql DB等等










# Gizzard：a middlewarte to 建立分散式資料儲存
## sharding 簡介

分片策略通常涉及兩種技術：分區和複製。 parition and replica

透過分區，資料被分成小塊並儲存在多台電腦上。
每個區塊都足夠小，儲存它的電腦可以有效地操作和查詢資料。

透過複製，資料的多個副本儲存在多台電腦上。
由於每個副本都在自己的機器上運行並且可以回應查詢，因此系統可以透過添加更多副本來有效地回應對相同資料的大量查詢。
複製還使系統能夠抵禦故障，因為如果任何一個副本損壞或損壞，系統可以使用另一個副本來執行相同任務。


## Gizzard is middleware
![[IMG-partition-20250810200253342.png|449]]

Gizzard 位於客戶端（通常是 PHP 和 Ruby on Rails 應用程式等 Web 前端）和許多資料分割區和副本之間的「中間」。
坐在中間，所有資料查詢和操作都透過 Gizzard 進行。 
Gizzard 實例是無狀態的，因此可以根據需要，運行盡可能多的 Gizzard 來維持吞吐量或管理 TCP 連線限制。
Gizzard 非常高效，部分原因是它在 JVM 上運行。 Twitter 的 Gizzard 應用程式之一（FlockDB，我們的分散式圖形資料庫）可以為每台商用機器每秒處理 10,000 個查詢。但您的情況可能會有所不同。


## Gizzard can supports any datastorage backend

Gizzard 旨在跨任何網路可用的資料儲存服務複製資料。這可以是關聯式資料庫、Lucene、Redis 或任何您能想像的資料庫。

作為一般規則，Gizzard 要求所有寫入操作都是冪等和可交換的（idempotent and commutative)。
特別是，Gizzard 不保證寫入操作會依序套用。因此，無論寫入的應用順序為何，系統的設計都必須達到一致的狀態。


## 自定義轉發表(forwarding table)
![[IMG-partition-20250810200255458.png]]

夠過 Gizzard, 我們可以 提供了一個 自定義的 hash function，給定資料的鍵（該鍵可以是特定於應用程式的，
該函數會產生屬於轉發表中的某個範圍的數字。

這種基於範圍的方法不同於許多其他分散式資料儲存中使用的「consistent hashing ring」技術。
hash 的方法 會產生  heterogeneously sized partitions，這通常讓你免於 hotspot的問題。


## a replication tree


轉發表中引用的每個分片，可以是實體分片或邏輯分片。

實體分片就是指特定資料儲存後端（例如 SQL 資料庫）。
邏輯分片代表資料的某種邏輯轉換。分支上的這些邏輯轉換通常是關於如何將讀寫操作傳播到該分支的子分支的規則。



例如:
這是一個兩級複製樹。
請注意，此圖代表一個分區 parition（如轉發表中所引用）
![[IMG-partition-20250810200256773.png|418]]


圖中的「複製」分支是簡單的策略，用於對所有子級重複寫入操作，並根據健康狀況和權重函數平衡子級之間的讀取。
您可以根據特定的資料儲存需求，建立自訂分支 / 邏輯分片，例如新增額外的事務 / 協調 primitive 或仲裁(quorum)策略。
但 Gizzard 附帶了一些具有廣泛實用性的標準策略，例如複製、只寫、唯讀和阻止（既不允許讀也不允許寫）。 

每個分區的上述 replication topologies 的性質可以不同。
這表示您可以為「較熱」分割區設定較高的複製級別，為「較冷」分割區設定較低的複製層級。這使得系統具有高度可配置性。



## fault-tolerant: 如何處理故障


所有寫入都具體化到持久的事務性日誌中。然後，對分片中的所有副本非同步執行寫入。
如果分片不可用，寫入操作將進入錯誤佇列並稍後重試。

為了實現 “最終一致性”，這種 “稍後重試” 策略要求您的寫入操作是冪等且可交換的。
這是因為稍後重試策略可能會無序地套用操作（例如，在重試舊的失敗作業之前套用較新的作業）。


## a migrations example

如何將將replica中的資料從一台電腦複製或移動到另一台電腦?
我們可以執行此操作來平衡更多或更少電腦之間的負載，或處理硬體故障。

從 Datastore A 遷移到 Datastore A' 時

- 會在它們之間設定一個 replica
- 在 Datastore A' 前面放置一個 WriteOnly 的策略。
- 然後資料從舊replica複製到新replica。 
- WriteOnly replica 確保在新 replica sync 完成前，因為WriteOnly 的策略，因此client 不會從中讀取任何資料（因為Datastore A' 的資料還不完整）

![[IMG-partition-20250810200257637.png]]



## 如何處理寫衝突以確保資料一致性

寫入衝突是指對相同記錄的兩次操作嘗試以不同的方式變更該記錄。
由於 Gizzard 不保證操作按順序，因此在對資料進行建模時必須確定是冪等且可交換的，以確保資料一致性。 (This is actually easier than trying to guarantee ordered delivery of messages with bounded latency and high availability.)
一個作法是，我們可以在寫入時用 timestamp去 確認，我們只寫入 newer 的資料。



# link
[\[System Design\] 淺談Database Partition. Centralized and Distributed. | HoMuChen](https://homuchen.com/posts/what-is-database-partition-sharding/)