
### 加idx的考慮
[Understanding database Indexes in PostgreSQL](https://mastermind.dev/indexes-in-postgresql)
- 加idx的考慮
    - 常常查
    - 會被join
    - 會被sort
- 文章有一些可以查 idx bloat 的 query 可以try


### 建立 compound idx
[https://www.db-fiddle.com/f/awN5Kj5PGoBm1Q4Z5yqjUT/1](https://www.db-fiddle.com/f/awN5Kj5PGoBm1Q4Z5yqjUT/1)
If you want to filter only col-c just have idx for col-c,
the compound-idx is less perf than col-c (one col index)


### 建立 idx 的 prep
- use explain to know what it will do, as a ref
- create sample project using similar structure and try to run it to know
    - also can use this way to more accurate estimate the time it took by adding more rows
    - need to consider the harware side, cpu, memeoru, disk IO per, those effect the speed
- know how to use `pg_stat_progress_create_index` to monitor the progess
- create index take space, ensure the disk space is available
- it also impact the IO, need to monitor the process (it is better to do this in QAT and monitor that)
- do this at off-peak hrs
- ensure you have backup to rollback, if not, you need to take backup before doing this
- consider using `CREATE INDEX CONCURRENTLY



## 建立 idx 建議加上 `concurrently`

語法
`CREATE INDEX CONCURRENTLY idx_name ON table_name (column_name);`


- 作用： 建立 idx 時，不會 lock 其他讀寫的操作
	- default mode下， might be table lock (not sure, need check)
- 但, 可能會跑更久
- ref: https://docs.postgresql.tw/reference/sql-commands/create-index#tong-bu-jian-li-suo-yin



### idx 數據結構
related:
- [[cs61b B-Trees]]
- [[b+ tree]]

b-tree
以下操作
```

<
<=
=
>=
BETWEEN
IN
IS NULL
IS NOT NULL
LIKE 'foo%' 
~ '^foo'


```

hash
- "=" 會用到
- but 很多時候優化器還是會用 btree


git
幾何數據類型、網絡地址數據, 全文搜索 找最近鄰居
- `<<` 、 `&<` 、 `&>` 、 `>>` 、 `<<|` 、 `|&>` 、 `|>>` 、 `@>` 、 `<@` 、 `~=`


sp-gist
多媒體、電話路由、IP 路由或 GIS
`<< 、 >> 、 ~= 、 <@ 、 <b4> 或 |>>`


gin (**G**eneralized **In**verted Index)
- full text search
- 全文搜索
- 當您想要索引數組、hstore、jsonb 並實現全文搜索時，請考慮 GIN
- multiple val store in a single object, doc-based
    - like hstore, array, jsonb, range
