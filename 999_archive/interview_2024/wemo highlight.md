
[[wemo prod issue 經驗總結]]


# 參與的大專案
- refactor 目前的系統
- 緣起：因為使用 google 第三方登入的價值，經過評估後，商業價值不大，因此我們打算自己做自己的登入系統
- 從第三方登入 -> 變成我們自己驗證
- devide ID + id card as uid (mobile 端有做 app check) 
	- 登入
		- email or phoneSMS verify(otp) -> tmp token -> access token
	- 新用戶
		- email or phoneSMS verify(otp) -> tmp token -> upload photo, 輸入必要資訊 -> 完成註冊 -> access token
	- 換裝置
		- need BOTH email and phoneSMS verify(otp) -> tmp token -> access token
- 架構： [BE  註冊流程優化 - 新用戶的流程 flow ](https://docs.google.com/document/d/1FF-fwFPHrGgOXE-C_rJQwERT3LnPM2i9zT-kBZHsEKM/edit?tab=t.0#heading=h.5ojky6euoemf)
	- [[WeMo Authentication and Registration Flow]]
- 因此
	- 我主要負責簡訊系統微服務
	- 研究了 redis bull queue 技術，並在簡訊系統微服務(angelia)中實施了 redis queue。
	- 與第三方簡訊系統(every8d)進行了串接，實現了簡訊發送的自動化和錯誤機制的建立。
		- 收發簡訊的模組化：未來可能有其他功能都可以方便使用簡訊系統
		- 第三方API串接
			- 呼叫對方的 API, 把簡訊送給第三方，然後開一個 API 作為 webhook來對方把收到結果發送回來。
			- 除了看文件外，還要積極對方工程師就文件沒有提到的 case 進行溝通。
			- 要把想到的測試例子都進行測試，很多 case 文件不會提到。
			- Integrate with third-party API, every8d -> [every8d 開發](https://www.notion.so/nture4388/SMS-integration-and-webhook-every8d-fa51fac9719b4d01915af0d9cf86bc02?pvs=4)
		- queue 的機制的建立和確認, no missing msg, retry 機制, idenpotent
			- [[test on bull queue when pod is not graceful shutdown]]
	- 通過這些措施，有效提高了簡訊系統的穩健度與可觀測性
- blocking mechanism
	- 主要用到 redis, hset, multi
	- hset 也可以用 increby1 來針對一個 欄位, hset, 等，很多操作，包起來，變成一個 "類似原子性的"操作
		- [[從 ACID 來看 redis multi]]
	- [BE Phone Number 阻擋與封鎖名單機制](https://docs.google.com/document/d/1ziRuI1WJpqosTHBjyk8-getSaScJlRcTUbPbeCacH1Q/edit?hl=zh-tw&tab=t.0#heading=h.l6pp6k2uhniq)
	-   [[wemo AbstractBlocker code]]
		- hermes src code key word: AbstractBlocker
- core system
	- pricing
	- 和既有系統 rent, get scooter, API 等相容
	- 後台的功能建立, API 設計
	- doc
		- [[stack calculation mechnsim 主要邏輯]]
		- [[pricing 主表 -> pricing timeline -> pricing config]]
		-  [[core system 功能規劃記錄]]
		- [[Core System] Pricing System](https://docs.google.com/document/d/1wRbjPzLZiezzo4yeUonlsM5oYNzy1vdbOlzgnut-u8E/edit?tab=t.0)
		- [[BE][core] VC and PC planning](https://docs.google.com/document/d/1t0eXvISPs8aVB6GqHCFltD_-eGzCwEHFP3WSdXZ8yFg/edit?tab=t.0#heading=h.bp2u06wa33zh)
- 小時租計算加入里程費
	- [[thoughts_on_code on time plan]]
- 成就獎勵 kafka
	- so, data team will calculate the user raide count and put the result into kafka nosql db: kql
	- I will make a http post to this kql db, and get the result, response to user
	- design aspect
		- modulize the kql access layer to enable future reuse, in the future, ppl can still use this module to query this sql to do other thing, at that time, I referece the design how typeorm interface
		- no sdk, only raw http reqest, so I need to wrap a layer to make it easy to use when other part of the app use it like sdk
			- assemble input to kql accept qeury
			- parse incoming kql response and transfrom into easy to use strcuture
	-/Users/re4388/project/work/hermes/repositories/kafka/ksqlDBClient.ts
- - WEMOPASS_TWN 票券系統的規劃與執行
		- [Promotion + WeMo Pass 相關流程文件](https://docs.google.com/document/d/1q4qTnGETImMcytSNjIj4gRSLdq87sR0G4x00FOJW23k/edit?tab=t.0)
- 很多的issue 處理和既有系統長出新功能
	- db turning
	- 忘記還車
	- wemo discount 序號
	- 新增綁卡3D驗證


# wemo perf review
---

2024 下半年可以說的
- core 開發


2024 上半年 feature 開發: 
**可以說的：
- blocking mechanism
- 小時租里程費(hourly rental mileage fees)
	- [ ] 翻翻是否有東西可以說
- 阻擋和封鎖名單機制(blocking mechanism)
	- 確保 atomic in each ops, use hashSet
- I-One-Fly 相關任務(planning part of feature and implementaion)
	- [ ] 翻翻是否有東西可以說
- 跨區還車(support implementaion)
	- [ ] 翻翻是否有東西可以說
- 新戶發註冊騎乘券(support implementaion)
	- [ ] 翻翻是否有東西可以說


2023下
**可以說的：
- 成就獎勵 kafka
- feat
	- service app 維護歷史紀錄
	- BE - 成就獎勵數值 - Sync Data From Kafka
		- 模組化
		- 一樣是接kafka ksql 的rest API
	- BE - Hermes - Scooter API 紀錄租借時的里程與電量資料
	- BE - RenTour - Total ODO 調整
		- [ ] 翻翻是否有東西可以說
	- RD_Stability_對帳系統: 財務對帳系統搬回 Apollo
	- WeMo Pass Phrase2 - Discount  折扣模式擴充
		- [ ] 翻翻是否有東西可以說
	- [CS] App用戶回報機制優化
		- [ ] 翻翻是否有東西可以說
	- [IMC][WeMo PASS][首購優惠] 買月送月（TWM）

- 2.issue 處理
- wallet 餘額退款等
- sim 卡
- refund error
- bb-powerOff
- …
- 等 38件



2023上
可以說的：
- revamp user login process
	- intergrate 3 party sms api into our system
	- use queue to manage the sms sending flow


feature面: 上半年的主要回顧與貢獻: 
- rabbit jump 盤點、規劃與執行
	- 盤點了公司所有的可以用在價格調整上設定，包括程式面，設定上，資料庫上。
	- 規劃了 rabbit jump 的技術執行細節, rollback規劃與演練,  QAT 演練等。
	- 執行了 rabbit jump 價格調整計畫。
	- 通過這些措施，幫助公司達成 rabbit jump 的策略目標。
- 換電預約 bug 防堵修正執行
	- 定位與確認換電預約系統的 程式邏輯bug。
	- 就既有邏輯進行修正，新增測試。
	- 通過這些措施，有效防堵換電預約系統的 bug ，提高系統的安全性。
- 註冊流程優化 2.0 -> revamp user login process
- WEMOPASS_TWN 票券系統的規劃與執行
		- [Promotion + WeMo Pass 相關流程文件](https://docs.google.com/document/d/1q4qTnGETImMcytSNjIj4gRSLdq87sR0G4x00FOJW23k/edit?tab=t.0)




# 摘要 wemo highlight



在 Wemo Scooter 的工作經驗中，我主要負責後端開發，並參與了多個重要專案，涵蓋系統重構、新功能開發以及問題修復等方面。

以下是一些可以重點提及的項目，並根據 STAR 原則做說明：

**一、系統重構 -  用戶登入系統 (Revamp User Login Process)**
* **Situation (情境):**  Wemo 原本使用 Google 第三方登入，但評估後發現商業價值不大，決定自行開發登入系統。
* **Task (任務):** 
	* 我的任務是開發新的登入系統，包含 email/簡訊驗證、token 發放、以及與現有系統的整合。
	* 我主要負責簡訊發送的開發。
* **Action (行動):**  
	* 第三方串接  
		* 積極與 Every8d 工程師溝通
		* 釐清文件中未提及的案例，並進行了全面的測試
		* error code 的 mapping (從第三方 -> 微服務 -> 主服務的使用者)
		* see [every8d 開發](https://www.notion.so/nture4388/SMS-integration-and-webhook-every8d-fa51fac9719b4d01915af0d9cf86bc02?pvs=4)
	* 簡訊發送模組化
		* 確保未來其他功能也能方便使用
		* use nest.js, 依賴注入的方式
		* API interface 是比較通用的，就是發一個簡訊需要帶過來的比較資訊
	* bull queue 系統實作
		* rate limit 設定
			* 防止惡意攻擊
		* queue concurrey 設定, prirotiy 設定, rate limit, 一秒要最多要處理多少的 job 設定，多的進入 delay queue 等等
			* /Users/re4388/project/work/angelia/src/bullQueue/bullQueue.module.ts
		* [[gracefaul shutdown 的處理]]
			* [[test on bull queue when pod is not graceful shutdown]]
			* 實驗 實際上的情況
			* bull queue retry 的設定
			* 觀察 bull queue 底層 redis 的行為 -> 類似 kill pod, job 真的存在 delayed job 等等
		* idempotent 的處理
			* 第三方重複呼叫 cb
				* 因為我們呼叫記錄都會記入 db, 因此如果重複 cb, 可以去 db check, 那如果有重複的話，就不處理了
			* 如果 pod crash, 起來後， reprocess job 後 
				* use default,  retry 1 times
		* monitor tool setup
	

* **Result (結果):** 新的登入系統成功上線，提升了系統的穩定性和可觀測性。模組化的設計也提升了程式碼的可重用性。




**二、核心系統開發 -  價格計算系統 (Core System Development - Pricing System)**
* **Situation (情境):**  Wemo 需要一個更彈性且精確的價格計算系統，以支援新的商業模式和計費方式。
* **Task (任務):** 我參與了核心價格計算系統的開發，包含後台功能建立、API 設計，以及與租借、取車等既有系統的整合。
* **Action (行動):** 我設計並實作了價格計算的核心邏輯，並撰寫了相關技術文件，例如堆疊計算機制、價格表結構等。  我也確保新系統能與現有系統無縫整合。
* **Result (結果):** 新的價格計算系統成功上線，並支援了小時租里程計費等新功能。




**三、成就獎勵系統 - Kafka 整合 (Achievement Reward System - Kafka Integration)**
* **Situation (情境):**  Wemo 需要一個成就獎勵系統來提升用戶活躍度。 資料團隊會計算用戶騎乘次數，並將結果放入 Kafka (KSQL)。
* **Task (任務):** 我的任務是開發一個系統，從 Kafka 讀取用戶騎乘數據，並將獎勵發送給用戶。
* **Action (行動):** 我設計了一個 HTTP POST 請求到 KSQL 資料庫，並將 KSQL access layer 模組化，方便未來重複使用。  由於沒有現成的 SDK，我還封裝了一層 API，簡化了 KSQL 查詢和結果解析的流程。
* **Result (結果):**  成就獎勵系統成功上線，並有效提升了用戶參與度。 模組化的設計也方便了其他團隊使用 KSQL 資料庫。

**四、問題修復和效能優化 (Issue Handling and Performance Optimization)**
*  除了上述專案，我也處理了許多線上問題，例如資料庫效能調校、忘記還車處理、折扣序號問題等等，提升了系統的穩定性和可靠性。



# 電梯簡報

在 Wemo Scooter 擔任後端工程師期間，我參與了用戶登入系統重構、核心價格計算系統開發、以及成就獎勵系統的 Kafka 整合等關鍵專案。  
我擅長使用各種技術，例如 Node.js, Typescript, Redis, Postgres, 以及第三方 API 串接，並注重程式碼的品質，模組化設計和可重用性。  
我也積極參與問題修復和效能優化，確保系統穩定可靠地運行。


**額外建議:**
*  在面試過程中，可以根據面試官的提問，選擇性地展開說明這些專案的細節。


 準備一些具體的數據或案例，例如系統效能提升的百分比、問題解決的效率等等，可以讓你的回答更有說服力。
希望這些資訊能幫助你更好地準備面試！


