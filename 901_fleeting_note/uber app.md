[設計 Uber - 高可擴充性 - --- Designing Uber - High Scalability -](https://highscalability.com/designing-uber/)


## req

乘客應該能夠查看附近的司機，如下圖所示
![[IMG-uber app-20250810200244355.png|394]]


- 乘客可以發起乘車服務(a ride, from src to dst)
- 附近的司機將收到行程通知，其中一位司機將確認行程
- 當派車去接乘客時，將向客戶顯示 "預計到達時間" ETA。
- 行程完成後，乘客會在乘車收據中看到行程詳細信息，例如行程地圖、價格等。


out of scope:
自動匹配司機和乘客的方式，在調度行程時考慮新司機和乘客等因素



## architecture

###  ride life cycle
![[IMG-uber app-20250810200245655.png]]


### 組件

我們可以建立以下組件來支援此旅行生命週期。

Driver Location Manager: 
駕駛員位置管理器：負責維護不斷變化的駕駛員位置。它將從駕駛員的app發出的包含其當前位置的消息，並更新汽車位置索引。


Trip Dispatcher:
行程調度員：負責 handle 使用者的 ride請求，並根據這些請求調度司機。


Arrival ETA Calculator: 
到達 ETA 計算器：一旦司機接受乘車，該組件將負責計算司機到達乘客的 ETA。


Trip Recorder:
行程記錄器：它將記錄行程進行時，從 app 傳輸的 GPS 訊號。這些 GPS 訊號將被記錄在資料儲存中，供後續系統（例如地圖匹配器和定價計算器）使用。


Map Matcher: 
地圖匹配器：此元件將負責使用把行程匹配到實際地圖上。


Price Calculator: 
價格計算器：它將使用記錄的行程資訊來計算使用者必須為該行程支付的價格。



上述組件可分為三大類：Pre-Trip Components, On-Trip Components, and Data Storage。
![[IMG-uber app-20250810200247068.png|485]]



## high level design


### **Pre-Trip Components**

該組件將支援查看附近位置的汽車並根據用戶的請求調度乘車功能。
driver 的位置可以儲存在記憶體中的汽車位置索引中（在後面的部分中解釋），以支援高讀寫。

driver應用程式將繼續發送更新的汽車位置，這些位置將在此索引中更新。
當rider應用程式請求附近的汽車位置或發出乘車請求時，將查詢該索引。

操作順序如下圖所示。
![[IMG-uber app-20250810200248636.png]]

行程開始前有三個操作順序，如上圖所示： 
i) 更新司機位置 
ii) 列出附近的司機 
iii) 叫車。

第一個操作涉及隨著駕駛員不斷改變其位置而更新駕駛員位置。
第二個包括取得附近司機清單所涉及的步驟。
第三包括涉及請求和調度乘車的一系列步驟。



### **Car Location Index 汽車位置索引**

我們將在分散式索引中維護 driver 位置，該索引分佈在多個節點上。

駕駛員(driver)資訊（包含他們的位置、車內人數以及他們是否在行程中）被聚合為物件。
這些 driver 物件使用某種分片機制（例如，城市、產品等）以及複製來分佈在節點上。
![[IMG-uber app-20250810200250044.png|239]]


每個節點維護一個 driver 物件列表(a list of drivers objects)，可以即時查詢這些物件以取得附近 driver 的列表。
另一方面，當 driver 的 app 發送有關其位置或任何其他相關資訊的更新時，這些物件也會更新。

下面列出一些特徵。
- 它應儲存所有駕駛員的當前位置，並應支援按位置和屬性（例如駕駛員的行程狀態）進行快速查詢。
- 它應該支援大量的讀取和寫入。
- 這些索引中儲存的資料將是短暫的，我們不需要長期的持久性儲存。


Updating and Querying Car Location Index
![[IMG-uber app-20250810200250950.png]]



###  **ETA Calculator 預計到達時間計算器**


我們需要在用戶派車後，考慮路線、交通和天氣等因素，向用戶顯示接送預計到達時間。
在給定道路網路上的起點和目的地的情況下，估算 ETA 的兩個主要組成部分包括 
i) 以最低成本計算從起點到目的地的路線（這是時間和距離的函數）
ii) 估算穿越該路線所需的時間路線

![[IMG-uber app-20250810200252107.png|350]]


我們可以先透過 directed graph 來表示實體地圖，以方便路線計算。
![[IMG-uber app-20250810200255291.png]]


我們可以使用 Dijkstra 等演算法來找到來源和目的地之間的最短路徑。

然而，Dijkstra 演算法找到「N」個節點的最短路徑的時間是 O (NlogN)這種方法對於這些叫車平台的運行規模來說不可行。

我們可以透過對整個圖進行分區(partition)、預先計算分區內的最佳路徑並僅與分區的邊界進行交互來解決這個問題。
> 原文：We can solve this problem by partitioning the entire graph, pre-computing the best path within partitions, and interacting with just the boundaries of the partitions.

在下圖中，我們展示了這種方法可以幫助將時間複雜度從 O (NlgN) 顯著降低到 O (N'lgN')，其中 N' 是 N 的平方根。
![[IMG-uber app-20250810200256595.png|577]]



