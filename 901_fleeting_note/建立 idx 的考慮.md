
- idx 越多, 寫入會變慢
	- 因為需要 update extra b+ tree
	- idx 表也佔空間，進而影響 vacumm 效能 (pg)
- idx 不是建了就會用，要看 plan 結果，因此不是亂建
- 選基數大的 col as idx
    - 基數小就是只有2類, like sex
    - [[what is 基數(Cardinality) in db]]
- 注意 idx 的 col 順序
	- [[postges Composite Index and Partial Index]]
- 請使用 `EXPLAIN` 語句可以幫助分析查詢計劃，確定是否會進行全表掃描
- 如果沒有 idx, 以下情況會 full table scan, 可以考慮評估以上因素後，給適合的欄位加上 idx
	- 如果 `status` 列沒有索引，則會導致全表掃描。
		- `SELECT * FROM orders WHERE status = 'shipped';`  
	- 使用`SELECT *
		- 全表掃描
	- 使用 `LIKE` 進行查詢，並且模式以通配符(`%`)開頭時，通常會導致全表掃描
		- `SELECT * FROM products WHERE name LIKE '%apple%';`
	- 當使用 `OR` 條件時，如果其中一個條件沒有索引，整個查詢可能會導致全表掃描
		- ``SELECT * FROM employees WHERE department_id = 1 OR job_title = 'Manager';``
	- 使用 `NOT` 條件也可能導致全表掃描，特別是當沒有適當的索引時
		- `SELECT * FROM customers WHERE NOT country = 'USA';`
	- 在沒有適當索引的情況下進行聚合查詢，例如 `COUNT`, `SUM`, `AVG` 等，可能會導致全表掃描
		- `SELECT COUNT(*) FROM transactions WHERE created_at > '2023-01-01';`
	- 當查詢中包含 `ORDER BY` 或 `GROUP BY` 且沒有索引時，數據庫可能需要進行全表掃描來獲取排序或分組的結果
		- `SELECT * FROM sales ORDER BY sale_date;`










ref:
- [30-13 之資料庫層的優化 - 索引設計與雷區 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10221971)