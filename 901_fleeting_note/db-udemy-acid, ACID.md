related:
[[pg isolation level]]
[[db-udemy-acid, ACID]]
[[DDIA DB transaction 交易]]

---


# atomic → abt all success or all fail
    
    

示範  rollback

我們會跑一個 tx, 做一些事情，還沒 commit...
然後我們會 exit psql, 然後回到 db, 應該不會寫入
```sql
# re4388 @ 1301527-NB in ~/tmp [12:33:26]
$ docker run --name pgacid -d -e POSTGRES_PASSWORD=postgres postgres:latest                     [Tue Nov 29 12:33:26 2022]

# re4388 @ 1301527-NB in ~/tmp [15:55:05]
$ docker ps                                                                                  [Tue Nov 29 15:55:05 2022]
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS      NAMES
0a9cb8f1a28a   postgres:13   "docker-entrypoint.s…"   7 minutes ago   Up 7 minutes   5432/tcp   pgacid

# re4388 @ 1301527-NB in ~/tmp [16:02:54] C:126
$ docker exec -it pgacid psql -U postgres                                                    [Tue Nov 29 16:02:54 2022]
psql (13.9 (Debian 13.9-1.pgdg110+1))
Type "help" for help.

postgres=# create table products (pid serial primary key, name text, price float,
inventory int);
CREATE TABLE

postgres=# create table sales (saleid serial primary key,
pid integer, price float,quantity integer);
CREATE TABLE

postgres=# insert into products (name, price, inventory) values (
'phone', 999.99, 100);
INSERT 0 1
postgres=# select * from products;

 pid | name  | price  | inventory
-----+-------+--------+-----------
   1 | phone | 999.99 |       100
(1 row)

postgres=# begin transaction;
BEGIN
postgres=*# update products set inventory = inventory-10;
UPDATE 1
postgres=*# select * from products;
 pid | name  | price  | inventory
-----+-------+--------+-----------
   1 | phone | 999.99 |        90
(1 row)

# just leave during a tx and we later check if it will auto rollback

postgres=*# exit

# re4388 @ 1301527-NB in ~/tmp [16:08:49]
$ docker exec -it pgacid psql -U postgres        [Tue Nov 29 16:08:49 2022]
psql (13.9 (Debian 13.9-1.pgdg110+1))
Type "help" for help.

# it does rollback
# the atomic works
postgres=# select * from products;
 pid | name  | price  | inventory
-----+-------+--------+-----------
   1 | phone | 999.99 |       100
(1 row)

```


# isolation is about occurrent!!

## pg default level: commited_read
    

demo no-dirty-read
tx1寫入，但是還沒 commit
tx2 會看不到tx1還沒 commit 的東西

實驗
```bash
##################################
###########################

postgres=# begin transaction;
BEGIN
postgres=*# update products set inventory = inventory-10;
UPDATE 1
postgres=*# insert into sales(pid, price, quantity)
values (1, 999.99, 10);
INSERT 0 1

postgres=*# select * from sales;
 saleid | pid | price  | quantity
--------+-----+--------+----------
	  1 |   1 | 999.99 |       10
(1 row)

# before commit switch to another terminal 

╰─❯ docker exec -it pgacid psql -U postgres

postgres=# select * from sales;
 saleid | pid | price | quantity
--------+-----+-------+----------
(0 rows)

# as above, we see nothing, so this is iolation took efect
# we have not yet see data since the first terminal tx is not commit 
# or rollback yet
# if we can see it, that's a dirty read
# and pg, by default, prevent it

# switch to terminal 1
postgres=*# commit;
COMMIT

# and check sales in terminal 2, you will see the commit
postgres=# select * from sales;
 saleid | pid | price  | quantity
--------+-----+--------+----------
	  1 |   1 | 999.99 |       10
(1 row)

=====

```
    


## default isolation level is 沒有 repeatable read 保證
    

tx1 會寫入，但還沒 commit
tx1 接著 query, 看到一些資料 state:A1
tx2 會寫入，改變資料(state become A2)，然後完成 commit
因為 tx2 已經 commit, 因此 tx1 可以query again 然後看到A2
A1 !== A2 -> 沒有 repeatable read 保證

