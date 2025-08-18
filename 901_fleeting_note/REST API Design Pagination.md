使用 `OFFSET` 和 `LIMIT` 來實現分頁（pagination）時，當數據持續新增或刪除時，會遇到兩個問題

### **數據不一致性**

當你在查詢某一頁的數據時，如果在這個查詢過程中有新的數據被插入或刪除，這可能導致你在不同的請求中看到不一致的數據。
例如：
- 假設你請求第 1 頁的數據，查詢的 SQL 是 `SELECT * FROM table ORDER BY created_at LIMIT 10 OFFSET 0`。
- 假設你會看到 created_at, DESC, 最新前10筆
- 在這個查詢執行的過程中，假設有一條新的數據被插入
- 當你請求第 2 頁的數據時 `SELECT * FROM table ORDER BY created_at LIMIT 20 OFFSET 10`
- 這時候你想要拿到的原本的後10筆, 但是因為新數據進來，變成第一筆(DESC), 因此整個數據往後位移一筆


### **性能問題**

使用 `OFFSET` 會導致性能下降，尤其是在數據量很大的情況下。因為資料庫需要掃描所有的記錄直到達到指定的 OFFSET，這在資料持續增加的情況下會變得更慢。


為了克服這些問題，可以考慮：

1. **基於游標的分頁（Cursor-based Pagination）**：
    - 使用某個唯一的排序字段（如 ID 或時間戳）來進行分頁，而不是使用 OFFSET。
	    - 這樣可以避免因為數據變化而導致的重複或丟失問題。
	    - 因為 ID 或是這個唯一的排序字段可以上 idx, 也可以避免性能下降問題。
	- Josh metioned: 
	- nit: use lastKey
		- 如果 key 是 id，這可以是 where id > xxx 來處理  
		- 如果 key 是復合的東西，例如 createdAt + id 這可以是 where (createdAt = xxx AND id > yyy) or (createdAt > xxx) 類似這樣
```sql

SELECT * FROM table WHERE created_at > last_seen_created_at ORDER BY created_at LIMIT 10;

```    



# 為何 DB 的 OFFSET 的性能不是很好?

在資料庫中使用 `OFFSET` 進行查詢時，性能不佳的原因主要與資料庫如何處理這種查詢有關。雖然 `OFFSET` 本身並不是直接導致性能問題的唯一因素，但它確實會影響查詢的效率。以下是一些主要原因：

### 1. **全表掃描（Sequential Scan）**

當使用 `OFFSET` 時，資料庫通常需要從查詢的起始位置開始讀取資料，然後跳過指定的行數。這意味著資料庫可能需要進行全表掃描，特別是在沒有適當索引的情況下。這會導致性能下降，因為資料庫需要讀取大量的資料，即使最終只返回少量的結果。

### 2. **資料排序**

如果查詢需要對結果進行排序（例如使用 `ORDER BY`），資料庫在計算 `OFFSET` 時必須首先完成排序操作。這會增加額外的計算負擔，特別是當資料集很大時。

### 3. **記憶體和 I/O 負擔**

使用 `OFFSET` 可能會導致資料庫在記憶體和 I/O 操作上承受更大的負擔。當資料庫需要跳過大量的行時，它必須讀取這些行並將其丟棄，這會消耗額外的資源。

### 4. **索引的使用**

在某些情況下，資料庫可能無法有效利用索引來加速 `OFFSET` 查詢。即使有索引，資料庫仍然需要從索引中讀取資料，然後跳過指定的行數，這可能會導致性能下降。

### 5. **查詢計劃的複雜性**

使用 `OFFSET` 可能會使查詢計劃變得更加複雜，資料庫需要考慮如何最佳地執行查詢，這可能會導致額外的計算和資源消耗。

### 6. **大 `OFFSET` 值的影響**

當 `OFFSET` 的值很大時，性能問題會更加明顯。這是因為資料庫需要跳過大量的行，這會導致查詢的延遲顯著增加。



also can see 
[API Pagination: Making Billions of Products Scrolling Possible - YouTube](https://www.youtube.com/watch?v=14K_a2kKTxU&list=WL&index=2)

![[IMG-REST API Design Pagination-20250810200241464.png]]