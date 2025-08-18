

類似系統：Twitter/FB /Instagram/Quora

### 功能
- user can post a tweet
- homeline to see user's fd/follower tweets/news
- feed 可以有 vid and img
- shall be hometimeline fast, which is main functionality
- data assum
	- 200 M active user and 100 M new tweets per day
	- each user visist homeline/news feed 5 times
	- each homeline has 20 tweets
	- each tweet has ~300 bytes
		- photo ~ 200 KB, 20% tweet has photo
		- vid ~ 2 MB, 10% tweet has vid

### estimate
- write size daily?
	- text: 100M new tweets * 300 byes =  30 gb
	- img: 100M * 20% * 200 KB = 4 tb
	- vid: 100M * 10% * 2 MB = 20 tb
	- sumup:  24 tb per day
- 讀寫比很高 ~ 50: 1  ([ref](https://highscalability.com/the-architecture-twitter-uses-to-deal-with-150m-active-users/))



high level desin

### Feed publishing API
- POST /v1/me/feed
	- content: content is the text of the post

- GET /v1/me/feed
	- return news feed


### flow:
- POST 打上來後，進入 post service -> 進入後面的 db，前面有 cache layer
- 另外也會進入 fanout svc ->  透過 graphDB  get follower list, get data from RDBMS then 透過 mq 處理後進入 cache
	- note: real world redis put your home time line in redis cluster with over 800 entries([ref](https://highscalability.com/the-architecture-twitter-uses-to-deal-with-150m-active-users/)), with a couple of TB of ram
- 另外也會進入 notificaiton svc -> notify ppl the new content is published
	- tweet use AWS Firehose

![[IMG-news feed 系統-20250810200238283.png]]

### fanout svc deep dive
- 有兩種作法 
	- push, fan-out-on-write
	- pull, fan-out-on-read, or called on-demend model
- fan-out-on-write / 預寫  / push
	- 就是當你 post tweet 時就開始處理，最後會寫入所有 fds 的 homeline cache
		- pro: 
			- ppl read its homeline is fast -> and this is what we need
		- con: 
			- 如果很多 fds/followers -> 這些處理會很大量 -> heavy load on server and slow
- fan-out-on-read /  on-demend  / pull
	- 就是當 fd 去看頁面的時候，才會開始進行處理
		- pro: 
			- 不需要進行沒必要的推送, like inctive user
			- 也讓 server load 低
		- con
			- 另外當要讀才會拉大家的 feeds，等於寫入沒有處理，這時候才去處理也會慢很多。
		- can also pull periodically
- hybird
	- we use 預寫 in most case
		- so we keep read homeline is fast ([ref](https://highscalability.com/the-architecture-twitter-uses-to-deal-with-150m-active-users/))
		- we can also do this for active user  or user who is online
	- for hot-user -> we use on-demand model when checking hot-user's timeline
- 優化
	- when get homeline news, limit size when server response / client side also do lazy-loading
	- let user can disable notification


更多細節可以看: 
- [The Architecture Twitter Uses to Deal with 150M Active Users, 300K QPS, a 22 MB/S Firehose, and Send Tweets in Under 5 Seconds - High Scalability -](https://highscalability.com/the-architecture-twitter-uses-to-deal-with-150m-active-users/)
	- on timeline
		- Your home timeline sits in a Redis cluster and has a maximum of 800 entries.
		- At 150 million users with 300K QPS for timelines (home and search) naive materialization can be slow. Solution is a write based fanout approach. Do a lot of processing when tweets arrive to figure out where tweets should go. This makes read time access fast and easy. Don’t do any computation on reads.
		- This is a very directed path. Completely precomputed home timeline. All the business rules get executed as tweets come in.
		- Immediately the fanout process occurs. Tweets that come in are placed into a massive Redis cluster. Each tweet is replicated 3 times on 3 different machines. At Twitter scale many machines fail a day.
		- Fanout queries the social graph service that is based on [Flock](https://github.com/twitter/flockdb?ref=highscalability.com). Flock maintains the follower and followings lists.
		- The Redis cluster has a couple of terabytes of RAM.
		- Native list structure are used inside Redis.
		- Let’s say you tweet and you have 20K followers. What the fanout daemon will do is look up the location of all 20K users inside the Redis cluster. Then it will start inserting the Tweet ID of the tweet into all those lists throughout the Redis cluster. So for every write of a tweet as many as 20K inserts are occurring across the Redis cluster.
		- What is being stored is the tweet ID of the generated tweet, the user ID of the originator of the tweet, and 4 bytes of bits used to mark if it’s a retweet or a reply or something else.
		- Your home timeline sits in a Redis cluster and is 800 entries long. If you page back long enough you’ll hit the limit. RAM is the limiting resource determining how long your current tweet set can be.
		- ... more in article
	- on notification system
		- Twitter runs one of the largest real-time event systems pushing tweets at 22 MB/sec through the Firehose.
		- At any given time there’s about 1 million sockets open to the push cluster.
- [Facebook Timeline: Brought to You by the Power of Denormalization - High Scalability -](https://highscalability.com/facebook-timeline-brought-to-you-by-the-power-of-denormaliza/)
	- Denormalzation, creating special purpose objects instead of distributed rows that must be joined, minimizes random IO by reducing the number of trips to the database.
	- Timeline decides the order to display data by calculating a rank based on metadata. The denormalization process brought all that metadata together in a format that meant ranking could be done in a few IOs and streamed efficiently from the database using a primary key range query

---


- fanout svc 先去 graphDB ([GitHub - twitter-archive/flockdb at highscalability.com](https://github.com/twitter-archive/flockdb?ref=highscalability.com)) 拿 fd/follower list
- 然後去 db 拿資料
- 最後把資料 pass to msg queue 處理
	- 處理後放到 redis
	- 資料結構 is redis list  `[[post_id1, user_id1],[post_id2, user_id2],[post_id3, user_id3]...]`
	- for active user, your homeline data is in redis

![[IMG-news feed 系統-20250810200239231.png]]



### db schema
- related: notion note [PG, cocurrent-data-modificaiton: tweet-like](https://www.notion.so/nture4388/PG-cocurrent-data-modificaiton-tweet-like-d7283c2655694e0098b4c82363473c55?pvs=4)
![[IMG-news feed 系統-20250810200240411.png]]









## note on [Timelines at Scale - InfoQ](https://www.infoq.com/presentations/Twitter-Timeline-Scalability/)

capacity planning is their job

user timeline vs home timeline



pull infra and push infra

pull 個概念是 you visit 的 twitter and you get the info
你主動去那個網站拿資料，或是打 api 拿資料 -> client side trigger

push 是 server side trigger, firehole
![[IMG-news feed 系統-20250810200241634.png|626]]


![[IMG-news feed 系統-20250810200243031.png|431]]

下面介紹 home timeine API

![[IMG-news feed 系統-20250810200244507.png]]


![[IMG-news feed 系統-20250810200245888.png|545]]




![[IMG-news feed 系統-20250810200247402.png|591]]



![[IMG-news feed 系統-20250810200248972.png|665]]


![[IMG-news feed 系統-20250810200250154.png]]



## link

- [Building Timeline: Scaling up to hold your life story - Engineering at Meta](https://engineering.fb.com/2012/01/05/web/building-timeline-scaling-up-to-hold-your-life-story/)
- [system-design-primer/README.md at master · donnemartin/system-design-primer](https://github.com/donnemartin/system-design-primer/blob/master/solutions/system_design/twitter/README.md)
- [AlgoDaily - Design of the Twitter Architecture - Introduction](https://algodaily.com/lessons/design-of-the-twitter-architecture)
- [Twitter System Architecture. Please clap and share if you like. | by JIN | InterviewNoodle | Medium](https://medium.com/interviewnoodle/twitter-system-architecture-8dafce16aec4)
- [The Infrastructure Behind Twitter: Scale](https://blog.twitter.com/engineering/en_us/topics/infrastructure/2017/the-infrastructure-behind-twitter-scale)
- [Design Twitter - A System Design Interview Question - GeeksforGeeks](https://www.geeksforgeeks.org/design-twitter-a-system-design-interview-question/)
- [Designing Twitter: System Design Interview Question](https://www.enjoyalgorithms.com/blog/design-twitter)
- [CodeKarle: Twitter System Design](https://www.codekarle.com/system-design/Twitter-system-design.html)
- How News Feed Works:
	- https://www.facebook.com/help/327131014036297/
- [2] Friend of Friend recommendations Neo4j and SQL Sever:
	- http://geekswithblogs.net/brendonpage/archive/2015/10/26/friend-of-friend-recommendations-with-neo4j.aspx