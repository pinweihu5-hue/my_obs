---  
alias:  
creation_date: 2024-06-06 19:26  
tags: 
related:
- [[db_idx]]
- [[what is duckDB]]

---  


duckDB 可以從 aws S3 讀 parquet 檔案, 然後建立一個 "view" and save as XXX.db
PS: parquet is a col-orinted data foramt, optmized for read

```python

# Send
import duckdb
db = duckdb.connect("weird_rides.db")
db.sql("""
    CREATE VIEW weird_rides
    AS SELECT pickup_at, dropoff_at, trip_distance, total_amount
    FROM 's3://robotaxi-inc/daily-ride-data/*.parquet'
    WHERE fare_amount > 100 AND trip_distance < 10.0
""")
db.close()

```


然後你可以用 duckDB 直接從 s3 讀這個 local db file
因為是 dockdb + parquet, 可以是"部分讀" (只讀取需要的 col), 如果你有下 where condition, 真正抓下來的更少
```python

# Receive
import duckdb
conn = duckdb.connect()
conn.sql("""
ATTACH 's3://robotaxi-inc/virtual-datasets/weird_rides.db'
AS rides_db (READ_ONLY)
""")


conn.sql("SELECT * FROM rides_db.weird_rides LIMIT 5")
# ┌─────────────────────┬─────────────────────┬───────────────┬──────────────┐
# │      pickup_at      │     dropoff_at      │ trip_distance │ total_amount │
# │      timestamp      │      timestamp      │     float     │    float     │
# ├─────────────────────┼─────────────────────┼───────────────┼──────────────┤
# │ 2019-04-01 00:03:20 │ 2019-04-01 00:03:54 │           0.0 │       240.35 │
# │ 2019-04-01 00:16:16 │ 2019-04-01 00:16:21 │           0.0 │       138.36 │
# │ 2019-04-01 02:01:22 │ 2019-04-01 02:01:28 │           0.0 │       192.96 │
# │ 2019-04-01 06:26:44 │ 2019-04-01 06:27:14 │           0.0 │        115.3 │
# │ 2019-04-01 07:28:12 │ 2019-04-01 07:28:12 │           0.0 │        127.2 │
# └─────────────────────┴─────────────────────┴───────────────┴──────────────┘

```



衍生問題：
如果當初存到 bigquery partition 沒有切好
那我可以只拉幾個 col 下來
然後利用 duckdb 用上面的讀取嗎?