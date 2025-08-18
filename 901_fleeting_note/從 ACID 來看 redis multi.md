related
- [redis doc ono multi](https://redis.io/docs/latest/develop/interact/transactions/#usage)
- [[ACID]]


# 沒有 A: 不同於跟 DB 的 原子性 （all have or all fail）
- How multi work?
	- 事務中的所有命令都被序列化並按順序執行
	- 使用 [`MULTI`](https://redis.io/docs/latest/commands/multi/)指令輸入 Redis 交易。此時使用者可以發出多個命令。 Redis 不會執行這些指令，而是將它們排隊。該命令始終回复 `OK` 。
	- 一旦呼叫 [`EXEC`](https://redis.io/docs/latest/commands/exec/) ，所有命令都會被執行。
	- 如果客戶端在調用 [`EXEC`](https://redis.io/docs/latest/commands/exec/)命令之前斷線/crash，則不會執行任何操作。
	- 如果調用後，有指令失敗，其他命令也會被處理，沒有 roll back -> 因此不是 not all work and not all down -> 沒有 A
		- 如果你自己要手動執行回滾

```
MULTI ( 開始事務 )
SET A-Account "0" ( 加入隊列 )     -> 成功
SET B-Account "1000" ( 加入隊列 )  -> 失敗
SET C-Account "1000" ( 加入隊列 )  -> 一樣跑
EXEC ( 執行隊列操作 )
```


# 不算有I: 類似隔離, 雖然單線程，但不同 thread 還是有可能會交織影響
- 可以用 [`WATCH`](https://redis.io/docs/latest/commands/watch/)，來幫助 Redis 交易提供 CAS (check and set)
	- 監視 [`WATCH`](https://redis.io/docs/latest/commands/watch/) ed 鍵以偵測它們的變更。如果在 [`EXEC`](https://redis.io/docs/latest/commands/exec/)命令之前至少修改了一個監視鍵，則整個事務將中止，並且 [`EXEC`](https://redis.io/docs/latest/commands/exec/)將返回 [Null 回覆](https://redis.io/docs/latest/develop/reference/protocol-spec/#nil-reply)以通知事務失敗。



# 沒有 D -> 如果 crash 還是會 loss 一些 data
- rdb 看以下觸發條件, 中了就會進行存到hd 的 .rdb file
	- 900 秒內有 1 個改動。
	- 300 秒內有 10 個改動。
	- 60 秒內有 10000 個改動。
- aof 比較吃性能，但可以縮短到1s
- 以上，就算是 aof 還是有機率損失 1s data → 沒有持久性
- PS: innoDB use redolog+fsync來達到，commit 成功前提（redo log fasync into db）


其他留意事項
- 使用指令了解其time complexity
    - 因為 single thread → 如果真的太慢，會卡住！！
- 很多操作 (like for loop)，可以用 pipeline or multi 一次搞定，不用一次往返 redis
- 如果需要，不要用 keys (to see all keys), 會卡, 用scan, 這個是 batch 操作
- key都要設 TTL



# multi 的好處是
- multi <-> 跟 exec 間，不會跟其他客戶端干擾，可以減少 race condition.
- 性能會比較好: 整個是一包，像是上面，可以是一次網路往返，不用三次



ref:
[30-18 之資料緩存層的服務 - Redis 概念與一致性難題 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10224221)