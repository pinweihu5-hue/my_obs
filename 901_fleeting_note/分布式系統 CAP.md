
P - 分區容忍 (Partition tolerance)
A-  高可用
C - 資料一致 (這邊都是說"強"一致)

這個理論說，只能三選二
`PS: 但是這種觀念有點誤導人，因為在討論分布系統的CAP時，你無法避免網路問題(P)，你根本無法3選2，你只能選 AP or CP`

分區容忍(P)表示如果 node 之間的連接出問題，還是可以提供服務
除非是單機或是 (比較不會出問題的區網)，網路都會有機率出問題，因此我們還是會希望系統提供服務，因此 parition tolerance 是一個必要的功能


所以問題就是，高可用(A) 和 資料一致(C) 我們要選那一個的問題

要高可用，資料就會不一致（最終一致在 CAP 脈絡下也是不一致，因為一段時間就是不一致）
如果要資料一致，那就是服務在資料出現不一致的時候(當網路中斷)，你讓他無法 access 到不一致的資料 -> 就沒可用性了

選一個看產品
類似金流服務 -> 資料都要保持一致 ->  CP -> Hbase, Cassandra (with Tunable Consistency )
如果是 like 點讚數量 -> 高可用更重要  -> AP -> DynamoDB



related:
- [[Cassandra]]
- [[a chat system#why fb use HBase than Cassandra]]
- go to Notion to find note on dynamo


![[IMG-分布式系統 CAP-20250810200247251.png]]

also see [[Consistency and Consensus, DDIA CHAPTER 9#The Cost of Linearizability]]