```bash

# insert some new data in terminal 1
postgres=# insert into products (name, price, inventory) 
values ('earbud', 99.99, 160);
INSERT 0 1

postgres=# select * from products;
 pid |  name  | price  | inventory
-----+--------+--------+-----------
   1 | phone  | 999.99 |        90
   2 | earbud |  99.99 |       160
(2 rows)

postgres=# insert into sales(pid, price, quantity) values (1, 999.99, 5);
insert into sales(pid, price, quantity) values (1, 999.99, 5);
insert into sales(pid, price, quantity) values (1, 99.99, 10);
insert into sales(pid, price, quantity) values (1, 89.99, 10);
insert into sales(pid, price, quantity) values (1, 79.99, 20);

INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1

postgres=# select * from sales;
 saleid | pid | price  | quantity
--------+-----+--------+----------
	  1 |   1 | 999.99 |       10
	  2 |   1 | 999.99 |        5
	  3 |   1 | 999.99 |        5
	  4 |   1 |  99.99 |       10
	  5 |   1 |  89.99 |       10
	  6 |   1 |  79.99 |       20
(6 rows)

# in term1, let's have a report

postgres=# begin transaction;
BEGIN

postgres=*# select pid, count(pid) from sales group by pid;
 pid | count
-----+-------
   1 |     6
(1 row)

# before we run below query in ter1 ...let's go to terminal2
postgres=*# select pid, price, quantity from sales;

# go terminal 2
postgres=# begin transaction;
BEGIN
postgres=*# insert into sales(pid, price, quantity) values(1, 999.99, 10);
INSERT 0 1
postgres=*# update products set inventory = inventory-10 where pid=1;
UPDATE 1
postgres=*# commit;
COMMIT

# go back to ter1 and hit enter
postgres=*# select pid, price, quantity from sales;
 pid | price  | quantity
-----+--------+----------
   1 | 999.99 |       10
   1 | 999.99 |        5
   1 | 999.99 |        5
   1 |  99.99 |       10
   1 |  89.99 |       10
   1 |  79.99 |       20
   1 | 999.99 |       10
(7 rows)

# the total count for pid1 is 7 not 6!!!
# this report is not consistent
# we have dirty read?!, pg, by default, dones't prevent this

pg default isolation level is not fully MVCC (multiple version concurrent control)
so tx1 can read commited tx2's result (at this time, tx1 is not commit yet) and having
a inconsistent result (since tx2 change the tx1 result)

# the isolation is not fully isolation, we got many level of isolation
# to prevent this, we need to have repeatable read level of isolation

```
    

## isolation repeatable read level
    

我們可以設定更高 isolation level 來預防

```bash
# let's rollback the terminal 1 of inconsist result
postgres=*# rollback;
ROLLBACK

# the default is read commited, we use a higher level here

postgres=# begin transaction isolation level repeatable read;
BEGIN
postgres=*# select pid, count(pid) from sales group by pid;
 pid | count
-----+-------
   1 |     7
(1 row)

# before run below, again, go to terminal 2 to have a concurrent case
postgres=*# select pid, price, quantity from sales;

# in ter2
postgres=# begin transaction;
BEGIN
postgres=*# insert into sales(pid, price, quantity) values(1, 999.99, 10);
INSERT 0 1
postgres=*# update products set inventory = inventory-10 where pid=1;
UPDATE 1
postgres=*# commit
;
COMMIT

# back to ter1
# let's hit enter

postgres=*# select pid, price, quantity from sales;
 pid | price  | quantity
-----+--------+----------
   1 | 999.99 |       10
   1 | 999.99 |        5
   1 | 999.99 |        5
   1 |  99.99 |       10
   1 |  89.99 |       10
   1 |  79.99 |       20
   1 | 999.99 |       10
(7 rows)

# we still have 8 rows, even another tx add a new one to make it 8!!\
# we have isolation this time!
# it's pg MVCC, it is more expensive than read-commited level

# we can go to ter2
# we have 8 rows
postgres=# select pid, price, quantity from sales;
 pid | price  | quantity
-----+--------+----------
   1 | 999.99 |       10
   1 | 999.99 |        5
   1 | 999.99 |        5
   1 |  99.99 |       10
   1 |  89.99 |       10
   1 |  79.99 |       20
   1 | 999.99 |       10
   1 | 999.99 |       10
(8 rows)

# we can back to ter1 and commit, we will see 8 rows
postgres=*# commit;
COMMIT
postgres=# select pid, price, quantity from sales;
 pid | price  | quantity
-----+--------+----------
   1 | 999.99 |       10
   1 | 999.99 |        5
   1 | 999.99 |        5
   1 |  99.99 |       10
   1 |  89.99 |       10
   1 |  79.99 |       20
   1 | 999.99 |       10
   1 | 999.99 |       10
(8 rows)

```
    
