


# 如何快速確認二個 Table 是否相同

如果這兩個查詢都返回空結果，則表示兩個表格的資料相同。
```sql

SELECT * FROM table_a
EXCEPT
SELECT * FROM table_b;

SELECT * FROM table_b
EXCEPT
SELECT * FROM table_a;

```



這個查詢會返回在一個表格中存在但在另一個表格中不存在的所有行。
```sql

SELECT *
FROM table_a
FULL OUTER JOIN table_b ON table_a.id = table_b.id -- 假設 id 是主鍵
WHERE table_a.* IS NULL OR table_b.* IS NULL;

```




# 請對一個表進行 partition, 按日為單位進行切割

### 1. 創建父表

首先，你需要創建一個父表，這個表將不直接存儲資料，而是作為所有子表的基礎。

```sql

CREATE TABLE your_table (
    id SERIAL PRIMARY KEY,
    data TEXT,
    create_time TIMESTAMP WITH TIME ZONE
) PARTITION BY RANGE (create_time);

```

### 2. 創建子表

接下來，你需要為每一天創建一個子表。
這些子表將根據 `create_time` 的範圍進行分區。以下是創建某一天的子表的範例：
```sql

CREATE TABLE your_table_2023_01_01 PARTITION OF your_table
FOR VALUES FROM ('2023-01-01 00:00:00') TO ('2023-01-02 00:00:00');

CREATE TABLE your_table_2023_01_02 PARTITION OF your_table
FOR VALUES FROM ('2023-01-02 00:00:00') TO ('2023-01-03 00:00:00');

```
你可以根據需要重複這個步驟來創建更多的子表。


### 3. 自動創建子表
如果你希望自動化子表的創建，可以**使用 PostgreSQL 的函數和觸發器**。
這樣可以在插入資料時自動檢查是否需要創建新的子表。以下是一個簡單的範例：
```sql

CREATE OR REPLACE FUNCTION create_partition() RETURNS TRIGGER AS $$
DECLARE
    partition_name TEXT;
    partition_date DATE;
BEGIN
    partition_date := DATE(NEW.create_time);
    partition_name := 'your_table_' || TO_CHAR(partition_date, 'YYYY_MM_DD');

    EXECUTE format('CREATE TABLE IF NOT EXISTS %I PARTITION OF your_table FOR VALUES FROM (%L) TO (%L)',
                   partition_name,
                   partition_date,
                   partition_date + INTERVAL '1 day');

    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER create_partition_trigger
BEFORE INSERT ON your_table
FOR EACH ROW EXECUTE FUNCTION create_partition();



```
### 4. 插入資料

現在，當你插入資料時，觸發器會自動檢查並創建所需的分區：
```sql

INSERT INTO your_table (data, create_time) VALUES ('sample data', '2023-01-01 12:00:00');
INSERT INTO your_table (data, create_time) VALUES ('sample data', '2023-01-02 12:00:00');

```
### 5. 查詢資料

查詢資料時，你可以直接查詢父表，PostgreSQL 會自動處理分區的查詢：
```sql

SELECT * FROM your_table WHERE create_time >= '2023-01-01' AND create_time < '2023-01-03';

```