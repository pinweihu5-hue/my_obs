[[db_idx]]
[[tx and lock]]
[[db-udemy-acid, ACID]]



---

- default isolation level is `read_commited_level`
	- read_commited_level can prevet dirty_read
	- dirty_read means?
		- tx1 begin
		- tx2 begin and write sth, not commit
		- tx1 can read stuff in which tx2 does
	- dir

- what `read_commited_level` **CAN NOT prevent**
	- non_repeatable_read
		- if happen use `begin transaction isolation level repeatable read;` to solve
	- serialize_abnomly: 
		- many txs and their order can mess up
		- to solve? use `begin transaction isolation level serializable;`