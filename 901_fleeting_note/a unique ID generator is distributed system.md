related
- [[why not just use UUID?]]


需求
• IDs must be unique.
• IDs are numerical values only.
• IDs fit into 64-bit.
• IDs are ordered by date.
• Ability to generate over 10,000 unique IDs per second



### 可以用 uuid (like v4?)
- 128 bit 佔空間 → 沒必要那麼多 bit
- uuidV4 is not order by time -> 很多功能會喪失，類似放 db 當 idx, pk. 效能就不好
	- uuid 後面版本 可以嗎 -> 還要 study



### alex-book


Snowflake ID 的結構如下：
- **1 位**：保留位（通常為 0）
- **41 位**：時間戳（毫秒級，從某個固定的時間點開始）
- **10 位**：機器 ID（可以用來標識不同的服務器或實例）
- **12 位**：序列號（在同一毫秒內生成的 ID 序列）

- data center and machine ID 一開始就那個機器和中心就寫死，一開始就固定
	- 2**5  = 32 -> 支援 32個資料中心
	- 每一個資料中心支援 32 台機器
- sq num
	- 每秒可以產出 4096 (`2^12=4086)
	- if no need that high cocurrent -> 可以降低
	- ts ->14 bits
		- 2 ^ 41 - 1 = 2199023255551 ms, 約有  69 years
	- how to covert binary 41 bits to UTC?
		- 假設 41 bit is -> `00100010101001011010011011000101101011000`
		- 轉為dec -> [00100010101001011010011011000101101011000 binary to decimal conversion](https://www.rapidtables.com/convert/number/binary-to-decimal.html?x=00100010101001011010011011000101101011000) -> 297616116568
		- 加上一個設定的初始日期 , 比如 snowfake default epoch 1288834974657, equivalent to Nov 04, 2010,01:42:54 UTC.
		- 297616116568 + 1288834974657 = 1586451091225
		- 再轉為 utc  (`new Date(1586451091225)`)
![[IMG-a unique ID generator is distributed system-20250810200231586.png]]



- **選擇實現**：
    - 可以使用現成的 Snowflake ID 庫，根據你使用的編程語言選擇相應的庫。例如：
        - Java: Snowflake ID Generator
        - Python: snowflake-connector-python
        - Go: go-snowflake
- **配置機器 ID**：
    - 根據你的環境配置機器 ID，這通常是根據服務器的唯一標識（如 IP 地址或主機名）生成的。
- **生成 ID**：
    - 使用庫提供的 API 生成 ID。例如，在 Python 中，你可以這樣使用：
```python  fold

from snowflake import SnowflakeGenerator

# 初始化生成器，假設機器 ID 為 1
generator = SnowflakeGenerator(machine_id=1)

# 生成唯一 ID
unique_id = generator.generate()
print(unique_id)


```
    
- **處理並發**：
    - 確保你的實現能夠處理高併發的請求，這樣可以避免在同一毫秒內生成重複的 ID。
- **時間同步**：確保你的服務器時間是同步的，以避免生成重複的 ID。
	- 使用 NTP, NTP 是一種用於同步計算機時鐘的協議。大多數操作系統都支持 NTP，可以通過以下步驟配置
```bash title:ntp配置 fold

sudo apt update
sudo apt install ntp



# at /etc/ntp.conf  設定  公共 NTP 伺服器

server 0.pool.ntp.org
server 1.pool.ntp.org
server 2.pool.ntp.org
server 3.pool.ntp.org




# 啟動
sudo systemctl start ntp
sudo systemctl enable ntp
ntpq -p



# 如果你的服務器有防火牆，確保允許 NTP 流量（通常是 UDP 123 端口）
# 定期檢查服務器的時間是否正確，可以使用以下命令: 
date

# 如果時間不正確，可以手動同步：
`sudo ntpdate -u 0.pool.ntp.org`


# 也可以用 Chrony (另一個 NTP 客戶端), 速度更快 


```

- **機器 ID 的唯一性**：在多個實例中運行時，確保每個實例的機器 ID 是唯一的。
- **ID 長度**：Snowflake ID 通常是 64 位整數，這意味著它可以生成大量的唯一 ID。






## [flicker 的 ticket server：便宜的分散式唯一主鍵](https://code.flickr.net/2010/02/08/ticket-servers-distributed-unique-primary-keys-on-the-cheap/)



### why flicker need it

Flickr use sharding to handle 很多資料。有時我們需要在資料庫之間遷移數據，所以我們需要我們的主鍵是全域唯一的。


### why not use GUIDs / UUID

- guid 太大, 128 bit
- bad for using it as idx in sql DB


### consistent hashing ring is useful here?

consistent hashing ring is is better suited for write-cheap environments (e.g. [LSMTs](http://portal.acm.org/citation.cfm?id=230826))
while MySQL is optimized for fast random reads.

(amazon dynamo db use coonsistent hashing to handle its sharding [ref](http://www.allthingsdistributed.com/2007/10/amazons_dynamo.html), which is optimized its write)



### use one db to handle the id

當然，如果每秒鐘處理 60 多張照片，那麼這張桌子就會變得相當大。我們可以去掉所有關於照片的額外數據，只將 ID 儲存在中央資料庫中



### 實作
Flickr 票證伺服器是一個專用資料庫伺服器，其上有一個資料庫，並且在該資料庫中存在諸如用於 32 位元 ID 的 `Tickets32` 和用於 64 位元 ID 的 `Tickets64` 之類的表。


```sql
CREATE TABLE `Tickets64` (
  `id` bigint(20) unsigned NOT NULL auto_increment,
  `stub` char(1) NOT NULL default '',
  PRIMARY KEY  (`id`),
  UNIQUE KEY `stub` (`stub`)
) ENGINE=InnoDB
```


`SELECT * from Tickets64` 傳回一行，如下所示：
```sql
+-------------------+------+
| id                | stub |
+-------------------+------+
| 72157623227190423 |    a |
+-------------------+------+

```



當我需要一個新的全域唯一 64 位元 ID 時，我發出以下 SQL：

```sql
REPLACE INTO Tickets64 (stub) VALUES ('a');
SELECT LAST_INSERT_ID();
```



### how to handle SPOF?

我們透過運行兩個票證伺服器來實現「高可用性」。
我們透過將 ID 空間從中間（偶數和奇數）劃分來劃分兩個框之間的責任，使用：
```

TicketServer1:
auto-increment-increment = 2
auto-increment-offset = 1

TicketServer2:
auto-increment-increment = 2
auto-increment-offset = 2

```


We round robin between the two servers to load balance and deal with down time. 
The sides do drift a bit out of sync.
I think we have a few hundred thousand more odd number objects then evenly numbered objects at the moment, but this  is okay.







## why twitter need to create snowflake
ref: [宣布雪花 --- Announcing Snowflake](https://blog.x.com/engineering/en_us/a/2010/announcing-snowflake)
- 我們需要能夠以高度可用的方式每秒產生數萬個 id 的東西
- 這些 id 需要_大致可排序(k_sorted(定義 see below))
	- 因為如果推文 A 和 B 大約在同一時間發布，它們的 id 應該彼此非常接近，因為這是我們和大多數 Twitter 用戶端對推文進行排序的方式
- 此外，這些數字必須適合 64 位元。
	- 因為我們希望控制長度，不然儲存大量的這些 tweet id 會很痛苦。




### 其他
- use use ZooKeeper as KGS -> to study
- [The 2^120 Ways to Ensure Unique Identifiers](https://firebase.blog/posts/2015/02/the-2120-ways-to-ensure-unique_68)
- [Check-and-increment a counter in Redis - Stack Overflow](https://stackoverflow.com/questions/56899438/check-and-increment-a-counter-in-redis)
- [閒談軟體設計：UUID. 今天聊點不一樣的東西，有點不起眼卻很重要的小玩意：UUID](https://medium.com/閒談軟體架構/閒談軟體架構-uuid-2748df80aa7e)
- [UUID 原理與實作分析 - 該如何挑選適合的 UUID 版本](https://yuanchieh.page/posts/2020/2020-12-01-uuid-%E5%8E%9F%E7%90%86%E8%88%87%E5%AF%A6%E4%BD%9C%E5%88%86%E6%9E%90-%E8%A9%B2%E5%A6%82%E4%BD%95%E6%8C%91%E9%81%B8%E9%81%A9%E5%90%88%E7%9A%84-uuid-%E7%89%88%E6%9C%AC/)
- [Redis patterns example | Docs](https://redis.io/docs/manual/patterns/twitter-clone/)
- [Ticket Servers: Distributed Unique Primary Keys on the Cheap | code.flickr.com](https://code.flickr.net/2010/02/08/ticket-servers-distributed-unique-primary-keys-on-the-cheap/)




k sorted
k - 排序 (k-sorted) 指的是一個數列或陣列中的每個元素最多距離其排序後的位置 k 個位置。換句話說，如果將數列排序後，每個元素在 k - 排序的數列中，其索引位置與排序後索引位置的差距不會超過 k。