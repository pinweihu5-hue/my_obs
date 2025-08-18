
示意圖
![[IMG-autocomplete, typeahead, search-as-you-type-20250810200231738.png|382]]


### req
- 要很快 0.1 s 的反應速度
- 結果要有關連，某種 sort or rank
- 系統要 scale and HA


### estimate 
(skip)



### high level design


如果資料量很小：
用一個類似下面的 freq table
```sql

select * from table
where query like 'prefix%'
order by frequency DESC
limit 5

```

![[IMG-autocomplete, typeahead, search-as-you-type-20250810200232437.png|506]]

### trie
一般來說資料量都會很大 -> 用 trie
related: 複習 [[cs61b tries]]

每一個 node 也綁上一個 freq (or use certain kind of score)
![[IMG-autocomplete, typeahead, search-as-you-type-20250810200233238.png]]

找 prefix -> O(p), p 是 string length
找一個 prefix 下的所有 child  -> O(c), c 是 child 數量
把所有的 child 都用某種方式去 sort -> O( c logc)


撈前 top N 個 word:
O(p)+O(c)+O( c logc)
![[IMG-autocomplete, typeahead, search-as-you-type-20250810200234261.png]]


進一步調整 trie

1. 另外 prefix 沒有必要無限長，使用者也不會使用很長的prefix。因此 prefix is a pre-defined length -> O(p) 可以變成 O(1), constant
2. 把 top search result 都 放在 每一個 node, like below。這樣我們也不用下去找 its children
![[IMG-autocomplete, typeahead, search-as-you-type-20250810200235440.png|766]]

結論： to get top K query result from a prefix -> O(1)

## 主要組件

### Data gathering service

![[IMG-autocomplete, typeahead, search-as-you-type-20250810200236702.png|731]]

從 log 拉出 query word, 然後透過 aggregator parse and 整理成我們要的資料
類似算一下 freq, score
最後存到 db
然後常常被 query 的會被 cache 起來

上面可以每個禮拜做一次，如果沒有要求即時更新的話


這邊 db 我們選用 key-val store
key 就是 trie 的 node 的 key
val 就是 trie node 放的 val
![[IMG-autocomplete, typeahead, search-as-you-type-20250810200237702.png|721]]



### Query service
![[IMG-autocomplete, typeahead, search-as-you-type-20250810200238645.png|500]]

另外我們也要設定讓瀏覽器可以 cache query result

如下圖，cache 一小時，且是 private (for this user only)
![[IMG-autocomplete, typeahead, search-as-you-type-20250810200239694.png]]



### trie operation
- create
	- 就是上面提到 worker 從 log 拿來的資料，parse and build up trie tree
- update
	- 建議整個砍掉重建比較簡單
	- 如果要 partially update 也可以
- delete unwated query word
	- 可以在 cache 出去有一層 layer 去檔掉
	- 或是 cronjob 去 db check and remove


### how to scale trie?
- a-z 放 26 個 partition
- 如果要切更多，可以類似大寫也是一個 parition -> 52個
- 或是第2層 -> like ‘aa-ag’, ‘ah- an’, ‘ao-au’, and ‘av-az’ 這邊就 4 個 partition
- 不過以上的缺點是， a~z 的出現不是均勻分布的，我們會希望 partition 是均勻的
- 另一個作法就是我們另外用一個 service 來告訴我們要如何把某個 char 要放在哪裡
	- we analyze historical data distribution pattern and apply smarter sharding logic
![[IMG-autocomplete, typeahead, search-as-you-type-20250810200240912.png|625]]

## 更多要思考的
- 支援非英文 -> unicode
- 不同國家有不同的關鍵字 -> 不同區域有不同 trie
- 思考要如何支援 real-time query search, 這樣如果有熱門事件，馬上就可以被update 到 trie
	- 這需要更高效率的 re-create, update trie, 一些方向包括：
	- Reduce the working data set by sharding
	- Change the ranking model and assign more weight to recent search queries
	- Data may come as streams, so we do not have access to all the data at once. Streaming data means data is generated continuously. Stream processing requires a different set of systems: Apache Hadoop MapReduce [6], Apache Spark Streaming [7], Apache Storm [8], Apache Kafka [9], etc.




## link
[1] The Life of a Typeahead Query: https://www.facebook.com/notes/facebook- engineering/the-life-of-a-typeahead-query/389105248919/
[2] How We Built Prefixy: A Scalable Prefix Search Service for Powering Autocomplete:
https://medium.com/@prefixyteam/how-we-built-prefixy-a-scalable-prefix-search-service- for-powering-autocomplete-c20f98e2eff1
[3] Prefix Hash Tree An Indexing Data Structure over Distributed Hash Tables:
https://people.eecs.berkeley.edu/~sylvia/papers/pht.pdf
[4] MongoDB wikipedia: https://en.wikipedia.org/wiki/MongoDB
[5] Unicode frequently asked questions: https://www.unicode.org/faq/basic_q.html [6] Apache hadoop: https://hadoop.apache.org/
[7] Spark streaming: https://spark.apache.org/streaming/
[8] Apache storm: https://storm.apache.org/
[9] Apache kafka: https://kafka.apache.org/documentation/