[[db_idx]]


[Maciej Walkowiak | PostgreSQL and UUID as primary key](https://maciejwalkowiak.com/blog/postgres-uuid-primary-key/)


- Use uuid type not string type when choose using uuid as pk
- Use uuid v7 (not the default v4), which is time sorted and you shall use it when decide to use uuid as Postgres pk ( pk index only can use b tree and which is bad at not sorted uuid like v4)
	- about 2x-3x perf dif when insert between v4 and v7, but do you own experiment


also see this: [Postgres: Opinionated Rules-of-Thumb and Gotchas | Hacker News](https://news.ycombinator.com/item?id=38429670)
- not use uuid as pk if you need perf
	- context is imporant, use uuid if you will have possibly need to merge data
- To represent a point in time, you almost always want `timestamptz`, not `timestamp`. Counterintuitively, the latter is timezone-dependent on some clusters.
	- ???? how to prove?
- The default transaction mode is not fully isolated (the "I" in "ACID"), but the fully I mode is often too slow to be useful, so learn to use the default mode safely. 
	- See https://www.postgresql.org/docs/current/transaction-iso.html
