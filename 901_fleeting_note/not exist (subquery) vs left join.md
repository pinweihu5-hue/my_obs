

緣起
[Sign in · GitLab](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5206)


比較這兩個哪一個比較快


sql1
use subquery, not exist
```sql

SET search_path = 'hermes-qat';
EXPLAIN ANALYZE SELECT p.*
FROM "Promotion" p
WHERE
      p."exchangeEndAt" < now()
    AND p."archivedAt" IS NULL
    AND NOT EXISTS (
        SELECT
            d
        FROM
            "PromotionDetail" d
        WHERE
            d."promotionId" = p.id
    )
ORDER BY
      p.id ASC
LIMIT
      5000;


```




執行計畫
```js 

Limit  (cost=58448.81..58460.67 rows=4742 width=2408) (actual time=1073.887..1074.407 rows=5000 loops=1)
  ->  Sort  (cost=58448.81..58460.67 rows=4742 width=2408) (actual time=1073.885..1074.208 rows=5000 loops=1)
        Sort Key: p.id
        Sort Method: top-N heapsort  Memory: 1667kB
        ->  Nested Loop Anti Join  (cost=0.42..53231.28 rows=4742 width=2408) (actual time=0.623..928.244 rows=703235 loops=1)
              ->  Seq Scan on "Promotion" p  (cost=0.00..48229.99 rows=4965 width=2408) (actual time=0.567..323.361 rows=994446 loops=1)
                    Filter: (("archivedAt" IS NULL) AND ("exchangeEndAt" < now()))
                    Rows Removed by Filter: 131279
              ->  Index Only Scan using "index_promotionId" on "PromotionDetail" d  (cost=0.42..6.06 rows=10 width=8) (actual time=0.000..0.000 rows=0 loops=994446)
                    Index Cond: ("promotionId" = p.id)
                    Heap Fetches: 291211
Planning time: 1.402 ms
Execution time: 1074.957 ms

```



sql2
use join
```sql

SET search_path = 'hermes-qat';
EXPLAIN ANALYZE SELECT p.*
FROM "Promotion" p
LEFT JOIN "PromotionDetail" d ON p.id = d."promotionId"
WHERE p."exchangeEndAt" < now()
  AND p."archivedAt" is null
  AND d IS NULL
ORDER BY p.id ASC
LIMIT 5000;

```

```js

Limit  (cost=78791.64..78791.70 rows=25 width=2408) (actual time=1579.642..1580.109 rows=5000 loops=1)
  ->  Sort  (cost=78791.64..78791.70 rows=25 width=2408) (actual time=1579.640..1579.892 rows=5000 loops=1)
        Sort Key: p.id
        Sort Method: top-N heapsort  Memory: 1667kB
        ->  Nested Loop Left Join  (cost=0.42..78791.06 rows=25 width=2408) (actual time=1.916..1431.629 rows=703235 loops=1)
              Filter: (d.* IS NULL)
              Rows Removed by Filter: 387839
              ->  Seq Scan on "Promotion" p  (cost=0.00..48229.99 rows=4965 width=2408) (actual time=1.241..501.012 rows=994446 loops=1)
                    Filter: (("archivedAt" IS NULL) AND ("exchangeEndAt" < now()))
                    Rows Removed by Filter: 131279
              ->  Index Scan using "index_promotionId" on "PromotionDetail" d  (cost=0.42..6.06 rows=10 width=145) (actual time=0.001..0.001 rows=0 loops=994446)
                    Index Cond: (p.id = "promotionId")
Planning time: 1.134 ms
Execution time: 1580.643 ms


```



NOT EXISTS 版本使用子查詢來檢查是否存在匹配的 PromotionDetail 記錄。
LEFT JOIN 版本通過外部連接和檢查 NULL 值來實現相同的目的。


效能考慮：
b. 數據掃描：

NOT EXISTS 版本可能對每個 Promotion 記錄執行一次子查詢。
LEFT JOIN 版本一次性連接兩個表，可能減少了數據庫的訪問次數。

c. 數據量影響：
- 當 PromotionDetail 表比 Promotion 表大得多時，NOT EXISTS 可能更有效。
	- 對於 Promotion 表中的每一行，檢查 PromotionDetail 表是否有匹配的行。一旦找到匹配的行，就停止搜索並排除該 Promotion 記錄。 -> Early Termination -> 因此如果 PromotionDetail 很大, 不需要掃過那麼大的表
- 當兩表大小相近或 Promotion 表更大時，LEFT JOIN 可能更有效。
	- 使用 LEFT JOIN 時，數據庫通常只需要掃描每個表一次。相比之下，NOT EXISTS 可能需要對 Promotion 表中的每一行都去查詢 PromotionDetail 表，這可能導致多次掃描。


內存使用優化：
- 現代數據庫系統在執行 JOIN 操作時有很好的內存管理機制。
- 當 Promotion 表較大時，數據庫可以將較小的 PromotionDetail 表加載到內存中，加速連接操作。


並行處理：
- LEFT JOIN 操作通常更容易被數據庫引擎並行化處理。
- 這在處理大型表時特別有優勢，可以充分利用多核心處理器。

批處理效率：
- LEFT JOIN 允許數據庫一次性處理大量數據。
- 這種批處理方式通常比 NOT EXISTS 的逐行處理更有效率，特別是在處理大量數據時。


減少隨機 I/O：
- NOT EXISTS 可能導致大量的隨機 I/O 操作，特別是當 Promotion 表很大時。
- LEFT JOIN 則可以通過順序掃描和高效的連接算法來減少隨機 I/O。

d. 數據庫優化器：
現代數據庫的查詢優化器通常能夠很好地處理這兩種形式，有時甚至會將它們轉換為相同的執行計劃。





網路分析
subquery ->  [Explain PostgreSQL](https://explain.tensor.ru/archive/explain/7bd0621f702ec11ec7f4ef92c7a91b6a:0:2024-07-25#visio)
![[IMG-not exist (subquery) vs left join-20241003104934162.png]]


join -> [Explain PostgreSQL](https://explain.tensor.ru/archive/explain/b220c7a412953596dc9e13a3d5e3898a:0:2024-07-25#visio)
![[IMG-not exist (subquery) vs left join-20241003104948989.png]]


![[IMG-not exist (subquery) vs left join-20241003105015260.png]]