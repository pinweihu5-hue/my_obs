[[SOLID]]

---
SRP single responsibility principle: 一個組件應該服務於一個業務需求 ↔ 只有這個業務需求調整，才會需要改變這個組件

![[IMG-SOLID_SRP 單一職責-20250807202921487.png|759]]


**一個組件，不該他碰的東西，不要碰，如果需要交互，要解耦。**


# 違背的例子
- 一個 class or moudle 裡面，因為新需求，發現以前寫 的code 沒有模組化好，沒有切分好，讓你要改的code跟你不想要改的code, 兩個code在這個新的需求底下是兩個東西，但是以前把這兩個code當成一個東西寫，你被迫要一起改 (或是直接寫新的)
- 如果有新的需求進來且當初沒有考慮到的變化，程式就耦合了。那就需要重構。
	- 譬如說我把發送 sms 跟 登入流程一起做，沒有把 sms 機制自己拉出來，就是把這兩個功能 coupled 了
		- BTW, 看了一下，目前 sms and auth in angelia 也不能說 couple 都是用注入的方式
		- 如果要有一個新的功能用 sms, need to do?
			- for now, we use bullQueue in front of sms sending
			- a new queue
			- this queue's handler handle sms.send
			- have a new svc to add job into this new queue
	- 那結果就是如果有其他東西也需要用 sms every8d, 那我原本的那邊可能需要拆掉，或是另外寫一套
- 如何知道是否違背? 如果一個東西，你問他這個是做什麼的，他說，負責 xxxx 和 yyyy  → 這個 就違背了


# guideline not to violate SRP
- when you design a new fun, think its future extensiblity
	- when you do this, you will make your code more generic and make it just one SRP, in a abstract way
	- 類似 a abstract method and everyone can use it
		- `/Users/re4388/project/work/hermes/api/v2/blockList/service/blocker/AbstractBlocker.ts`
	- ex like rateLimitByxxx
		- you can by token, by phone, by mail...
	- so you don't couple to only phone or couple to login process or...


# 單一?
- 只允許一個力量去改變它, 引起類變化的只有一個原因
- SRP 的目的是，一個 staff 可以被改變的原因應該只有一個，原因來自一種業務需求
	- 類似登入流程改變，和 sms 發送邏輯改變都需要動到同一段 code, 這就是 violate SRP
	- 這兩段code應該設計成正交
	- 類似如果我有其他東西需要發送 sms, 我應該可以直接拿 sms 的 code 來用，裡面沒有混著 login的邏輯
	- 如果我登入流程需要調整， sms 應該不受到影響
- 不需要預測，但需要一定程度的 common sense，另外經驗會有差




傳統的洋蔥架構和 clean 架構就是像是下面這樣的layer。每一層內，又因為業務需求的不同，又可以再切
![[IMG-SOLID_SRP 單一職責-20250807202922703.png]]