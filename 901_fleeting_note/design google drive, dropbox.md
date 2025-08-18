類似 app: Google Drive, Dropbox, Microsoft OneDrive, and Apple iCloud



### req

- a file storage to store documents, photos, videos, and other files in the cloud.
- access your files from any computer, smartphone, and tablet
- easily share those files with friends, family, and coworkers


feature:
- Upload and download files, file sync, and notifications.
- support any file type
- storage must be encrypted
- let's set a file size is 10 GB
- 100 M DAU


key feature:
- upload file
- dl file
- Sync files across multiple devices
- see file revision
- share file with other ppl
- Send a notification when a file is edited, deleted, or shared with you

- not discuss -> google doc collaboration editing

非功能需求
- reliability -> data lose is not acceptable
- Fast sync speed is important
- Scalability and availability



### Back of the envelope estimation 
skip



### high level design: start from simplest

- a Apache web server
- a sql db
- a storage system
the dir in the storage system can be arrange like below:
![[IMG-design google drive, dropbox-20250810200233413.png|462]]


### API
- POST /api/upload 上傳
	- header
		- `Content-Type`: `multipart/form-data`
		- `Authorization`: Bearer token (如果需要身份驗證)
	- body
		- file: 要上傳的檔案
		- fileName: 檔案名稱 (可選，用於續傳)
		- fileSize: 檔案大小 (可選，用於續傳)
		- chunkIndex: 檔案的分塊索引 (可選，用於續傳)
		- totalChunks: 總分塊數 (可選，用於續傳)

```json
if 全部傳完
{
  "success": true,
  "data": {
    "fileId": "唯一檔案識別碼",
    "fileUrl": "檔案存取的 URL"
  }
}


續傳中的成功回應
{
  "success": true,
  "data": {
    "fileId": "唯一檔案識別碼",
    "uploadedChunks": 5,
    "totalChunks": 10
  }
}
```


- donwload a file
	- GET /api/download/{fileId}
		- header
			- `Authorization`: Bearer token (如果需要身份驗證)
			- Content-Disposition: attachment; filename="example.txt"  // 提示瀏覽器下載檔案，並指定檔案名稱
			- Content-Type: application/octet-stream  // 根據檔案類型（例如，`application/pdf`、`image/png` 等）。


==

接著，我們量變大的，因此上面的 single db 不夠用了

我們打算把資料放在 s3

- s3 提供 cross-region replication, 這讓我們可以sleep well
- 我們也加了 LB in fron of our web server
- 我們也可以 add more web server when loading is high
- 也把 db 移到 managed DB -> 他會自動 replica
![[IMG-design google drive, dropbox-20250810200234351.png|448]]


### 如果有多人上傳檔案，那要如何解決衝突

系統先處理的win
![[IMG-design google drive, dropbox-20250810200235534.png]]

系統會跟 user 2 顯示衝突
![[IMG-design google drive, dropbox-20250810200236802.png]]
user 2 可以選擇 overwrite 檔案，放棄自己的版本，或是 merge 檔案


---

### High level design, more complete


block servers:
- A file can be split into several blocks, each with a unique hash value, stored in our metadata database.
- each block then store in S3.
- 如果要重建 file, get hash from db and use those hash to get them from cloud storage and  them reconstruct those block in a order to to bring a file back.

notificaiton svc and offline backup queue:
- 如果有檔案異動，notificaiton svc 會對 user 發送通知，如果userf offline, 會先放在 queue中，等user 上線再送出。user app 可以一定時間去 pool  notificaiton svc 看看是否有新的異動。
![[IMG-design google drive, dropbox-20250810200237843.png]]


### block servers

如果每一次更新檔案都要整個重新處理，很多面相都不 make sense
- watse 流量 bandwidth
- waste process resource

兩個方法

- Delta sync: When a file is modified, only modified blocks are synced instead of the whole file using a sync algorithm
- Compression: For example, gzip and bzip2 are used to compress text file

![[IMG-design google drive, dropbox-20250810200238772.png|654]]

如果有修改，差異性比對上，只上傳 差異的部份
![[IMG-design google drive, dropbox-20250810200239818.png|457]]


### High consistency requirement

It is unacceptable for a file to be shown differently by different clients at the same time. The system needs to provide strong consistency for metadata cache and database layers. 

