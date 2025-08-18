[[_wemo_idx]]


[[my_wemo_interview_tryout_0]]
## wemo interview 作業內容

- 框架使用 NestJS (<https://docs.nestjs.com/>)
- 需實際連 relational DB, 何種 DB 不拘
- 設計後端 Node.js server
- **需求: 請實作租車流程**
  - **基本三張表資料: User, Scooter, Rent**
  - **同一 user 同時只能租一台車**
  - **同一台車只能被同一個 user 租賃**
  - **紀錄使用者租車的時間(起迄時間)**
- 以上為必需條件, 需包含測試.
- 其他可自由發揮, 任何你覺得應該要有，但是我們沒有提到的部分
- 推上任一版控平台 (GitHub, BitBucket … etc) 並於面試前提交連結, 包含說明文件
- 我們會要求在面試時呈現你的作業, 包含環境設定、執行程式
- 有能力充分表達設計概念
- 過程中問題都可以來信討論
- 任何你覺得有趣可展示的實作或架構

* 公司實作  or  [[wemo homework design]]
  - 同一個 user 同時只能租一台車, 同一台車只能同時被一個 user 租賃
    - rent will record 這筆 rent is active or not (via his_flag or history flag)
    - rent 表有記錄 userId
    - 因此我們可以撈出不是 history flag 的車子，就是 active rent
	    - 然後看這個 userId 是否在裡面 
		    - 如果有，表示這個 user 正在租借中 -> throw err
    - rent 表也有記錄 scooter ID 
	    - if this scooter 在 rent 表有 active rent → throw err
  - 紀錄使用者租車的時間（起迄時間）
    - 目前公司實作
    - rent 表記錄起迄時間




# 評分標準

- [面試評分討論表 V2](https://docs.google.com/spreadsheets/d/1CPr4SW_A19Q5Ql_27UnU_r6Gu4BHZwdqHNHmSC1SsD0/edit?gid=2144417756#gid=2144417756)
* [評分標準 template](https://docs.google.com/document/d/1OsoZn-dODP0UvczKNpTjM7izZPU-qd2zTeaUb699z2w/edit)

|           |                                                                                                                                                                                                                                              |     |      |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- | ---- |
| 項目        | 該達到的目標 (0~5分)                                                                                                                                                                                                                                | Mou | Josh |
| 表達溝通能力    | : 無法清楚表達自己的想法或是無法接受質疑討論。<br>1: 可以表達但組織性不夠，聽者需要花心力消化內容，可以聽從討論但無己見。<br>2: 可以表達且有組織性，可以參與討論<br>3: 可以表達且有組織性，可以參與討論，且勇敢表達有自己的見解<br>4: 可以完整表達想法，且 open mind 討論不同聲音，對於知道正確的內容被質疑時無法有效說服。<br>5: 可以完整表達想法，且 open mind 討論不同聲音，對於知道正確的內容勇敢堅持與說服證明。     |     |      |
| 符合履歷經歷    | 歷上自己寫的熟悉技術應該要了解與熟悉  <br>0: 對自己寫的東西一問三不知。<br>1: 部分知道部分卻不清楚。<br>2: 知道自己做的內容，但不知道決策與設計的理念。<br>3: 知道自己做的內容，可以說出一些決策與設計的理念。<br>4: 非常熟悉自己寫的所有內容與項目也有參與設計，但面向有些侷限或是需要有人領導。  <br>5: 非常熟悉自己寫的所有內容與項目，知道設計與決策的來龍去脈與全面的影響力。                             |     |      |
| 對有興趣的技術專精 | : 完全沒有興趣或明顯理解有誤。<br>1: 只知道一點技術或知道很多但都是皮毛。<br>2: 只知道少數技術，但可以說出完成內容。<br>3: 知道多項技術，但只有一小部分熟悉。<br>4: 知道多項技術，且大部分有研究可深入探討。<br>5: 專精多項技術，且願意交流分享。                                                                                                  |     |      |
| 解決問題能力    | 0: 無法解決問題, 都需要既有的 Solution, <br>1: 僅能提出問題, 不會主動去想解決方案<br>2: 能點出問題, 無想法但會主動找尋團隊討論<br>3: 能點出問題, 試圖提出幾種方案, 但不確定是否能夠實作<br>4: 能點出問題, 提出幾種可能的方案但無法分析優劣, 和團隊討論後能夠執行, 會執行但無法和團隊協作.<br>5: 能獨立作業, 列出問題背後的問題, 找尋可執行的方法分析優劣差異, 提供討論, 能執行完成並且和團隊協作, 與團隊分享 |     |      |





### 江妤靜 2024_08_28-10_03_54


[GitHub - melody50819/nestjs-test](https://github.com/melody50819/nestjs-test)


26歲 女

聚典資訊股份有限公司 後端工程師 2023/02~仍在職
產業類別 電腦軟體服務業
職務類別 軟體工程師
公司規模 1~30人
管理責任 需負擔管理責任,且直接管理人數約4人以下
工作技能 MySQL、Python、AWS、Flask、MongoDB


工作內容 
1.後端API開發與優化
- 使用Python和Flask框架設計、開發和維護RESTful API
- 使用微服務架構,提高系統可擴展性和性能
2.資料庫管理與優化
- 實作資料庫分頁,提升資料查詢時間
- 簡化資料更新流程完善資料流
3.測試
- 導入單元測試策略,提高代碼覆蓋率至95%以上
- 使用pytest等工具自動化測試流程
- 實現正確的持續集成/持續部署(CI/CD)流程
4. AWS雲服務管理與優化
- 設計和維護AWS的雲架構,包括EC2、S3、RDS等服務
5.系統重構(核心專案)
- 領導並成功完成了一個為期6個月的重構專案
- 重新設計系統架構,將單體應用轉換為微服務架構
- 建立開發規範和PR,提升開發速度與建立資料互通機制
- 在預定時間內完成專案,同時保持系統穩定運行
6.其他
- 完善技術文件
- 建立後台 control panel,方便相關人員專案導入與設定
- 重構舊有 Codebase,提高代碼可維護性和擴展性
- 帶領新進人員在3個月內掌握公司專案與技術



個人簡介 【預計可到職日:09/09】
【謝絕行政、直播、銷售人員】
我是江妤靜,畢業於淡江大學資管系,擔任軟體工程師已有 3 年經驗。
擅長撰寫的語言是 Python 搭配 Flask 框架,資料庫為 MySQL、MongoDB,搭配使用工具為 VSCode、Hackmd、
Notion,使用系統為 Ubuntu,也曾自學 Django、FastAPI框架。
個人格言 學習動力來自於成就感,成就感來自於不斷的學習。
#認真 #有進取心 #善於傾聽
個人特色 認真、喜愛coding、獨立、好學
個人連結 https://github.com/melody50819



### 唐書涵 2024_08_20-10_09_36




- prepare statement 觀念不夠清楚，應該可以防止 sql injection了嗎?





英諾瓦資訊科技服務有限公司 software engineer 2022/05~仍在職
產業類別 電腦系統整合服務業
職務類別 軟體工程師
公司規模 100~500人
工作技能 Python、Linux、Node.js、MySQL、AWS、ReactJS
工作內容 AWS/Gitlab/Apigee/Docker/Bash/MySQL/Python/Node.js

Frontend & Backend Development:
Developed frontend using React and backend using Node.js.
API Management:
Managed APIs for migration from aws EC2 to aws ECS.
Microservice Oversight:
Maintained over 10 microservices in Node.js, Java, and Ruby within an nginx environment.
Maintenance & Troubleshooting:
Utilized Linux command line, Postman, aws Cloudwatch for maintenance and troubleshooting tasks.
Containerization & Deployment:
Streamlined containerization and deployment processes, transitioning applications from development
environments to scalable production infrastructure on AWS.
Customization & Scripting:
Customized internal tools and customer-facing applications using Python and Bash scripts.
Migration task from C1 to C2 infrastructure:
● Refactored Ruby-based Docker image to resolve compatibility issues, enhance stability, fix E2E test failures, and
implement a temporary solution during downtime to enable ongoing updates to the service repository.
● Developed and executed a comprehensive traffic switch plan, minimizing downtime and ensuring a smooth
transition for end users.
Full-Stack Development:
● Developed features to support the transition from OAuth to CIAM, also collaborated with cross-functional teams
to integrate solutions. ● Enhanced the malware detection system for S3, expanding the file size limit from 2GB to 50GB


自傳


自傳
成長背景

來自新竹,常喜歡透過不同視角觀察事情,個性負責任不喜歡給人添麻煩,會在能力範圍內幫助他人。喜歡與人相處,也特別喜歡解決
團體中遇到的挑戰。因此從小便積極參與社團及公共事務,在高中大學甚至於在國外唸研究所的期間都擔任不同社團的幹部。

學歷
大學及研究所都就讀資訊管理學,在就讀時在各項課程中漸漸紮實了自己的資訊知識,也在各樣程式語言課程中精進自己的實作能力。
以下列出我較為深刻的課堂以及實作內容。


長庚大學
C 語言程式設計
期末團體作業需要開發一個以C語言為主的應用程式,我們組別開發了一個遊戲程式可以透過直接透過終端機來進行介面操作,並且選
擇各樣的小遊戲來挑戰。

大學畢業專題- 抗糖揪團App
第一次實作可以上線的大型專案,資料庫端使用MySQL,伺服器端使用Apache Tomcat 以及java servlet,App的部分則透過Java程式
語言進行開發。

國立中山大學資訊管理研究所
進入研究所後,
進入了人工智慧領域的李瑋柏教授的實驗室,研究領域主要研究機器學習演算法在活動辨識上的應用以及最佳化問題。
修課內容巨量資料分析、雲端計算、服務流程計算及人工智慧等等課程都在讓我在資訊科技的實作領域有了更多的認識,
像是如何寫一個基於restful架構的網路服務
如何從爬蟲技術開始對一份資料集進行資料前處理、特徵工程、建模型預測等來進行分析。

碩士論文-基於基因演算法的第二型糖尿病患者之運動處方演算法推薦

論文分兩個階段,首先將透過機器學習演算法去辨識使用者一周的活動作息。
二是基於使用者作息及本身條件,將運動處方推薦轉化成一個最佳化排程問題並透過基因演算法找出解法。

Johhanes Kepler University Linz
在奧期間由於環境,大幅增進了我的英文聽說讀寫能力,也獲得了與不同國家學生合作專案的機會。由於該校彈性的修課制度,我對於
有興趣的領域修課內容皆集中在資料分析,如推薦系統、監督機器學習非監督式機器學習以及資料視覺化等課程。
專案合作- 拼車網站功能升級
SKF公司對於自己的員工使用公用車出差相關業務皆透過網站進行統一管理,如日誌上傳、各輛公務車的租借狀況等等。專案目標是將
原有的目的地功能用Google map API取代為一個互動式的地圖,以及開發一個網路服務供行動版App進行互動使用等等。網站架構為
C# MVC,資料庫為MsSQL,網路服務則是使用flask進行開發。




==

3. **技術不斷演進，您如何持續學習新的技術和工具？請分享您最近學習的一項新技術，以及您學習的方法和資源。** (評估學習能力、對新技術的熱情)
    

==


AI  幫忙...

後端工程師面試題目 (根據履歷設計)

**一、技術能力與經驗 (Technical Skills & Experience)**

1. **您在履歷中提到使用 Node.js 維護超過 10 個微服務。請問您在微服務架構中，如何處理服務之間的通訊？請分享您在實務上使用過的通訊方式，並說明選擇該方式的理由。** (評估對微服務架構、服務間通訊的理解與實務經驗)
    
2. **您有使用 Python 和 Bash 腳本開發內部工具和客戶端應用程式的經驗。請分享一個您開發過的工具或應用程式，並說明其功能、設計理念以及您遇到的挑戰。** (評估腳本語言應用能力、問題解決能力)
    
3. **您在 C1 到 C2 基礎架構遷移任務中，將 Ruby-based Docker 映像檔進行了重構。請問您是如何分析和解決相容性問題的？您使用了哪些工具和技術？** (評估 Docker 應用能力、問題分析與解決能力)
    
4. **您在履歷中提到了 AWS、GitLab 和 Docker 等技術。請分享您在這些技術方面最熟悉的領域，並說明您在實際專案中是如何應用這些技術的。** (評估對常用後端技術的熟悉程度和實務經驗)
    

**二、問題解決與學習能力 (Problem Solving & Learning Ability)**

1. **您在維護和故障排除方面使用了 Linux 命令列、Postman 和 AWS CloudWatch。請分享一個您曾經遇到的棘手技術問題，並說明您是如何使用這些工具來診斷和解決問題的。** (評估問題分析、除錯能力)
    
~~您在碩士論文中使用了機器學習演算法來識別使用者的活動作息。請問您是如何選擇和評估不同演算法的？您遇到了哪些挑戰，以及如何克服這些挑戰？** (評估機器學習知識、研究能力、問題解決能力)~~
    
3. **技術不斷演進，您如何持續學習新的技術和工具？請分享您最近學習的一項新技術，以及您學習的方法和資源。** (評估學習能力、對新技術的熱情)
    

**三、團隊合作與溝通能力 (Teamwork & Communication)**

1. **您在履歷中提到與跨部門團隊合作，將 OAuth 遷移到 CIAM。請問您在與其他團隊合作時，是如何溝通和協調的？您遇到了哪些挑戰，以及如何克服這些挑戰？** (評估團隊合作、溝通協調能力)
2. **請分享一個您在團隊合作中遇到的衝突或挑戰，並說明您是如何處理和解決這個問題的。** (評估團隊合作、衝突處理能力)
    

**四、文化適應與職涯目標 (Culture Fit & Career Goals)**
1. **您為什麼想加入我們公司？您對這個職位的期望是什麼？您未來的職涯目標是什麼？** (評估求職動機、職涯規劃、文化適應性)
    

**面試技巧:**

- 除了詢問問題，更重要的是仔細聆聽面試者的回答，並根據他們的回答進行追問，以更深入地了解他們的技術能力、經驗和思考方式。
- 可以準備一些實際的程式碼範例或案例分析，讓面試者展示他們的技術能力和解決問題的思路。
- 創造輕鬆的氛圍，讓面試者可以放鬆心情，更好地展現自己的能力和潛力。

希望以上面試題目能幫助您找到合適的後端工程師！




###  陳珮萱 2024_08_13-15_33_21

夠麻吉股份有限公司 PHP 後端工程師 2021/06~2024/07 (3年2個月)
產業類別 網際網路相關業
職務類別 後端工程師
公司規模 30~100人
工作技能 PHP、Git、shell script、MySQL、HTML、JavaScript
工作內容 1. 台灣pay金流介接及各金流帳務報表處理。
2. 電子發票相關業務及Turnkey軟體更新。
3. 負責公司各系統開發與維護。
4. 協助各單位報表或資料撈取需求。





 串接 API
 - 測試所有情境
 - 要包到哪種程度, 容易使用





---


###  劉士豪 2024_08_12-14_46_48




interview Q prep
~5年了， why 換工作?

web3 -> 離開 web3
full stack -> BE

下一個階段的職業目標是什麼?

==
華碩電腦股份有限公司 工程師 2019/05~仍在職
產業類別 電腦及其週邊設備製造業
職務類別 全端工程師
公司規模 500人以上
工作內容 技能

AWS: Cloudfront, API Gateway、S3、Lambda、Cognito、EC2等
前端 TypeScript、Vue3、Bootstrap5、Element plus
後端開發 Node.js、Express、MySQL
Web3 ethers.js、hardhat、solidity
Asus NFT Plaza
NFT交易平台,使用Opensea Seaport交易合約搭建,包括上架、交換、拍賣 NFT等功能。發行ROG Matrix顯卡
NFT、與其他廠商NFT
智能合約輔助工具:ABI decode tool、contract deploy tool
AI/ML
一個提供Jupyter Notebook介面,出租機台GPU算力的網頁服務 主要開發 會員系統、調用廠商機台API,進行機
台壓力測試、黑白箱安全性檢測,金流系統串接(stripe)、管理人員後台
Quantumcloud
方便使用者於自家桌機挖礦的桌面軟體 維護網頁、OAuth、edm送信
其他
mqtt、react native、electron、webrtc



ro

### 郭政杰 2024_07_29-14_00_05

- uuidV4 as rent PK?
- why use druing 的寫法？
- req 先到先贏，但是


### 張祐綸 2024_07_26-10_47_10
- unit test concept is not much experience
- no consider lock and tx in code
- any cert?
- 感覺都問不深
- 感覺要一直問一直問才可以問到東西
- 可以問一下
	- docker 的了解
	- any techinical 坑 encounter?
		- 資料設計上的問題
	- 你對軟體技術的那一部分比較有興趣?
		- netowrking, db, language, algo, dist...etc
	- why change copany, next goal?
- 基本上不太了解選型


### 陳威洋 2024_06_26
result:
- 沒有帶上技術細節
- josh like maybe too confident and hard to being pursuade


meeting note 2024_06_26-10_35_27


post rent
- permission
- scooter_plan, price plan


2 stage commit
- renting 中 -> 改 status
lock??


大流量遇到 db tx下
可以用 reddis lock, redlock


用 ts 的漸進是框架

解釋一下 nest life cycle
變成 mvc??
component的方式

==
how to use lavavel 寫 scooter?


### 江翊喬
[YC Chiang | CakeResume](https://www.cakeresume.com/yc-chiang-f521bd?locale=zh-TW)

no pass
reason: 
- 程度不夠, too many other thing, too less if any Tech skill demostration
- 說話沒有 to the point

--



Q to ask
- 你會如何設計來達到題目需求?
- 除了題目以外，就使要詢問工作經驗上看有沒有挖深
- 這個 candidate 很多都是寫的簡單
- 可能從下面這段來判斷 db 深度
> 財務開立發票的流程：
    - **跨部門協作**：與 TPM 與財務團隊密切合作，分析和確定用戶需求，設計更符合財務人員的數據篩選系統。
    - **技術實現**：實現了從前端傳送篩選條件到後端進行篩選的功能，確保了篩選結果的準確性。具體方法包括設計和優化後端查詢邏輯，使系統能夠快速處理和響應各種篩選請求。
    - **性能優化**：透過引入索引和優化 SQL 查詢語句，大幅提升篩選操作的效率，將篩選響應時間減少了 70%，顯著提升了用戶體驗。


這邊可以確認重構和design pattern深度
> - 重構 CMS 後端系統：
    - **提升可讀性**：透過重構結構和統一程式碼風格，使程式碼更易於理解和維護。
    - **增強維運效率**：優化系統架構和模組化設計，使日常維護和更新較有效率。




```面試邀約 2024_06_17-14_09_03


Josh Tsai
6 月 13 日 週四 下午 2:10 (4 天前)
寄給 krischiang0301、 Jean、 李貞子、 Zoe、 我、 Jack

江翊喬 您好：

感謝您申請我們後端工程師的職位，我們很高興邀請您進行面試。

面試詳情如下： 

面試時間：2024 年 06 月 20 日 下午 16:30  
面試時長：兩小時內
面試形式：線上面談，請開啟視訊
面試連結：https://meet.google.com/zox-yybj-wkz
面試流程內容如下：

第一部分：開發題目討論，這部份您可以先思考或是寫看看，我們會與您針對題目做相關的討論，約 20 ~ 30 分鐘，題目要求如下：

使用 NestJS 框架 (參考資料: https://docs.nestjs.com/)。
連接關聯型資料庫，資料庫種類不拘。
設計一個後端 Node.js 伺服器，達成基本租借流程。
需要有基本資料表: User, Scooter, Rent。
限制同一個人同時間只能租借一台車，一台車同時也只能被一個人使用。
需要紀錄使用者租車的起迄時間。
第二部分：個人經歷與技術討論，這部份會依照您的過往使用或做過的成品進行技術上的討論，約 30 ~ 50 分鐘。

請告知我們您是否可以如期參加，謝謝。
如果您無法赴約也請通知我們，我們會為您調整面試時間。


```


eng exp 約 一年四個月




---

### kunyi0821

同意跳過。跟之前比算是比較看不出任何亮點的作業 xd

- 用 redis 去檢查這個 user 是否已經在租借中了
  - 因此如果成功租借，會加入 redis via hset
- 然後 scooter 有一個 is_use 欄位，因此 start rent 時，可以 query 這個 scooter 看看這個欄位來決定這個 scooter 是否已經被租用了
- 上面都沒問題，才會 insert rent, update scooter(改 isUse)

---

### wei

- 有 dao -> use clean 架構
- use pnpm
- 用了 cls, thread storage
- 沒有在 rent 上押上 isUse 或是 history 之類的 flag
  - scooter 表上有記錄 userId, nullable, user 表上有記錄 scooterId nullable
  - 防止一車多租，或是一人多租他是透過檢查 scooter 是否非 null and 這個 user 上的 scooter 是否已經非 null
  - 好處是不會讓 rent 表的壓力太大

---

### 許尹維

[https://github.com/YINWEIHSU/scooter-rent-simple](https://github.com/YINWEIHSU/scooter-rent-simple)

- Users can rent only one scooter at a time.
- A scooter can only be rented by one user at a time.
- Only administrators have the permissions to add or modify scooter details.
- Users can only return their own rental orders.
- All users can view the current list of available scooters.

---

### ivymuchacha

履歷 [https://github.com/ivymuchacha/wemo-backend-project](https://github.com/ivymuchacha/wemo-backend-project)

- 有沒有達到作業要求?

總結：

- **瑕不掩瑜**
- BE 的最基本功有了，作業要求都有達到
- 從我的角度來看，我覺得剩下就是好不好相處和配合度
  優點
- 文檔算是滿多背景資訊的, 用心
- 有做註冊流程 → 加分
- 關鍵邏輯： 使用者無法同時借多車，和同時間只能租一台都有相對應的邏輯，處理方法我覺得，以小 project 來說，已經是標準答案（J 有更好的作法請教我 XD）。
  缺點
- 不過把面試作業要求放到 public github 複製貼上可能沒有一個面試上對於公司和面試者之間資訊是否應該公開的敏感度
- 文檔感覺很多，不過對於要讓他跑起來的人，不太友善，有很多東西要自己設定
  - 類似我自己開一個 mysql, 沒有 default setting, 沒有腳本 populate 初始資料，他有提到但自己要進去 sql 下等等
  - 然後因為 sql 語句因為上面是我自己弄，可能又剛好無法跟他的對上，需要自己調整等等
- 沒有 e2e test

我想要多了解的點

- error code
  - why use 201 for create user and login
  - 同一個 user 一次只可以租借期間租第二台，409 conflict? 比較少見還是我自己不懂?
- swagger 哪裡可以填入 auth 的資訊？需要 token 的我都不知道怎麼打 XDD
- token 多久過期？
- [ ] 準備一下面試要問的問題 - [ ] 幾個技術 - [ ] 可能會想要問以前對那個技術比較深入, 有哪些坑 - [ ] 聊一下有沒有學到什麼東西你覺得很有趣的 - [ ] 幾個與人互動遇到困難的情境題
      為何會想要當工程師
      為何會想要做後端
      來這個公司想要獲得什麼
      履歷有提到，維護分散系統架構以及推撥蕭效能問題，可以請你舉個具體的例子嗎？遇到什麼問題，如何解決？

---

### wemo candidate SiaoChi

        - no release in final
        - don’t know why ret expire time back to user → not fmailair with the auth mechansim
        - `getAvailableScooter` → no retrcit, auth 沒有做但又有api可以 createUser and login

- [team aggreement](https://athena.wemoscooter.com/wemo/backend-team-management)
  - Personality - KB: verbal skills, able to present the most important things - Josh: passion on features or techniques - Ben: like sharing knowledge - Jack: open-minded, shouldn't be defensive - Water: put yourself in others' place
    以下是一些問題，可以幫助您評估面試者是否符合上述人格特質：
  1. 請描述您在過去的項目中如何有效地傳達重要信息給團隊或利益相關者。
  2. 您對於後端功能或技術有哪些特別的熱情？請舉例說明。
  3. 您在過去的工作中如何分享知識和經驗給團隊成員或其他人？
  4. 請舉一個您在工作中遇到不同意見或挑戰時，如何保持開放的態度並解決問題的例子。
  5. 您如何將自己放在他人的位置上，以更好地理解他們的需求和觀點？請舉例說明。

##新人 on board process
[https://app.asana.com/0/1141737863181977/1205590974589698/f](https://app.asana.com/0/1141737863181977/1205590974589698/f) - archive - [ ] [https://app.asana.com/0/1141737863181977/1202289362114277/f](https://app.asana.com/0/1141737863181977/1202289362114277/f)
[https://app.asana.com/0/1156972811515649/1183244841176268/f](https://app.asana.com/0/1156972811515649/1183244841176268/f)
[https://app.asana.com/0/1141737863181977/1188429371313642/f](https://app.asana.com/0/1141737863181977/1188429371313642/f) - [ ] [https://app.asana.com/0/1154876818853744/1205413202533648/f](https://app.asana.com/0/1154876818853744/1205413202533648/f)