一旦我們知道了最佳路線，我們就會透過考慮交通狀況來找到穿越路段的估計時間，交通狀況是時間、天氣和現實生活事件的函數。

我們使用此流量資訊來填充圖表上的邊權重，如下圖所示。
![[IMG-uber app-20250810200257581.png|571]]




###  **On-Trip Component 在行程組件**

此組件處理從旅行開始到結束所涉及的過程。
當行程進行時，它會追​​蹤行程的位置。它還負責產生地圖路線（如下圖）並在完成時計算行程成本。
![[IMG-uber app-20250810200258391.png]]



在下圖中，我們顯示了行程進行中和完成時所涉及的操作順序。
司機 app 在騎行過程中發布 GPS 位置，行程記錄器透過 Kafka 串流使用該位置，並保留在位置儲存中。
行程完成後，Map Matcher 使用原始 GPS 位置產生地圖路線。
![[IMG-uber app-20250810200259016.png]]



###  **Location Store/Data Model   位置儲存 / 資料模型**

此儲存組件將負責儲存行程進行期間乘客 / 司機應用程式發送的 GPS 位置流。下面列出了 location store 的一些特色。

- 它應該支援大量寫入。
- 存儲應該是 durable。
- 它應該支援基於時間序列的查詢，例如在給定時間範圍內駕駛員的位置。


我們可以使用 Cassandra 等資料儲存來保存 driver 位置，從而確保長期耐用性。
我們可以拿 時間 作為 pk 的一部分，作為分區鍵來儲存 driver location。
這確保了 driver location 按時間戳排序，因此可以支援基於時間的查詢。
我們可以將位置點作為序列化訊息儲存在資料儲存中。



### **Map Matcher 地圖匹配器**


此模組接收原始 GPS 訊號作為輸入並輸出道路網路上的位置。
輸入的 GPS 訊號包括緯度、經度、速度和航向。另一方面，道路網路上的位置包括緯度（在實際道路上）、經度（在實際道路上）、路段 id、道路名稱和方向 / 航向。

這本身就是一個有趣的挑戰，因為原始 GPS 訊號可能遠離實際的道路網路。

下圖中很少有這樣的例子，左上角的方框對應於高層建築造成的城市峽谷情況，右下角的方框則對應於 GPS 訊號稀疏造成的情況。
地圖匹配器產生的地圖路線用於兩個主要用例：
i) 尋找駕駛員在道路網路上的實際位置 
ii) 計算票價。
![[IMG-uber app-20250810200300282.png]]

下面有提到可以利用隱藏馬可夫模型（HMM）的方法
這邊 先skip



###  **Optimizations 最佳化**


設計的問題之一是大量的讀取和寫入都針對資料儲存。
我們可以透過將原始 driver location 的資料快取在分散式快取 (Redis) 中，將讀取流量與寫入流量分開來優化系統。 
MapMatcher 利用 Redis 快取中的原始 driver location來建立匹配的映射並將其保留在資料儲存中。
![[IMG-uber app-20250810200300439.png|571]]



### improve the predicted ETAs by applying machine learning

應用機器學習的第一個挑戰涉及[特徵工程，](https://en.wikipedia.org/wiki/Feature_engineering?ref=highscalability.com)以提出區域、時間、行程類型和駕駛員行為等特徵來預測實際的預計到達時間。

另一個有趣的挑戰是選擇可用於預測預計到達時間的機器學習模型。

對於這個用例，我們將依賴使用[非線性](https://stats.stackexchange.com/questions/71437/distinction-between-linear-and-nonlinear-model?ref=highscalability.com)（例如，預計到達時間與一天中的時間不線性相關）和 [非參數](https://en.wikipedia.org/wiki/Nonparametric_statistics?ref=highscalability.com)（沒有關於不同功能和預計到達時間之間相互作用的先驗資訊）模型，例如隨機森林、神經網路和 KNN 學習。

我們可以透過在 ETA 預測關閉時監控客戶問題來進一步改善 ETA 預測。


Fun fact：在本次[演講](https://www.youtube.com/watch?v=FEebOd-Pdwg&feature=youtu.be&t=879&ref=highscalability.com)中，Uber 的資料科學家 Sreeta Gorripaty 討論了 ETA 誤差（實際 ETA - 預測 ETA）與地區之間的相關性。
作為[特徵工程](https://en.wikipedia.org/wiki/Feature_engineering?ref=highscalability.com)的一部分，ETA 誤差的機率密度函數 (PDF) 如下圖所示。該圖顯示，與北美相比，印度的分佈尾部更粗。這表明印度的預計到達時間 (ETA) 預測較差，這使得**該地區**成為訓練用於預計到達時間 (ETA) 預測的機器學習模型的重要特徵

圖 skip, no impoetant


### **Addressing Bottlenecks: 解決瓶頸**

在系統中，每個元件都由不同的微服務組成，每個微服務執行單獨的任務。

我們將為駕駛員位置管理、地圖匹配、預計到達時間計算等提供單獨的網路服務。

我們可以透過採用 fallback mechanism 來解決服務故障，從而使系統更具彈性。

一些常見的後備機制包括使用最近快取的數據，甚至在某些情況下使用後備 Web 服務。

混沌工程是彈性測試的常用方法，其中將故障 / 中斷注入系統並監視其效能以進行改進，從而使服務對故障更具彈性
