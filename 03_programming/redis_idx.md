- [[why use redis]]
- 指令
	- multi
		- [[ioredis MULTI and EXEC]]
		- [[從 ACID 來看 redis multi]]
	- [[redis Rpoplpush]]
- 設定
	- [[redis 的 memory eviction policy]]
	- 如果 local 需要跑大量測試，需要開並行， redis 要設定一下，讓redis db 使用 jest worker id
		- [[one redis db per test worker]]
- 內部
	- [[redis internal]]
- redis 資料結構
	- [[redis_sorted_set]]
	- [[redis pubsub]]
- course or book
	- [notion_redis_from_dist_book](https://www.notion.so/nture4388/ch9-distributed-primitive-redis-e6ded496dd1f4b2181a029bdef6a33ac?pvs=4)
	- [redis-udemy in notion](https://www.notion.so/nture4388/redis-udemy-cd3b4e9fff1d47c29fca00e2e6222eaa?pvs=4)
- 應用
	- [[WeMo blocker 機制]]
	- [[design doc on Lock quotes time in redis]]
	- to read -> [高并发场景下，红包雨怎么实现？](https://mp.weixin.qq.com/s/q1BPuqFDpdQC_AodeOabsA)
	- [[使用 Redis 實作限流器的挑戰與缺陷]]
- 套件
	- [[ioredis-mock]]





# why 快取要設 tls
- 快取層和持久層有緩存不一致的問題需要解決
- 這樣你讓這個不一致至少要有一個最後會失效的時間，兜底。

