[[code_idx]]

---

notion: [Notion – The all-in-one workspace for your notes, tasks, wikis, and databases.](https://www.notion.so/nture4388/Algo-19b9faf2c1424838aad50c8142aad791?pvs=4)


[[what is p, np 問題]]



[Leetcode 刷題學習筆記 -- 心得統整 - HackMD](https://hackmd.io/@meyr543/r1skFcvgY)



[Hash Tables, Associative Arrays, and Dictionaries (Data Structures and Optimization) - YouTube](https://www.youtube.com/watch?v=S5NY1fqisSY)
- hash map -> use a hash fn to put stuff in one bucket
	- you can think the array idx is the key in hash map
		- but in array world, you need to iterate to find the element in a array (if you don't know the idx), in hashmap, you can directly get the element in the bucket(O(1))
	- a simple hash fn can be the length of your stuff
	- a good hash fn can put into buckets distributed well, and less collision
		- [choose a hash fn - Wikipedia](https://en.wikipedia.org/wiki/Hash_table#Choosing_a_hash_function)
	- 2 way to handel collision
		- chainning, put "many" stuff in on bucket with a data strcutre, can be linked list, dynamica array, tree, any data structure
			- [Separate_chaining - Wikipedia](https://en.wikipedia.org/wiki/Hash_table#Separate_chaining)
		- open addressing, still put '1' stuff in bucket, if you find the bucket is full, find the next bucket
			- how to find? linear probing, quadratic probing..etc
			- [Open_addressing - Wikipedia](https://en.wikipedia.org/wiki/Hash_table#Open_addressing)
			- pro and con?
				- if you use open addressing + linaer probing, you may faster when begin due to cache locality, but slower when you data is become big and most bucket is full (since u need to find many many time and then find a empty spot)
				- keep a eye a "load factor", common load factor is 0.75 and you need to "rehash"  (you can do it all once or do it incrementally) and use a bigger memory space
					- tradeoff -> increase ur mem usage, but you gain speed
	- what is assosicate array(java), dicnotionarray(python), map(js) related to hash function?
		- hash function is a specific implementation
		- associate array(java's hashmap) and dict(), JS's obj are ADT in high level lang, we define ADT's spec, how they act, we don't specifcy their impl details, can use hashmap or other way
	- perf on hashmap vs LL/array
		- hashtable: 
			- O(1) for insert, delete, lookup on average
		- LL/array:  
			- insert and delete is O(1) for specific pos (like head, tail or you are at the spot), o.w. will be O(n) 
			- O(n) for lookup
		- real world: [The View from Aristeia: Should you be using something instead of what you should use instead?](https://scottmeyers.blogspot.com/2015/09/should-you-be-using-something-instead.html)
			- c++, liner search array O(n) outperf than 20-50 hashmap
			- also, hashmap need to rehash for 每一段時間, which cause perf drop
	- when NOT use hashmap/hash table and shall use other?
		- if you mem footsprint 非常寶貴, since hahsmap will take up more space
		- not an answer for range queries or prefix queries
			- what is range queries (搜索 2023 年 1 月 1 日至 2023 年 12 月 31 日之間發生的所有訂單, 找20~30歲的人) -> cab use b-tree
		- what is prefix queries? 輸入 "app" 可能會返回 "apple", "application", "apparatus" 等, 如 SQL 中的 LIKE 'prefix%'  or spellchecking 應用 > can use trie
		- poor at cache locality since data is randomly distruibuted in mem
		- ref: [algorithm - Why not use hashing/hash tables for everything? - Stack Overflow](https://stackoverflow.com/a/20170379/7621417)
		- most of the time, author in JS game dev, just use dynamic array when he need to iterate a lot and seldom need to loopup and use map when he don't need to iterate and need to lookup more, and find other  data strcuture when he need to opmitize the speed




[(592) Why Linked Lists vs Arrays isn’t a real choice - YouTube](https://www.youtube.com/watch?v=34ky600VTN0)
- based on author exp, most of of time, dynmaic array is enough, use LL when you want to use its specific feature to optimize
- in memory:
	- LL take a little more mem since it need to store ptr( in next)
	- but LL can use mem more effficent when you have low mem and highly fragemented mem layout since LL is linked one by one, and array will be need to have a chunk that the whole array can fit it
- in speed
	- when to get a middle ele, LL need to traverl and array is faster by using idx to get it
	- when insert and delete, LL can be very fast if ur ptr is at that place, just update the ptr and done
		- array will be need to move many ele and it will be slower
		- array will only be quick if you remove at end or top (depend on ur array impl)
	- when traveral
		- LL will not be fast, just loop over one by one
		- array will faster since it will leverage the cache locality




[Ask HN: The Required Fundamentals for Self-Taught Programming | Hacker News](https://news.ycombinator.com/item?id=40529064)
- 經過 25 年的程式設計生涯，我認為一切都可以歸結為資料結構和演算法
- 資料結構比演算法更重要。
- 我認為其他一切都來自於在職訓練中獲得的經驗，因為你需要訓練大腦的模式匹配部分。
- 良好的資料結構和演算法基礎可以大大簡化該過程。它們塑造了我們的理解，就像我們使用它們來編碼一樣。
- 正確的資料結構可以大大簡化問題，而錯誤的資料結構會將問題變成錯誤和義大利麵條程式碼的噩夢。
- 資料庫模式是其在應用程式層級（而不是模組層級）的自然擴展。
- 我沒有具體的建議，因為資料結構是一個非常廣泛的領域，但 MIT OCW 資料結構和演算法課程是一個很好的起點。
- 哦，還有《設計資料密集型應用程式》一書，它對如何在該領域使用資料結構和演算法進行了更高層次的概述。