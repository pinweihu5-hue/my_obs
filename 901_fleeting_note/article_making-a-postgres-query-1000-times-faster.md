---  
alias:  
creation_date: 2024-06-05 21:17  
tags: 
related:
- [[db_idx]]

---  


結論:
把 explain 中的 filter -> 變成 Index Cond 速度會變快


https://mattermost.com/blog/making-a-postgres-query-1000-times-faster/


雲端的 sql insight 顯示這是 cpu bound
![[IMG-article_making-a-postgres-query-1000-times-faster-20241003104933806.png]]


query
```sql

SELECT Posts.*, Channels.TeamId
  FROM Posts LEFT JOIN Channels ON Posts.ChannelId = Channels.Id
  WHERE
    Posts.CreateAt > $1
    OR
    (Posts.CreateAt = $1 AND Posts.Id > $2)
  ORDER BY Posts.CreateAt ASC, Posts.Id ASC
  LIMIT $3;

```




```
mmdb=> EXPLAIN (ANALYZE, BUFFERS) SELECT Posts.*, Channels.TeamId FROM Posts LEFT JOIN Channels ON Posts.ChannelId = Channels.Id WHERE Posts.CreateAt > '1687424888405' OR (Posts.CreateAt = '1687424888405' AND Posts.Id > 'tpomh9yu1tffmdp6dopobwuc9h') ORDER BY Posts.CreateAt ASC, Posts.Id ASC LIMIT 10000;
QUERY PLAN
------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=1.22..2287.94 rows=10000 width=464) (actual time=23921.236..23954.229 rows=10000 loops=1)
   Buffers: shared hit=40557115
   ->  Incremental Sort  (cost=1.22..13998881.93 rows=61217938 width=464) (actual time=23921.235..23953.205 rows=10000 loops=1)
         Sort Key: posts.createat, posts.id
         Presorted Key: posts.createat
         Full-sort Groups: 311  Sort Method: quicksort  Average Memory: 45kB  Peak Memory: 45kB
         Buffers: shared hit=40557115
         ->  Nested Loop Left Join  (cost=1.00..11421751.31 rows=61217938 width=464) (actual time=23920.970..23947.067 rows=10001 loops=1)
               Buffers: shared hit=40557115
               ->  Index Scan using idx_posts_create_at on posts  (cost=0.57..9889434.54 rows=61217938 width=461) (actual time=23920.930..23931.063 rows=10001 loops=1)
                     Filter: ((createat > '1687424888405'::bigint) OR ((createat = '1687424888405'::bigint) AND ((id)::text > 'tpomh9yu1tffmdp6dopobwuc9h'::text)))
                     Rows Removed by Filter: 40920000
                     Buffers: shared hit=40 553 119
               ->  Memoize  (cost=0.43..0.70 rows=1 width=30) (actual time=0.001..0.001 rows=1 loops=10001)
                     Cache Key: posts.channelid
                     Cache Mode: logical
                     Hits: 9002  Misses: 999  Evictions: 0  Overflows: 0  Memory Usage: 151kB
                     Buffers: shared hit=3996
                     ->  Index Scan using channels_pkey on channels  (cost=0.42..0.69 rows=1 width=30) (actual time=0.007..0.007 rows=1 loops=999)
                           Index Cond: ((id)::text = (posts.channelid)::text)
                           Buffers: shared hit=3996
 Planning:
   Buffers: shared hit=112
 Planning Time: 0.501 ms
 Execution Time: 23954.974 ms
(25 rows)
```



可以確認這邊使用的 idx 是否合理
![[IMG-article_making-a-postgres-query-1000-times-faster-20241003104948771.png]]



看到這個join可以拆拆看
![[IMG-article_making-a-postgres-query-1000-times-faster-20241003105001446.png]]


這邊表示有 4千萬筆資料...keep in mind
![[IMG-article_making-a-postgres-query-1000-times-faster-20241003105018533.png]]



這個語法可以讓你把 buffer 轉為多少 g
```

SELECT pg_size_pretty(40557115 * current_setting('block_size')::bigint);


```




作者把 where 拆開，懷疑 OR

先把下面
```sql


SELECT Posts.*, Channels.TeamId
  FROM Posts LEFT JOIN Channels ON Posts.ChannelId = Channels.Id
  WHERE Posts.CreateAt > ?1 -- <- only the first condition here
  ORDER BY Posts.CreateAt ASC, Posts.Id ASC
  LIMIT ?3;



```


