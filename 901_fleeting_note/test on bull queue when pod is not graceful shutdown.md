重跑的 job 會優先跑嗎?
- generally said, yes
- In my experiment, it still take some time for queue to pick those jobs to high priority
- so, say job 108 is a 重跑的 job, 109~112 are the job I sent immediatly after the server restart
- so 109 is still processed first, and then 108 got the chance to process




### Question 
當 JOBs 正在被處理時，pod 被"立即"關掉(連 graceful shutdown的邏輯都無法進行）。 
類似關掉前，有100個req正在陸續被打進來 -> 100個JOB, 有一些已經處理完，有一些正在被處理，有一些還沒被處理。
問題： 請問，"正在進行中"的 job 會怎樣？ 當 Pod 跑起來，這些job會被再跑一起嗎？還是就"不見了"? 還是？



測試方法：
- 讓一個job跑30sec
- 跑到一半kill process
- job會在queue中??


### 結論：
- 重新啟動process, consumer會去找到沒有完成的job, 完成他
- 有兩個參數可以設定
	- `lockDuration` job 多久沒有被處理才算  stalled  
		- 在 queue level [設定](https://github.com/OptimalBits/bull/blob/develop/REFERENCE.md#advanced-settings), like rate limit
	- `maxStalledCount` 被推遲的 job 會重跑的次數 
		- 預設是 1




### job lock
- job 會上 lock 來確保只有一個 consumer 處理 ( so, other work see a job locked, cannot process it)
- job 在一段時間（`lockDuration`) 過後，如果 lock 沒有被 renew, 就會被視為 `stalled`
	- 在 queue level [設定](https://github.com/OptimalBits/bull/blob/develop/REFERENCE.md#advanced-settings) `lockDuration`
	- why lock is not renew?
		- process 異常終止
		- node.JS 的 event loop 跑太久，久到超過一段時間process, 留意 CPU-intensive task 
- a stalled job will be automatically restart N 次 (`maxStalledCount` 可以設定 N ) （ see [setting](https://github.com/OptimalBits/bull/blob/develop/REFERENCE.md#advanced-settings)）




# how bull queue use redis

use redis string to save lock
![[IMG-test on bull queue when pod is not graceful shutdown-20250810210642091.png|761]]

sortedSet 去存放 completed  and failed 的 job
- 因為 job 自然是唯一的
- 有順序的
![[IMG-test on bull queue when pod is not graceful shutdown-20250810210642987.png|760]]


![[IMG-test on bull queue when pod is not graceful shutdown-20250810210643735.png]]



每一個 job 的 meta data 都會在redis, 用 hashmap的形式存放
記錄這個 job 的參數
![[IMG-test on bull queue when pod is not graceful shutdown-20250810210644676.png|695]]


ref:
- [JOB 的 life cycle](https://docs.bullmq.io/guide/architecture)
- [OptimalBits/bull: Premium Queue package for handling distributed jobs and messages in NodeJS.](https://github.com/OptimalBits/bull#important-notes)

