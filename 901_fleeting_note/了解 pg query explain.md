
# EXPLAIN and ANALYZE
- `EXPLAIN` + `ANALYZE` 會真正執行查詢。
	- 用在 prod 使用前請三思！
	- 可以用在 backup prod 來測試真實速度和看 ms
		- `ANALYZE` 還會提供以毫秒為單位的實際執行時間、實際列數和顯示該節點執行次數的迴圈計數
	    - `ANALYZE`：使用此關鍵字，`EXPLAIN` 不僅顯示計畫和 PostgreSQL 的估計值，還會**執行查詢**（因此請小心使用 `UPDATE` 和 `DELETE`！）
- 考慮快取。
    - 查詢執行時間總會有一定的差異，因為資料在第一次執行時可能不在快取中。這就是為什麼重複執行 `EXPLAIN ANALYZE` 幾次並觀察結果是否改變非常有價值的原因。
    - PG 目前還不知道如何清理快取。
- `EXPLAIN` 只提供成本
    - 估計查詢成本的單位是 artificial 的。
    - 1 表示 seq scan 讀取 8kB 頁面的成本
- **`BUFFERS`**
    - 顯示每個節點在快取中找到多少資料區塊 (`hit`)，有多少必須從磁碟 `read` 讀取，有多少被 `written` 寫入以及有多少被 `dirtied` 弄髒。
- **關注重點？**
    - 找出是那個節點執行時間佔大多數。
    - 找出長時間 seq scan。這些都是建立索引的理想候選者。
    - 找出估計列數與實際列數顯著不同的最低節點。很多時候，這就是效能不佳的原因。「顯著不同」通常意味著大約 10 倍的差距。

    

**2. 解讀計畫輸出：**

PostgreSQL 的 EXPLAIN 輸出通常是一個樹狀結構，從根節點開始，逐步向下展現查詢執行的步驟。 每個節點代表一個查詢操作，例如：

- **Seq Scan (順序掃描):** 表示資料庫會逐行掃描整個表來查找資料。 這是最慢的掃描方式，除非資料量很小，否則通常表示需要建立索引。
- **Index Scan (索引掃描):** 表示資料庫會使用索引來查找資料。 這是比順序掃描快得多的方法。 不同的索引類型（例如 B-tree, GiST, GIN）會有不同的掃描方式。
- **Bitmap Heap Scan (位圖堆積掃描):** 一種高效的掃描方式，特別適用於需要篩選大量資料的情況。 它先建立一個位圖來標記符合條件的資料列，然後再根據位圖掃描堆積。
- **Hash Join (雜湊 JOIN):** 一種結合多個表的 JOIN 方法，使用雜湊表來加速匹配。
- **Merge Join (合併 JOIN):** 另一種結合多個表的 JOIN 方法，需要先對資料進行排序。
- **Nested Loop Join (巢狀迴圈 JOIN):** 一種簡單但通常較慢的 JOIN 方法，會逐一迭代外部表中的每一行，並在內部表中查找匹配的行。
    

每個節點通常會包含以下資訊：

- **節點類型:** 例如 Seq Scan, Index Scan, Hash Join 等。
- **表名:** 參與此操作的表。
- **條件:** 篩選條件或 JOIN 條件。
- **估計成本:** PostgreSQL 估計此操作的成本。
- **估計列數:** PostgreSQL 估計此操作產生的列數。
- **實際執行時間 (僅限 EXPLAIN ANALYZE)**: 此操作實際執行的時間。
- **實際列數 (僅限 EXPLAIN ANALYZE)**: 此操作實際產生的列數。
    

**3. 關注重點：**
- **執行時間:** 找出執行時間最長的節點。 這些節點通常是效能瓶頸。
- **估計列數 vs. 實際列數:** 如果估計列數與實際列數差異很大（例如相差 10 倍以上），則表示 PostgreSQL 的估計可能不準確，這可能是效能問題的根源。
- **順序掃描:** 大量的 **Seq Scan 通常表示需要建立索引。
- **JOIN 方法:** 選擇不當的 JOIN 方法也會導致效能問題。


從裡面開始執行
![[IMG-了解 pg query explain-20250810210645416.png]]


# 怎麼看 cost
```
note:
(cost=1.12..1651763.11 rows=1 width=72)
```

- cost is Postgres computational units
- 啟動 cost  1.12
	- 這個成本考慮了查詢計劃的初始開銷，包括查詢執行的準備工作（例如，讀取索引或執行某些初始化操作）
- 總 cost     1651763.11
	- 總成本是從開始執行查詢到完成查詢所需的總成本，包括啟動成本和返回所有結果的成本
- `rows=1` 表示預計查詢將返回的行數。在這個例子中，預計只會返回一行結果
- `width=72` 表示每行的平均字節數(bytes)。這有助於估算查詢的內存需求
- 如果是 sort 的操作，啟動成本會比較高，因為排序需要額外的計算和內存 。排序需要將數據加載到內存中，進行排序，然後再返回結果，這會增加啟動成本。
- 如果是 seq scan, 啟動成本通常是0。因為順序掃描會直接從磁碟讀取數據，而不需要額外的計算或排序操作。因此，對於小型表或未經過排序的查詢，啟動成本通常接近於 `0`。




# 不一定上了 idx, planner 就一定會用
- pg 還不可以給 hint
- planner 會自己去判斷，如果 cost 比較高就不用，類似資料量不大，為何需要去idx表拿
- 譬如，如果你要拿 age > 10, 但是幾乎大多數的資料都是 age > 10, 那就沒必要去用 idx 表去查了，直接在原本的表用 seq scan 撈
- planner 會看統計資料，會知道資料分布，以此來決定



# 進階
[[what is db hash join, nested loop join, merge join]]


# link
- [How to read PostgreSQL query plan | by Minh Nguyen | Geek Culture | Medium](https://medium.com/geekculture/how-to-read-postgresql-query-plan-df4b158781a1)




# 分析網站
- [PlanSplainer](https://plansplainer.com/)
- [iBrB | explain.depesz.com](https://explain.depesz.com/s/iBrB#html)
- [https://explain.dalibo.com/](https://explain.dalibo.com/)
- [https://explain.tensor.ru/archive/explain/7ab4e26361511c1b4434502ebdceb059:0:2023-05-28#schema](https://explain.tensor.ru/archive/explain/7ab4e26361511c1b4434502ebdceb059:0:2023-05-28#schema)
- PS: 用了要小心是否會洩漏公司資訊


# to see
[Query Plan 案例入門. 只有實戰才能累積能力的技術 | by 古哥 | pgsql-tw | Medium](https://medium.com/pgsql-tw/query-plan-%E6%A1%88%E4%BE%8B%E5%85%A5%E9%96%80-f250ca7e6fe8)