result   33 ms!!!
```

mmdb=> EXPLAIN (ANALYZE, BUFFERS) SELECT Posts.*, Channels.TeamId FROM Posts LEFT JOIN Channels ON Posts.ChannelId = Channels.Id WHERE Posts.CreateAt > '1687424888405' ORDER BY Posts.CreateAt ASC, Posts.Id ASC LIMIT 10000;
QUERY PLAN
----------------------------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=1.14..1616.83 rows=10000 width=464) (actual time=0.347..33.077 rows=10000 loops=1)
   Buffers: shared hit=13949
   ->  Incremental Sort  (cost=1.14..9890887.64 rows=61217938 width=464) (actual time=0.346..32.054 rows=10000 loops=1)
         Sort Key: posts.createat, posts.id
         Presorted Key: posts.createat
         Full-sort Groups: 311  Sort Method: quicksort  Average Memory: 45kB  Peak Memory: 45kB
         Buffers: shared hit=13949
         ->  Nested Loop Left Join  (cost=1.00..7313757.02 rows=61217938 width=464) (actual time=0.053..25.892 rows=10001 loops=1)
               Buffers: shared hit=13949
               ->  Index Scan using idx_posts_create_at on posts  (cost=0.57..5781440.25 rows=61217938 width=461) (actual time=0.032..9.802 rows=10001 loops=1)
                     Index Cond: (createat > '1687424888405'::bigint)
                     Buffers: shared hit=9953
               ->  Memoize  (cost=0.43..0.70 rows=1 width=30) (actual time=0.001..0.001 rows=1 loops=10001)
                     Cache Key: posts.channelid
                     Cache Mode: logical
                     Hits: 9002  Misses: 999  Evictions: 0  Overflows: 0  Memory Usage: 151kB
                     Buffers: shared hit=3996
                     ->  Index Scan using channels_pkey on channels  (cost=0.42..0.69 rows=1 width=30) (actual time=0.007..0.007 rows=1 loops=999)
                           Index Cond: ((id)::text = (posts.channelid)::text)
                           Buffers: shared hit=3996
 Planning:
   Buffers: shared hit=112
 Planning Time: 0.440 ms
 Execution Time: 33.735 ms
(24 rows)

```



查另一邊
也很快 0.047 毫秒
```

SELECT Posts.*, Channels.TeamId
  FROM Posts LEFT JOIN Channels ON Posts.ChannelId = Channels.Id
  WHERE Posts.CreateAt = ?1 AND Posts.Id > ?2 -- <- only the second condition here
  ORDER BY Posts.CreateAt ASC, Posts.Id ASC
  LIMIT ?3;



agnivaltdb=> EXPLAIN (ANALYZE, BUFFERS) SELECT Posts.*, Channels.TeamId FROM Posts LEFT JOIN Channels ON Posts.ChannelId = Channels.Id WHERE Posts.CreateAt = '1687424888405' AND Posts.Id > 'tpomh9yu1tffmdp6dopobwuc9h' ORDER BY Posts.CreateAt ASC, Posts.Id ASC LIMIT 10000;
QUERY PLAN
--------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=0.99..17.03 rows=1 width=464) (actual time=0.015..0.015 rows=0 loops=1)
   Buffers: shared hit=4
   ->  Nested Loop Left Join  (cost=0.99..17.03 rows=1 width=464) (actual time=0.014..0.014 rows=0 loops=1)
         Buffers: shared hit=4
         ->  Index Scan using idx_posts_create_at_id on posts  (cost=0.57..8.59 rows=1 width=461) (actual time=0.013..0.013 rows=0 loops=1)
               Index Cond: ((createat = '1687424888405'::bigint) AND ((id)::text > 'tpomh9yu1tffmdp6dopobwuc9h'::text))
               Buffers: shared hit=4
         ->  Index Scan using channels_pkey on channels  (cost=0.42..8.44 rows=1 width=30) (never executed)
               Index Cond: ((id)::text = (posts.channelid)::text)
 Planning:
   Buffers: shared hit=112
 Planning Time: 0.437 ms
 Execution Time: 0.047 ms
(13 rows)
```



關鍵在於


本來的
->  Index Scan using idx_posts_create_at on posts  (cost=0.57..9889434.54 rows=61217938 width=461) (actual time=23920.930..23931.063 rows=10001 loops=1)
        **Filter: ((createat > '1687424888405'::bigint) OR ((createat = '1687424888405'::bigint) AND ((id)::text > 'tpomh9yu1tffmdp6dopobwuc9h'::text)))**
        Rows Removed by Filter: 40920000
        Buffers: shared hit=40553119

vs 

