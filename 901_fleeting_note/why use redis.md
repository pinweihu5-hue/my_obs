- why not we just use JS/TS code to handle?
	- redis list -> js arr
	- redis set -> js set
	- redis hash -> js map/object
- 當你有**很多 pods** 需要共用一個**暫時狀態**時
	- 分布系統的 資料來源，讓 application server 無狀態
	- 以上的概念都是跨 node/pod 需要有同一個服務去管理，這邊就是用同一個 redis去管理
	- 基本上，只要那個東西是暫時的，且需要跨node/pod的功能，都需要放 redis 而不是放 sql
- 類似
	- bull queue use redis as it store engine, 因為 job 的資料是暫時的
	- sentryToken, tmp token
	- 利用 redis 分布 上鎖m redlock



也很好用

- 指令基本上都設計成接近原子性(ACID)的效果 (but not all, see [[從 ACID 來看 redis multi]])
    - 提供一些複雜指令的原子性，like **`Rpoplpush`** 把 list1 的最後一個拿出來然後推到 list2 的第一個, [[redis Rpoplpush]]
- 指令格式
    - 用一個 key 可以拿到對應的資料結構, string, hash, set, list…etc
    - key → dataType(string-val, hash(H), sorted-set(Z), set(S), list-element)
- 多數資料格式都有預設值，不需要 init (省了一開始需要用app code寫 init code, 好方便), 也不需要砍
    - 如果拿走最後一個 ele from list, list 也會自動清掉