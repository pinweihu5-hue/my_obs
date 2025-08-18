[[db_idx]]
[[DuckDB, parquet, S3]]

---

- 算是 OLAP
	- A, 分析，一般 pg, mysql會分到 OLTP, t 是交易
	- col 儲存，因此可以 "只" 讀寫 需要處理的 column
- 它設計用於放在(嵌入)本地運行的環境, 不需要獨立部屬
	- 可以想像為 sqlite, 因此是 sqlite版本的 OLAP
	- 適合用於本地數據處理
	- 輕量應用
- 支持 sql 語法
- 跨平台


--

my note

感覺就是適合要分析的情境
你需要搞大量資料下來本地進行分析

  