# durability
    

as long as you hit commit → data in DB

實驗

```bash
# at ter1, let's be prepaed to kill the container

postgres=# \q
╭─ ~ ───────────────────────────────────────────────────────────────────────────────────── 34m 16s 20:57:47 ─╮
╰─❯ docker stop pgacid <---not yet hit enter

# in ter2

postgres=# begin transaction;
BEGIN
postgres=*# insert into sales(pid, price, quantity) values(3, 999.99, 10);
INSERT 0 1
postgres=*# commit;

# as long as we hit enter to commit in ter2
# we go back to ter1 and also hit enter to stop the container

# let's go back to cehck if pid=3 is really commit?
# yes!  it is commited into db

# let's durability

# ps:this not always the case, some db might write to memory first
# and not flush to the disk immeditatly, in this case...the above will not go to db

# or you have some config wrong and don't make this durability happen!

╰─❯ docker start pgacid                                                                                     ─╯
pgacid
╭─ ~ ───────────────────────────────────────────────────────────────────────────────────────────── 20:59:29 ─╮
╰─❯ docker exec -it pgacid psql -U postgres                                                                 ─╯
psql (15.1 (Debian 15.1-1.pgdg110+1))
Type "help" for help.

postgres=# select pid, price, quantity from sales;
 pid | price  | quantity
-----+--------+----------
   1 | 999.99 |       10
   1 | 999.99 |        5
   1 | 999.99 |        5
   1 |  99.99 |       10
   1 |  89.99 |       10
   1 |  79.99 |       20
   1 | 999.99 |       10
   1 | 999.99 |       10
   2 | 999.99 |       10
   3 | 999.99 |       10
(10 rows)
```




- [ ]  以下可能需要多看懂

# phantom read

tx1 and tx2 的條件被因為寫入結果打破造成的不一致
a inconsistent result in the same tx while another tx is happening


不太懂…need to rewatch XD

```bash

# setup the data

postgres=# create table sale2 (id serial primary key, pid integer, price float, date date not null);
CREATE TABLE

postgres=# insert into sale2(pid, price, date)
values (1, 10, '2021-02-02');
INSERT 0 1

postgres=# select * from sale2;
 id | pid | price |    date
----+-----+-------+------------
  1 |   1 |    10 | 2021-02-02
(1 row)

postgres=# insert into sale2(pid, price, date)
values (1, 10, '2021-02-03');
INSERT 0 1

postgres=# insert into sale2(pid, price, date)
values (1, 10, '2021-02-04');
INSERT 0 1

postgres=# insert into sale2(pid, price, date)
values (1, 5, '2021-02-03');
INSERT 0 1

postgres=# insert into sale2(pid, price, date)
values (1, 5, '2021-02-03');
INSERT 0 1

postgres=# select * from sale2;
 id | pid | price |    date
----+-----+-------+------------
  1 |   1 |    10 | 2021-02-02
  2 |   1 |    10 | 2021-02-03
  3 |   1 |    10 | 2021-02-04
  4 |   1 |     5 | 2021-02-03
  5 |   1 |     5 | 2021-02-03
(5 rows)

# go to ter2
# do some update
postgres=# insert into sale2(pid, price, date) values (1, 15, '2021-02-07');
INSERT 0 1

# go back ter1
# do the query again, and the report result change
# 上面 ter1 看到原本的加總是 40, 但是 ter2 update 後, ter1 進行另一個加總計算，變成 55 -> 不一致
postgres=*# select pid, sum(price) from sale2 group by pid;
 pid | sum
-----+-----
   1 |  55
(1 row)

```



# serializable


