
1. 似懂非懂還是最大的問題
2. 敏感度或經驗不夠，沒有挖出所有致命的未知的未知
- 沒有把 code 中的邏輯和用法問的一清二楚 
	- time plan data inconsistent issue, issue ticket
	- misunderstanding on a field meanings from the ceres return
	- not understanding the field meaning and also not ask clearly from people who understand
	- ask josh/sr 問清楚問題，慢慢來，不急，關鍵是問清楚每一個不確定的地方
- 沒有搞清楚 code 邏輯 && 沒有搞清楚 apollo 改地圖上的所有細節，所以我就無法聯想到這邊會噴掉
	- 結果 throw error inside array.some but the root cause is not knowing the consequence of deprecated zone id in Hermes table and unavailable zone from atlas
	- take time to know more about the spec, how 厲害關係人 think and act?  how to 加圖等
	- 程式上，要想有哪些edge cases, 要如何處理，這樣處理合不合理。
- 重構沒有去把經過的地方的邏輯都 100% 看對，然後那邊的邏輯的 test 也沒有去補好。
	- Logic error in box update message 
	- when refactor , 就算logic go through不是自己寫的code, 也應該要補上 unit test
	- ou could change the origin logic and you are not notice
	- 不要因為工作多很多就不做，代價可能是 prod issue
- qat 改code，沒有把 code 進入 dev
	- 壞習慣 在 qat 改 code
	- what is 好習慣[] 
		- 改 code 如果沒有很級， shall open a new branch and merge into qat -> if succceed -> create mr and add approver
		- 如果真的純測試 -> 測試一成功，就要 open a new branch 做上面的事情先
- 測試情境很複雜，一定要列出來，in excel, 不然會漏掉，漏一個就是 production issue
