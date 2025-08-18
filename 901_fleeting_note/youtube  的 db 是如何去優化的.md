
ref: [YouTube Architecture - High Scalability -](https://highscalability.com/youtube-architecture/)


- use raid10 disk
	- 兼顧 raid 0 and raid1
	- raid 0 同時讀多 disk
	- raid 1  有備
	- 就是把很多 disk 組radi0 and then 全部組建立 replicate
	- pro
		- fast and HA
	- con
		- complex and expensive

- img serving is not that simple
	- each img have many thumbnail -> 一個畫面要抓的資源數量比影片多很多
	- 一個畫面有很多 縮圖, maybe at least 50+
	- req 縮圖 per sec is very high
	- 一開始用 Apache cannot handle
	- 後來 Apache + squid（反向代理）緩減 -> 量更大 -> 又遇到瓶頸
	- 又換 lighttpd  -> 遇到 multiple thread 下 快取無法共用的問題
	- 最後用 GCP 的 big table (bigTable is NoSQL version of bigQuery)
		- use multi-level distributed cache


- suffer replicate lag
	- why?
		- leader-follower 架構 下, leader is good 機器, follower is not, and replicate is async -> so replicate lag is big
		- 只能讓 follower 也用好的機器?
	- how to replicate lag is small?
		- 不是讓 leader use good node, follower use bad node
		- 而是用功能和重要性來切 like watch vid in expensive cluster (and master and follower both use good node), other in not that expensive cluster
