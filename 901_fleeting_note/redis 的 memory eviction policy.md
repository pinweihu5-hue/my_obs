redis 的 內存淘汰策略



- cache 設定好 TTL, redis 好像是預設 lazy delete, 用到一樣的東西，如果 marked delete 在去砍，
- redis 不會每一次 TTL 到了，就去找到那個資料再砍，這樣比較沒效率。
- 預設 lazy delete, 用到一樣的東西，如果 marked delete 在去砍，

- redis 會排時間去砍 cache
- 設定策略，如果上面兩種(排程砍，lazy砍)都還是來不及讓 redis 的 mem 夠用 ，就使用以下策略, config setting
- Redis 的默認內存驅逐策略是 `noeviction`，這意味著當內存達到上限時，Redis 不會自動刪除任何數據，而是會返回錯誤。
- 這種策略適合需要確保數據持久性的應用。

### 其他可用的內存淘汰策略

Redis 提供了多種內存淘汰策略，開發者可以根據應用需求選擇合適的策略。以下是一些常見的內存淘汰策略：

1. **volatile-lru**：
    - 只驅逐設置了過期時間的鍵，並根據最近最少使用（LRU）算法選擇要驅逐的鍵。
2. **allkeys-lru**：
    - 驅逐所有鍵，根據最近最少使用（LRU）算法選擇要驅逐的鍵。
3. **volatile-lfu**：
    - 只驅逐設置了過期時間的鍵，根據最少使用頻率（LFU）算法選擇要驅逐的鍵。
4. **allkeys-lfu**：
    - 驅逐所有鍵，根據最少使用頻率（LFU）算法選擇要驅逐的鍵。
5. **volatile-random**：
    - 隨機驅逐設置了過期時間的鍵。
6. **allkeys-random**：
    - 隨機驅逐所有鍵。
7. **volatile-ttl**：
    - 驅逐設置了過期時間的鍵，優先驅逐那些剩餘存活時間最短的鍵。

### 如何設置內存淘汰策略

可以通過修改 Redis 配置文件或使用 `CONFIG SET` 命令來設置內存淘汰策略。

例如，設置為 `volatile-lru`：
`CONFIG SET maxmemory-policy volatile-lru`


check current setting
`CONFIG GET maxmemory-policy`