拿掉的
->  Index Scan using idx_posts_create_at on posts  (cost=0.57..5781440.25 rows=61217938 width=461) (actual time=0.032..9.802 rows=10001 loops=1)
        **Index Cond: (createat > '1687424888405'::bigint)**
        Buffers: shared hit=9953


ans:
一個用 filter -> 遍歷 `Posts` 表中的所有行，然後根據條件丟棄或保留它們
一個用 index cond (using idx) -> 查看索引並檢索它所包含的行




解法
1.
將原始查詢分成兩部分，獨立運行它們，然後在伺服器中收集結果

2.
It turns out there's a handy feature in PostgreSQL called row constructor comparisons that allows me to compare tuples of columns. That's exactly what we need. Instead of doing CreateAt > ?1 OR (CreateAt = ?1 AND Id > ?2), we can do (CreateAt, Id) > (?1, ?2). And the row constructor comparisons are lexicographical, meaning that it's semantically the same as what we had before!
ref: [delivered](https://dba.stackexchange.com/questions/241591/postgres-choosing-a-filter-instead-of-index-cond-when-or-is-involved)


所以只要調整為
```sql

SELECT Posts.*, Channels.TeamId
  FROM Posts LEFT JOIN Channels ON Posts.ChannelId = Channels.Id
  WHERE (Posts.CreateAt, Posts.Id) > (?1, ?2) -- <- lexicographical comparisons to the rescue!
  ORDER BY Posts.CreateAt ASC, Posts.Id ASC
  LIMIT ?3;

```


result ->  34 ms
會注意到 PostgreSQL 現在使用 Index Cond ，因此檢查索引以查找這些帖子，然後才套用過濾器。共享命中緩衝區降至 9955，僅 78 MiB。!
```
mmdb=> EXPLAIN (ANALYZE, BUFFERS) SELECT Posts.*, Channels.TeamId FROM Posts LEFT JOIN Channels ON Posts.ChannelId = Channels.Id WHERE (Posts.CreateAt, Posts.Id) > ('1687424888405', 'tpomh9yu1tffmdp6dopobwuc9h') ORDER BY Posts.CreateAt ASC, Posts.Id ASC LIMIT 10000;
                                                                           QUERY PLAN
-----------------------------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=1.15..1666.83 rows=10000 width=464) (actual time=0.366..34.053 rows=10000 loops=1)
   Buffers: shared hit=13951
   ->  Incremental Sort  (cost=1.15..10196977.36 rows=61217938 width=464) (actual time=0.365..33.031 rows=10000 loops=1)
         Sort Key: posts.createat, posts.id
         Presorted Key: posts.createat
         Full-sort Groups: 311  Sort Method: quicksort  Average Memory: 45kB  Peak Memory: 45kB
         Buffers: shared hit=13951
         ->  Nested Loop Left Join  (cost=1.00..7619846.74 rows=61217938 width=464) (actual time=0.059..26.840 rows=10001 loops=1)
               Buffers: shared hit=13951
               ->  Index Scan using idx_posts_create_at on posts  (cost=0.57..6087529.97 rows=61217938 width=461) (actual time=0.040..10.548 rows=10001 loops=1)
                     Index Cond: (createat >= '1687424888405'::bigint)
                     Filter: (ROW(createat, (id)::text) > ROW('1687424888405'::bigint, 'tpomh9yu1tffmdp6dopobwuc9h'::text))
                     Rows Removed by Filter: 2
                     Buffers: shared hit=9955
               ->  Memoize  (cost=0.43..0.70 rows=1 width=30) (actual time=0.001..0.001 rows=1 loops=10001)
                     Cache Key: posts.channelid
                     Cache Mode: logical
                     Hits: 9002  Misses: 999  Evictions: 0  Overflows: 0  Memory Usage: 151kB
                     Buffers: shared hit=3996
                     ->  Index Scan using channels_pkey on channels  (cost=0.42..0.69 rows=1 width=30) (actual time=0.007..0.007 rows=1 loops=999)
                           Index Cond: ((id)::text = (posts.channelid)::text)
                           Buffers: shared hit=3996
 Planning:
   Buffers: shared hit=112
 Planning Time: 0.471 ms
 Execution Time: 34.716 ms
(26 rows)
```




author 的經驗
1. 運行 `EXPLAIN` 時始終使用 `BUFFERS` 。它提供了一些對調查可能至關重要的數據。
2. 嘗試取得 `Index Cond` （在 MySQL 中稱為 `Index range scan` ）而不是 `Filter` 。
3. PostgreSQL 和 MySQL 會有不同的行為。