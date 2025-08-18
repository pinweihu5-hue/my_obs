related:
- [[cs61b Hashing]]

### 解決的問題
- rehash 後，很多資料都要重新移轉
- hotspot problem
- scale 



4 個 bucket,  mod 4
結果可能如下

hash f 可以選用 CRC32 to get 數字 result
hash(某個 key) -> say 1532712134 then mod 4 -> result 就是某個 server idx
![[IMG-Consistent Hash-20250809205548462.png]]

拿掉一個 server,  mod 3
-> a lot 資料 need to 重新移動
![[IMG-Consistent Hash-20250809205548604.png]]



### 機制

選一個 hash, say sha1 
sha1 range is 2^160 - 1
拉起兩端
![[IMG-Consistent Hash-20250809205548774.png]]


**把 key and server 都 hash -> then they are a point in a this hash ring**
如下圖
![[IMG-Consistent Hash-20250809205548907.png]]


key 往下找第一個遇到的 server, 就是這個 key 要放的 server
![[IMG-Consistent Hash-20250809205549054.png]]

如果 server 4 新增
只有 k0 需要改放到 server 4
![[IMG-Consistent Hash-20250809205549261.png]]


如果 server1  拿掉
只有 key 1 需要移動到 server2
![[IMG-Consistent Hash-20250809205549438.png]]


以上，解決了本來天真hash下，拿走一個 server後，很多資料/key 都要重新放到新的partition的問題

**還是有缺點** -> 資料不平均分配在所有的分區上
**如果 s1 拿掉, s2 cover 的範圍 (s2 partition) 是 其他的兩倍大**
![[IMG-Consistent Hash-20250809205549589.png]]


也有可能一開始的 hash 後的結果就是 就是下面的樣子
可以看到很多都是 s2 的範圍
![[IMG-Consistent Hash-20250809205549729.png]]


### 使用 virtual node
我們讓 s0 and s1 有三個 virtual node
可以看到下面的每一個 虛擬 node 的 partition 就比較平均
![[IMG-Consistent Hash-20250809205549882.png]]


一樣遇到的第一個 v_node 就是要放資料的 node
![[IMG-Consistent Hash-20250809205550036.png]]
**As the number of virtual nodes increases, the distribution of keys becomes more balanced**






PS:
Consistent hashing is widely used in real-world systems, including some notable ones: 
• Partitioning component of Amazon’s Dynamo database [3]
• Data partitioning across the cluster in Apache **Cassandra** [4]
• **Discord** chat application [5]
• **Akamai** content delivery network [6]




---

other ref, not seen
- [https://medium.com/system-design-blog/consistent-hashing-b9134c8a9062](https://medium.com/system-design-blog/consistent-hashing-b9134c8a9062)
- [Consistent Hashing | Interview Cake](https://www.interviewcake.com/concept/java/consistent-hashing)
- [Consistent hashing explained | Ably Blog: Data in Motion](https://ably.com/blog/implementing-efficient-consistent-hashing)
- [Consistent Hashing Algorithm: 應用情境、原理與實作範例 | by chyeh | Medium](https://medium.com/@chyeh/consistent-hashing-algorithm-%E6%87%89%E7%94%A8%E6%83%85%E5%A2%83-%E5%8E%9F%E7%90%86%E8%88%87%E5%AF%A6%E4%BD%9C%E7%AF%84%E4%BE%8B-41fd16ad334a)
- [The Ultimate Guide to Consistent Hashing | Toptal](https://www.toptal.com/big-data/consistent-hashing)
- [Consistent Hashing](https://arpitbhayani.me/blogs/consistent-hashing)
- [(16) 花花酱 Consistent Hashing - CS大讲堂 EP17 - YouTube](https://www.youtube.com/watch?v=lm6Zeo3tqK4)

[1] Consistent hashing: https://en.wikipedia.org/wiki/Consistent_hashing
[2] Consistent Hashing:
https://tom-e-white.com/2007/11/consistent-hashing.html
[3] Dynamo: Amazon’s Highly Available Key-value Store:
https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf
[4] Cassandra - A Decentralized Structured Storage System:
http://www.cs.cornell.edu/Projects/ladis2009/papers/Lakshman-ladis2009.PDF
[5] How Discord Scaled Elixir to 5,000,000 Concurrent Users:
https://blog.discord.com/scaling-elixir-f9b8e1e7c29b
[6] CS168: The Modern Algorithmic Toolbox Lecture #1: Introduction and Consistent Hashing: http://theory.stanford.edu/~tim/s16/l/l1.pdf
[7] Maglev: A Fast and Reliable Software Network Load Balancer:
https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/44824.pdf