Achieving strong consistency in a relational database is easy because it maintains the ACID (Atomicity, Consistency, Isolation, Durability) properties.
### schema for meta data
上面文件的 metaData,  我們可以用下面的 sql schema 
![[IMG-design google drive, dropbox-20250810200241008.png]]



### 上傳流程
![[IMG-design google drive, dropbox-20250810200242344.png]]

### 下載流程
![[IMG-design google drive, dropbox-20250810200243983.png]]



### Notification service
It uses long polling to keep clients up to date with file changes.

以下用 DropBox 使用 long pooling 的通知系統

Dropbox 使用長輪詢（long polling）來實現其通知系統，以便在文件變更時及時通知客戶端。以下是一些有關長輪詢和 Dropbox 通知系統的詳細說明：

什麼是長輪詢？
長輪詢是一種伺服器推送技術，與傳統的輪詢（polling）不同。在傳統的輪詢中，客戶端會定期向伺服器發送請求以檢查是否有新數據。這會導致不必要的請求和延遲，因為即使沒有新數據，客戶端也會定期發送請求。

而在長輪詢中，客戶端發送請求後，伺服器不會立即回應，而是保持連接直到有新數據可用或超時。當伺服器有新數據時，它會立即回應請求，客戶端隨後可以處理這些數據。這樣可以減少不必要的請求並提高效率。

Dropbox 的通知系統
文件變更檢測：當用戶在 Dropbox 中對文件進行更改（例如上傳、刪除或修改文件）時，這些變更需要即時通知到所有相關的客戶端。

長輪詢的使用：Dropbox 客戶端會向伺服器發送長輪詢請求，請求持續保持開啟。當伺服器檢測到文件變更時，它會通過這個請求將變更信息發送給客戶端。

即時更新：客戶端收到通知後，可以立即更新用戶界面，顯示最新的文件狀態，這樣用戶就可以即時看到變更，而不需要手動刷新或等待下一次輪詢。

連接管理：如果長輪詢請求超時或出現錯誤，客戶端可以自動重新發送請求，保持與伺服器的連接，確保不會錯過任何變更通知。

優點
即時性：長輪詢能夠提供接近即時的通知，讓用戶能夠迅速獲得文件變更的資訊。
減少不必要的請求：與傳統的輪詢相比，長輪詢減少了伺服器的負擔，因為它不會頻繁地處理無效請求。
簡化的實現：對於某些應用場景，長輪詢比 WebSocket 等技術更容易實現，特別是在需要兼容舊瀏覽器或不支持 WebSocket 的環境中。


### Save storage space
- De-duplicate data blocks. 
	- Eliminating redundant blocks at the account level is an easy way to save space. Two blocks are identical if they have the same hash value.
- Set a limit: We can set a limit for the number of versions to store. If the limit is reached, the oldest version will be replaced with the new version.
- Moving infrequently used data to cold storage. 
	- Cold data is the data that has not been active for months or years. Cold storage like Amazon S3 glacier [11] is much cheaper than S3




