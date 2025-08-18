---
aliases:
  - ddia storage
  - sst lsm
---

> Goal: get and retrive data


### the most simple db data strcuture in bash

simeple_db.sh
```sh fold

#!/bin/bash
## $1 is key $2 is value

## use >> append key-val to database file
## we use `,` to sepeatate key and value
db_set () {
  echo "$1,$2" >> database
}

## use grep to get the line in database
## then pipe and use sed to replace key into empty string (since we only need value)
## fianlly we only need to get one result
db_get () {
  grep "^$1," database | sed -e "s/^$1,//" | tail -n 1
}

```

- `db_set key value` to save key-val pair into `database`
- `db_get key` to get val
- use `,` to separate key and val
- new row insert by adding a new line

```bash

db_set 123456 '{"name":"London","attractions":["Big Ben","London Eye"]}' 

db_set 42 '{"name":"San Francisco","attractions":["Golden Gate Bridge"]}'

db_get 42
{"name":"San Francisco","attractions":["Golden Gate Bridge"]}


cat database
123456,{"name":"London","attractions":["Big Ben","London Eye"]}
42,{"name":"San Francisco","attractions":["Golden Gate Bridge"]}
42,{"name":"San Francisco","attractions":["Exploratorium"]}

```

- this is the **log strcutured**, LS
- when we say “log” here:
    - we are not specific say “application log”
    - we are saying **an append-only sequence of record**, could be human readbale or binary
- the probelm is that when this log getting big…
    - need to scan all to look up a key, O(N)
    - here we need an index 資料結構 to help -> 繼續看下一段
        - say if we have a b-tree help us, it’s become O(logN)
        - idx is a deried data, which help us increase query but come at a cost of slower write and increase space
- a "real" db also need to handle cocurrent write, crash receovery, clean up (avoid run out of disk space)…etc



### hash idx
- in-memory hashmap to record key-val
    - key 就是自己定義的
    - val 就是 ptr 要跳過去的offset
    - 這樣就可以快速的去讀 LS file 
    - so we can read the file begin the 64 byte offset and get the value
    - e.g. Riak’s Bitcask
![[IMG-storage and retrieval DB engine-20250810200243736.png]]


- What if we run out of disk?
	- 為了避免檔案太大，可以設定一個 max file size，超過就建立下一個檔案
	- 然後使用一個 cron job 定期去 清掉 duplicate key 來 save space, 我們只需要最新的 key
		- 因為我們本來就不會去 update key, 只會 append only -> 因此 file 很容易越來越大
![[IMG-storage and retrieval DB engine-20250810200245053.png]]


另外也可以把很多 file merge 成一個，這樣也避免太多檔案需要去找
![[IMG-storage and retrieval DB engine-20250810200246524.png]]

- Some issue to consider:
    - if we use binary format can save more space and lower time complexity
    - use a delete mark (tombstone) when process doing the compaction and know this key can be removed
    - periodically run snapshot, so when we need to run crash-recovery, no need to start from begin
    - need some checksum mechanism to detect partial write/result in the incident of db crash
	    - so if db crash, and we use checksum to check if the 特定時間內的資料都是完整的
    - one write thread and multiple read thread
- we got hash map, why not just use key to update the val?
    - write is just appending at end is very fast
        - much faster compared random seek in HD (a little fast in SSD)
        - is much easier to run in parallel, hard to do in random write → run parallel also boost speed
        - ez to do crash-recovery
        - ez to merge
- in-mem hash table 的 限制
	- The hash table must fit in memory, so if you have a very large number of keys, you’re out of luck. -> 如果資料量超級大 -> 這招就沒用了。
    - Range queries are not efficient
	    - For example, you cannot easily scan over all keys between kitty00000 and kitty99999 — you’d have to look up each key individu‐ ally in the hash maps.




### SST and LSM-tree (Log Structured Merge)

LSM tree used in *Bitcask, LevelDB, Google BigTable, RockDB, Cassandra (focus on write perf, noSQL mostly)*


what is Sorted String Table(SST)
- 就是上面 hash idx + append only DB 的進一步提昇
- 我們會讓 key is sorted and unqiue
- 寫入會慢一點，但是有如下好處


- since it is sorted, it is very easy and faster to compact, like below
	- like how we do merge sort, sort and merge
![[IMG-storage and retrieval DB engine-20250810200248028.png|690]]