最嚴格的 pg 隔離 level
基本上就是排除所有 cocurrent issue
除非 pg bug or rare case
可以解決上面的問題
```bash

# go to ter1

postgres=*# rollback;
ROLLBACK
postgres=# begin transaction isolation level serializable;
BEGIN
postgres=*# select pid, sum(price) from sale2 group by pid;
 pid | sum
-----+-----
   1 |  55
(1 row)

# go to ter2, do some update
postgres=# insert into sale2(pid, price, date)
values (1, 100, '2021-02-07');
INSERT 0 1

# back to ter1, due to serializable level of isolation
# the query result is isolated and not effected
postgres=*# select pid, sum(price) from sale2 group by pid;
 pid | sum
-----+-----
   1 |  55
(1 row)

# in pg, repeatable read already can prevent the phantom read
# situation show above
# this is something pg special, other db will ned to use
# serializable level to prevent phantom read

```


- need serializable and repeatable read can’t handle
    
    ```bash
    # setup the table
    
    postgres=# create table test (id integer, t varchar);
    CREATE TABLE
    postgres=# insert into test(id, t) values (12, 'b');
    INSERT 0 1
    postgres=# insert into test(id, t) values (13, 'b');
    INSERT 0 1
    postgres=# insert into test(id, t) values (10, 'a');
    INSERT 0 1
    postgres=# insert into test(id, t) values (11, 'a');
    INSERT 0 1
    postgres=# select * from test;
     id | t
    ----+---
     12 | b
     13 | b
     10 | a
     11 | a
    (4 rows)
    
    # in ter1
    postgres=# begin transaction isolation level repeatable read;
    BEGIN
    postgres=*# update test set t='a' where t='b';
    UPDATE 2
    postgres=*# select * from test;
     id | t
    ----+---
     10 | a
     11 | a
     12 | a
     13 | a
    (4 rows)
    
    # in ter2
    postgres=# begin transaction isolation level repeatable read;
    BEGIN
    postgres=*# update test set t='b' where t='a';
    UPDATE 2
    postgres=*# select * from test;
     id | t
    ----+---
     12 | b
     13 | b
     10 | b
     11 | b
    (4 rows)
    
    # if we go back to ter1 and commit
    postgres=*# commit;
    COMMIT
    postgres=# select * from test;
     id | t
    ----+---
     10 | a
     11 | a
     12 | a
     13 | a
    (4 rows)
    
    # and go to ter2 and commit
    postgres=*# commit;
    COMMIT
    postgres=# select * from test;
     id | t
    ----+---
     12 | a
     13 | a
     10 | b
     11 | b
    (4 rows)
    
    # if you back to ter1 and query again:
    postgres=# select * from test;
     id | t
    ----+---
     12 | a
     13 | a
     10 | b
     11 | b
    (4 rows)
    
    # since ter2 is commit later, and we should expect that
    # all t shall be 'b' but in this case, it's not
    # the two tx update intervean together and repeatable read
    # didn't full isolated them
    
    # let's try use serializable level
    
    # in ter 1
    postgres=# begin transaction isolation level serializable;
    BEGIN
    
    # in ter2
    postgres=# begin transaction isolation level serializable;
    BEGIN
    
    # back to ter1
    # we will do the same and commit, to make all to a
    postgres=*# select * from test;
     id | t
    ----+---
     12 | a
     13 | a
     10 | b
     11 | b
    (4 rows)
    
    postgres=*# update test set t='a' where t='b';
    UPDATE 2
    postgres=*# select * from test;
     id | t
    ----+---
     12 | a
     13 | a
     10 | a
     11 | a
    (4 rows)
    
    postgres=*# commit;
    COMMIT
    postgres=# select * from test;
     id | t
    ----+---
     12 | a
     13 | a
     10 | a
     11 | a
    (4 rows)
    
    # back to ter2 and do the same
    
    postgres=*# select * from test;
     id | t
    ----+---
     12 | a
     13 | a
     10 | b
     11 | b
    (4 rows)
    
    postgres=*# update test set t='b' where t='a';
    UPDATE 2
    postgres=*# select * from test;
     id | t
    ----+---
     10 | b
     11 | b
     12 | b
     13 | b
    (4 rows)
    
    # we will see it abort and show a very clear explanation!
    postgres=*# commit;
    ERROR:  could not serialize access due to read/write dependencies 
    among transactions
    DETAIL:  Reason code: Canceled on identification as a pivot, 
    during commit attempt.
    HINT:  The transaction might succeed if retried.
    
    # so if you care about tx and if this can happen
    # in this case, you need to have retry mechanism in place!
    
    ```