- [ ] [(692) How We've Scaled Dropbox - YouTube](https://www.youtube.com/watch?v=PE4gwstWhmc)


Reference materials
[1] Google Drive: https://www.google.com/drive/
[2] Upload file data: https://developers.google.com/drive/api/v2/manage-uploads
[3] Amazon S3: https://aws.amazon.com/s3
[4] Differential Synchronization https://neil.fraser.name/writing/sync/
[5] Differential Synchronization youtube talk https://www.youtube.com/watch? v=S2Hp_1jqpY8
[6] How We’ve Scaled Dropbox: https://youtu.be/PE4gwstWhmc
[7] Tridgell, A., & Mackerras, P. (1996). The rsync algorithm.
[8] Librsync. (n.d.). Retrieved April 18, 2015, from https://github.com/librsync/librsync
[9] ACID: https://en.wikipedia.org/wiki/ACID
[10] Dropbox security white paper:
https://www.dropbox.com/static/business/resources/Security_Whitepaper.pdf [11] Amazon S3 Glacier: https://aws.amazon.com/glacier/faqs/





## note: How We've Scaled Dropbox
[How We've Scaled Dropbox - YouTube](https://www.youtube.com/watch?v=PE4gwstWhmc&t=1481s)

dropbox note

首先，為了確保file都可以 sync
有很多的 upload操作， sync操作
因此讀寫比幾乎是1:1，這跟很多線上服務 high ratioi of read to write 不同

另外我們也不能說讓你砍掉shared 資料後，有人還是可以看到你砍的資料
或是砍了一半卻沒砍好 -> 所以我們需要 ACID 的保護
這也跟很多服務可以使用最終一致不同


==


### 我們來看看架構演化的歷程，和我們如何克服


第一代
全部都在一個 vm上
files on disk
db in vm
![[IMG-design google drive, dropbox-20250810200245290.png|509]]


然後遇到Disk out of space  -> 第二代
s3 拉出來
db 拉出來
![[IMG-design google drive, dropbox-20250810200246731.png|512]]


最後遇到
web server capacity不夠
pooling 不是很好選擇
進入第三代
利用 notificaiton server 的 通知來取代 client pooling
把上傳下載的主功能拉出來，go  to block server
![[IMG-design google drive, dropbox-20250810200248350.png|710]]




遇到以下問題 所以進入第四代
- 需要更多的capacity for meta and block server to handle more load
- 因為本來我們讓 block server 直接 query db, 又放在不同的地方，db query latency is high -> 決定只讓 meta server query db and they are in the near place geographically, and that block server RPC call to meta server
- 另外也增加 cache 來降低 db load
![[IMG-design google drive, dropbox-20250810200249760.png]]



第五代
parition and sharding for many components
![[IMG-design google drive, dropbox-20250810200250639.png]]


不是只是單存加很多機器，遇到很多困難, like

我們需要去修改 memcache 的 lib
因此 memecahce 是，本來你 server 如果在 memcache拿不到資料，server會去另一個 memcache server拿，這是為了 高可用性
但是如果兩個 server 分別去不同的 memcache 拿，一個有，一個沒有，這樣就不一致了。

然後他們那時候用 python, lb那一層狀到了 global interpret lock -> 就是可以 mutilple thread， but one will be schedule at a time. 
一個 lb 很簡單，但是當有很多 lb, 他們需要客製化一些LB 邏輯讓很多lb 可以 repect python 這個特性


db 這部份，他們就是用了基本的 sharding
這邊提到本來一些 交易，因為 sharding, 現在是還是可以視為一個交易，這就很難說了。
需要盤點清楚use case, 確認本來的 code 在這個 use case 的交易要如何處理。


not server(notification server)
連著 10 M + client conected to it
這邊有兩層結構來處理，因為一次要對 10m+的client send notrificaiton, just too expensive
client connect the not server all time via long pool
when server have event to notify, sevver and send out to client
1 million client connect to a server and not reach the server limit, it hit another kenral bug (another story)
就算可以負載 1 m, 如果 server do down, 要重連，會花滿多時間



有做 block level deduplicate, so no same block in the storage level (use hash and check if hash is the same)
block level is a 4mb chunk, so a file will be divided into chunk and ecach chunk is a block and has it id, and each chunk has its mapping to S3
deduplicate 不是 user level, 是global level, but 是否要 deduplicate 還有很多其他要考慮

client 如果發現只有修改一部分， will rsync diff and upload to server


很多 db 都很好 shard
但是 sharing file 不好 shard, like users <-> shard folers, 兩邊都會互相 query 很多, 這邊沒有 shard, 因此不好設計。


如何決定哪些在 self-managed, 哪些在 aws?
cost effieceveb, file 多數都在 s3 (let aws handle the replication)
但是很多 db, 其他非 file的資料，都放在自管的server, 且這些 data 也和 api server 很近，這對速度幫助很大
有一些 block server 會用 ec2 run

==

會有一個 server file jounrnal 記錄
ns is name space, it's like a your drop box folder
![[IMG-design google drive, dropbox-20250810200251866.png|619]]

remove casepath since it's now handle by client side
add this to quicky get the previous revision
![[IMG-design google drive, dropbox-20250810200255101.png|542]]


change pk
so, ns_id are together, menas same folder ar
and also latest (user active log are also together)
id  is like timestamp, 
![[IMG-design google drive, dropbox-20250810200256358.png|566]]


file change to 255
mysql use 255 in one bytes, if you also limit your varchar within 255, it's perf better
![[IMG-design google drive, dropbox-20250810200257402.png]]


remove latest in pk and field
本來 latest 都在一起,  perf for read, 但是 write 的工作就會較多
但後來想要 optimize for write, so remove this, and this is good for write, in the cost of slower read
這是一個有趣的tradeoff and given dropbox had many write, so they do it and work for them
![[IMG-design google drive, dropbox-20250810200258156.png]]

this table is per user/name space, so id size is not an issue
