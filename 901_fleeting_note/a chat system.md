
related
- [[WebSocket#how you impl online chat?]]
- [[WebSocket]]



相似 app: line, what's app, fb messager, wechat...

## req:
- supoort 1 on 1 chat
- support group chat both 
	- can handle 100 ppl in group chat
- only support text msg, a text length limit is 100,000 char long
- 50 M DAU
- multiple device support: same a/c can log-in in mobile and desktop
- store chat history forever




## 用 ws(websocket)
- use pooling and long pooling is not make sense
- 因為我們會需要 client and server side 兩端建立起 long conn，這樣 client can send msg to server, server also can send msg update to client
- It starts its life as a HTTP connection and could be “upgraded” via some well-defined handshake to a ws connection
- ws 通常也可以過 firewall, 因為 ws 也是用 80/443, 就是 http/s 的 port

![[IMG-a chat system-20250810200231277.png|510]]

![[IMG-a chat system-20250810200231457.png|448]]



## high level design
大方向有以下三個組件
![[IMG-a chat system-20250810200231985.png|601]]


stateless:
- 很多 功能 (sign up, login, user profile, etc) of a chat application 一樣打到 api servers


stateful
- server discovery 會 handle chat server 的 上線或上線，讓 client 找到 chat server
- stateful 是因為 clients <-> 和 chat servers 之間是有狀態的，一般client不會切換 chat server 除非 server 斷掉
- 如果一個連線會用到 10k 的 ram, 那 100 m cocurrent user -> 10 GB ram


整個串起來如下
![[IMG-a chat system-20250810200232873.png]]


## storage
- user setting, profile, friend 等 放在 sql
	- replicate and patition are common technique to scale
- chat history data 很大，用 nosql
	- whatapp,  一天就 60 B msg
	- nosql is good for 平行擴展
	- also get msg quicky


ps: both Facebook messenger and Discord use key-value stores. 
Facebook messenger uses HBase [4], and Discord uses Cassandra [5].


msg table for 1-on-1 chat
![[IMG-a chat system-20250810200233724.png|317]]


msg table for group/channel chat
![[IMG-a chat system-20250810200234795.png|329]]


msg_id 不需要 global unique, 因此可以用 local_seq_gen 即可
如果需要 global unqiue -> 需要使用 global unique id generator
- see  [[a unique ID generator is distributed system]]


## desing deep dive


### Service discovery


login 後 api server 透過 service discovery(e.g. zookeeper) 找到 userA 要登入那一個適合的 chat server, then 跟那個 server 連上
![[IMG-a chat system-20250810200235975.png|560]]



### 1 on 1 chat flow

- user A 和 chat server1 建立 ws 的連線
- user A send msg -> get msg_id from id 產生器
- 透過 msg sync queue
	- 會存入 kv store
	- 如果 user B offline -> msg 會 send to B via notificaiton 服務
	- 如果 user B online -> msg 會進入 chat server2, which have ws connection to user B
![[IMG-a chat system-20250810200237147.png|533]]




### msg sync across multiple devices
每一個 device 維護 `cur_max_msg_id` 來確認是否有最新的 msg

![[IMG-a chat system-20250810200238304.png|568]]



### Small group chat flow

可以把每一個 queue 當做每一個 user 的inbox
- 有沒有 new msg 需要收?  check inbox 即可
- 如果一個 group 裡面可以包括的 user 還不太大的話，這種機制還是可以運作的

WeChat uses a similar approach, and it limits a group to 500 members [8]. 
However, for groups with a lot of users, storing a message copy for each member is not acceptable.
![[IMG-a chat system-20250810200239260.png|577]]

and

![[IMG-a chat system-20250810200240446.png|551]]

### online presence


透過一個 presence server 來管理使用者的狀態

when user login
![[IMG-a chat system-20250810200241736.png|668]]


when user log out
![[IMG-a chat system-20250810200243071.png|689]]


Periodically, an online client sends a heartbeat event to presence servers. 
If presence servers receive a heartbeat event within a certain time, say x seconds from the client, a user is considered as online. Otherwise, it is offline.
![[IMG-a chat system-20250810200244643.png|713]]


online status fan-out
all user subscribe to a queue for a user A to know if user A is present 
![[IMG-a chat system-20250810200246194.png|697]]

- The above design is effective for a small user group. 
	- For instance, WeChat uses a similar approach because its user group is capped to 500. 
	- For larger groups, informing all members about online status is expensive and time consuming. Assume a group has 100,000 members. Each status change will generate 100,000 events. 
	- To solve the performance bottleneck, a possible solution is to fetch online status only when a user enters a group, manually refreshes the friend list or init a chat with that friend.




## Step 4 - Wrap up


# why fb use HBase than Cassandra
note on [The Underlying Technology of Messages](https://www.facebook.com/notes/facebook-engineering/the-underlying-technology-of-messages/454991608919/)
- fb 的messages 選擇用 HBase, 而不是 Cassandra
	- fb 團隊對於HBase的底層技術: HDFS (Hadoop Distributed File System) 已經有使用經驗
	- 他們 prefer HBase 更簡單的一致性模型: 自動負載平衡和故障轉移、壓縮支援、每台伺服器多個分片等
	- HBase 提供了更強的一致性，而 Cassandra 更注重可用性和容錯性
- HBase 可以看成是類似 Google bigtable 的 open source 的熱門 alternative
	- BigTable 是 Google 處理巨量數據的 key-val store  (vs. 處理結構資料是 BigQuery)





## link
[1] Erlang at Facebook: https://www.erlang- factory.com/upload/presentations/31/EugeneLetuchy-ErlangatFacebook.pdf
[5] How Discord Stores Billions of Messages: https://blog.discordapp.com/how-discord- stores-billions-of-messages-7fa6ec7ee4c7
[8] From nothing: the evolution of WeChat background system (Article in Chinese):
https://www.infoq.cn/article/the-road-of-the-growth-weixin-background 
[10] Flannel: An Application-Level Edge Cache to Make Slack Scale:
https://slack.engineering/flannel-an-application-level-edge-cache-to-make-slack-scale-b8a6400e2f6b