2. 不需要把全部的 key 都放在 mem 了! 這很重要，因為這讓我可以可以存巨量資料
- you can have a sparse key and only need to put sparse key in mem
- like no need to put `handiwork` in mem,  to get `handiwork`, start look at `handbag` -> `102134` to find it
- 下面的灰色地方，就是 offset 之間，也可以 compressed 然後只有當我們需要scan 在解壓縮就好
![[IMG-storage and retrieval DB engine-20250810200249447.png]]


how to build and maintain SST? (also called Log-Structured Merge Tree algorithm)
- how to keep string sort
- disk 層可以用 b-tree,  memeory 層可以用 AVL樹或是紅黑樹
	- ref [[cs61b B-Trees]], [[cs61b Red Black Trees or LLRB]]
- 寫入
	- when you write, write into memory first (called memtable)
	- save into disk when memtable size over a given thredhold
- 讀取
	- when you read, check the memtable first, if can’t find, search the most recent disk segment and continue search to least recent segment
- bg process to run merge and compaction process
- use a log to do crash recovery and we can remove this log after saved into disk


LevelDB, RocksDB use this for their key-val store, Cassandra and Hbase also use similar logic in their store process.


Optimization
- it’s expensive if we can’t find a key after a lot of checking (first check memtable, recent disk segment and all the way to all segments) ⇒ have a bloom filter to check 是否不存在，如果不存在，就不用找了
	- ref: [[Bloom Filter 布隆過濾器]]
- compression optimisation:
	- 大小也分層設計 (size-tiered): small and recent sstable merge into old and bigger sstable
	- leveled: we assume more query for recent data, so new data go into more thiner SStable and have many level based on recent to least recent

throughput
- LSM tree can support high throughput
	- since it is sorted in disk, so it’s fast to merge and look up
	- the write is also sequential and append only


### B-tree
- related [[cs61b B-Trees]]
- [notion b-tree, b+ tree](https://www.notion.so/nture4388/b-tree-b-tree-43e9e29fe349466e989fa980cb0c236f?pvs=4)

PS: DDIA 這邊說的比較像是 B+ 樹

used a lot in many RMBD db (MySQL, PostgreSQL, CouchDB..etc)


- put data into a segment (4kb), a page
    - why 4kb,  this is to align to file system block, to reduce page refetch
- each page, we have keys and ptrs to children pages
    - lookup:
        - to find userId 251, first find ptr b/n 200~300
        - then that ptr go to child page which find ptr b/n 250~270
        - and then find 251 val
        - **val is only store at leaf page**
    - branching factor:
        - the ratio of ptr/ref in each page
        - normally we have hundreds of ptr/ref
    - update
        - like lookup, just find it and update its value
![[IMG-storage and retrieval DB engine-20250810200250532.png|585]]

- add new key
    - find the range to contain this new val
    - if the space is running out, split into two pages like below:
![[IMG-storage and retrieval DB engine-20250810200251538.png|632]]

- self-balance mechnism
- throw self-balance, n key will have log(n) tree height
	- usually 3-4 level deep in most case
- 4kb page and have 4 level deep can containing 256 TB
- **write require more operation compared to LSM so slower compared to LSM**
    - LSM write is just append a new record
    - b-tree is modify in place, so it need to seek to that pos first (in disk ) and modify it.
    - which also involve split page operation for self-balance, so we have multi operation in once.
- if the db crash or power outrage happened in between:
    - we can use write-ahead log (WAL, which is append-only) to log  before write and we can use this to recovery.
    - sometimes we also use latches (lightweight lock) to allow b-tree parallel operations.

### B-tree vs LSM-tree
- 應用場景
	- B-tree: most RDBMS
	- LSM: many noSQL use it
- 讀
	- B-tree: 
		- read is faster, only need fewer level to look up
		- B+ tree 同一個 page, 裡面去操作會是 Sequential access, 不過跳 node 就是 random access 了
	- LSM: compared to B-tree might be slower, but depend
- 寫
	- LSM:
		- 因為 append log, 比 B-tree 快
		- 因為 append log 是  Sequential writing, which is also needed for disk quick access
		- in some case, if the write throughput is so high and make the compaction process can’t keep up with, this will affect write result
	- B-tree
		- B-tree need to write to WAL and tree page
		- may need to split page, create node or re-balance
		- write slower compared to LSMT
- query latency 的可預測性
	- Btree: have more predictable query latency
	- LSM: it’s possible that the query is coming while the LMStree is running compaction, which affect the read pref and make query unpredictable.
- 空間
	- LSM: good space saving due to its compaction
	- B-tree: use page, and sometimes will have page framentation issue (some space in page is not used)

### Other data structure

see notion

### OLTP and OLAP

see notion

### Data warehouse

see notion

### column store

see notion


