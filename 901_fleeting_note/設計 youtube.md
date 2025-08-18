related:
- [[youtube  的 db 是如何去優化的]]


feat
• Ability to upload videos fast  
• Smooth video streaming  
• Ability to change video quality
• Low infrastructure cost  
• High availability, scalability, and reliability requirements  
• Clients supported: mobile apps, web browser, and smart TV



Back of the envelope estimation

Assume the product has 5 million daily active users (DAU). • Users watch 5 videos per day.  
• 10% of users upload 1 video per day.  
• Assume the average video size is 300 MB.
• Total daily storage space needed: 5 million * 10% * 300 MB = 150TB
CDN cost.
• When cloud CDN serves a video, you are charged for data transferred out of the CDN.
• Let us use Amazon’s CDN CloudFront for cost estimation (Figure 14-2) [3]. Assume 100% of traffic is served from the United States. The average cost per GB is $0.02. For simplicity, we only calculate the cost of video streaming.
• 5 million * 5 videos * 0.3GB * $0.02 = $150,000 per day.


### upload the actual video
![[IMG-設計 youtube-20250810200251806.png|700]]


### update the metadata
![[IMG-設計 youtube-20250810200254832.png|399]]


### Video streaming flow
![[IMG-設計 youtube-20250810200256156.png|331]]


### Directed acyclic graph (DAG)
![[IMG-設計 youtube-20250810200257303.png|606]]


![[IMG-設計 youtube-20250810200258105.png|329]]


### Video transcoding architecture
![[IMG-設計 youtube-20250810200258877.png|729]]


![[IMG-設計 youtube-20250810200259788.png|594]]


![[IMG-設計 youtube-20250810200300108.png|769]]

![[IMG-設計 youtube-20250810200300974.png]]


### Speed optimization: parallelize video uploading

![[IMG-設計 youtube-20250810200301095.png]]
![[IMG-設計 youtube-20250810200301674.png|625]]


### Speed optimization: place upload centers close to users
![[IMG-設計 youtube-20250810200301911.png|500]]
### Speed optimization: parallelism everywhere
![[IMG-設計 youtube-20250810200302067.png]]

![[IMG-設計 youtube-20250810200302292.png]]


### Safety optimization: pre-signed upload URL
![[IMG-設計 youtube-20250810200302425.png]]

### Cost-saving optimization
![[IMG-設計 youtube-20250810200302577.png|510]]

### Error handling




---


## [Netflix: What Happens When You Press Play? - High Scalability ](https://highscalability.com/netflix-what-happens-when-you-press-play/)


建造資料中心的經驗給了 Netflix 一個重要的教訓 —— 他們不擅長建立資料中心。

Netflix 擅長提供會員影片。 Netflix 寧願專注於更好地提供視頻，而不是更好地建立資料中心。對 Netflix 來說，建構資料中心並不是競爭優勢，
提供影片才是競爭優勢。

當時，Netflix 決定遷移到 AWS。 AWS 剛剛成立，因此選擇 AWS 是一個大膽的舉動。

===

AWS 區域發生故障的頻率是多少？每月一次。嗯，一個區域其實並不是每個月都會故障。 Netflix 每月進行測試。每個月 Netflix 都會故意讓一個區域故障，只是為了確保其係統能夠處理區域層級的故障。一個地區可以在六分鐘內疏散。

===
您的 Netflix 裝置（iPhone、電視、Xbox、Android 手機、平板電腦等）與 EC2 中運行的 Netflix 服務進行通訊。

查看可能觀看的影片清單？這是您的 Netflix 設備正在聯繫 EC2 中的計算機以獲取清單。

詢問有關影片的更多詳細資訊？這是您的 Netflix 設備正在聯繫 EC2 中的計算機以獲取詳細資訊。

就像我們在書中討論的所有其他雲端服務一樣。

===

Netflix 使用 DynamoDB 和 Cassandra 作為其分散式資料庫。這些名稱對您來說並不意味著什麼，它們只是高品質的資料庫產品。

資料庫. 資料庫儲存資料。您的個人資料資訊、帳單資訊、您看過的所有電影，所有此類資訊都儲存在資料庫中。


===

你和我可能是非常不同的人。您認為我們是受到同一種標題圖像的激勵嗎？可能不會。我們有不同的口味。我們有不同的偏好。

Netflix 也知道這一點。這就是 Netflix 現在對向您展示的所有圖像進行個性化處理的原因。 Netflix 會嘗試選擇突出影片中與您最相關的方面的藝術作品。

如果你喜歡喜劇，Netflix 會向你展示羅賓威廉斯主演的圖片。如果您更喜歡浪漫電影，Netflix 會向您展示馬特達蒙和米妮德賴弗準備接吻的畫面。

===


在您可以在您喜愛的裝置上觀看影片之前，Netflix 必須將影片轉換為最適合您的裝置的格式。這個過程稱為_轉碼_或_編碼_。(_transcoding_ or _encoding_)

轉碼是將影片檔案從一種格式轉換為另一種格式的過程，使影片可以在不同平台和裝置上觀看。

Netflix 在 AWS 中同時在多達 30 萬個 CPU 上對其所有影片進行編碼。這比大多數超級電腦都大！

==

Netflix 做的第一件事就是花大量時間驗證影片。它會尋找可能由先前的轉碼嘗試或資料傳輸問題引起的數位偽影、顏色變化或遺失影格。

如果發現任何問題，影片將被拒絕。

影片經過驗證後，會被輸入 Netflix 所謂的媒體管道。

管道只是數據經過一系列步驟以使其可供使用的過程，就像工廠中的裝配線一樣。每個影片的製作都有 70 多種不同的軟體參與。

處理單一多 TB 大小的檔案是不切實際的，因此管道的第一步是將影片分成許多較小的區塊。

然後視訊區塊通過管道，以便可以並行編碼。並行只是意味著同時處理區塊。

==
編碼過程會創建很多文件。為什麼？ Netflix 的最終目標是支援所有連網裝置。

Netflix 於 2007 年開始在 Microsoft Windows 上提供串流影片。隨著時間的推移，更多設備被添加 ——Roku、LG、三星藍光、Apple Mac、Xbox 360、LG DTV、Sony PS3、Nintendo Wii、Apple iPad、Apple iPhone、Apple TV、Android、Kindle Fire 和 Comcast X1。

Netflix 總共支援 2200 種不同的裝置。每個設備都有一種在該特定設備上看起來最好的視訊格式。如果您在 iPhone 上觀看 Netflix，您將看到為您在 iPhone 上提供最佳觀看體驗的影片。

Netflix 將影片的所有不同格式稱為_編碼設定檔_(_encoding profile_)。


Netflix 也建立針對不同網路速度進行最佳化的檔案。如果您在快速網路上觀看，您將看到比在慢速網路上觀看時更高品質的影片。

還有不同音訊格式的檔案。音訊被編碼成不同的品質等級和不同的語言。

還包含字幕檔。影片可能有多種不同語言的字幕。

每個影片都有許多不同的觀看選項。您看到的內容取決於您的裝置、網路品質、Netflix 套餐和語言選擇。

===

對於_《王冠》，_ Netflix 儲存了大約 1,200 個文件！

《怪奇物語》第二季有更多文件。它以 8K 拍攝，共有九集。來源視訊檔案有很多很多 TB 的資料。光是一季編碼就花了 19 萬個 CPU 小時。

結果呢？ 9,570 個不同的視訊、音訊和文字檔案！


==

讓我們從定義 CDN 開始。 CDN 是一個_內容傳遞網路_。

"傳遞" 意味著視訊檔案透過網路從中央位置複製並儲存在世界各地的電腦上。

對於 Netflix，儲存影片的中心位置是 S3。


CDN 背後的想法很簡單：透過將電腦分佈到世界各地，讓影片盡可能靠近使用者。當用戶想要觀看影片時，找到最近的有影片的電腦並從那裡串流到裝置。

CDN 的最大優勢是速度和可靠性。

為了支援串流服務，Netflix 在美國五個不同的地點建立了自己的簡單 CDN。

當時 Netflix 影片目錄很小，每個位置都包含其所有內容。

2009 年，Netflix 決定使用第 3 方 CDN。大約在這個時候，第三方 CDN 的價格正在下降。

Netflix 與 Akamai、Limelight 和 Level 3 等公司簽約，提供 CDN 服務。使用第三方 CDN 沒有任何問題。事實上，幾乎每家公司都這樣做。例如，NFL 使用 Akamai 直播橄欖球比賽。

透過不建立自己的 CDN，Netflix 有更多的時間來處理其他更高優先事項的項目。

Netflix 投入了大量時間和精力來開發更聰明的用戶端。 Netflix 創建了適應不斷變化的網路條件的演算法。即使面臨錯誤、網路超載和伺服器超載，Netflix 也希望會員始終能夠觀看到最好的畫面。 Netflix 開發的一項技術是切換到不同的視訊來源（例如另一個 CDN 或不同的伺服器）以獲得更好的結果。


2011 年，Netflix 意識到其規模需要專用的 CDN 解決方案來最大限度地提高網路效率。影片分發是 Netflix 的核心競爭力，也可能成為巨大的競爭優勢。

因此，Netflix 開始開發 Open Connect，這是它自己的專用 CDN。 Open Connect 於 2012 年推出。


Open Connect 對 Netflix 來說有很多優勢：

- 更便宜。第三方 CDN 價格昂貴。自己做的話會省很多錢。
- 品質更好。 Netflix 認為，透過控制整個視訊路徑（轉碼、CDN、裝置上的用戶端），它可以提供卓越的視訊觀看體驗。
- 更具可擴展性。 Netflix 的目標是在世界各地提供服務。要快速支援所有這些人，同時提供高品質的視訊觀看體驗，需要建立自己的系統。


Netflix 開發了自己的視訊儲存電腦系統。 Netflix 稱之為「開放式連線裝置」或「OCA」(Open Connect Appliances)。
每個 OCA 都是一個快速伺服器，針對傳輸大檔案進行了高度最佳化，並配備大量硬碟或快閃磁碟機來儲存影片。


站點中早期 OCA 安裝的樣子
![[IMG-設計 youtube-20250810200302805.png|372]]


有幾種不同類型的 OCA 用於不同的目的。有一些大型 OCA 可以儲存 Netflix 的整個影片目錄。有些較小的 OCA 只能儲存 Netflix 影片目錄的一部分。較小的 OCA 每天在非高峰時段都充滿視頻，使用 Netflix 稱為_主動快取的_流程。稍後我們將詳細討論主動快取的工作原理。

從硬體角度來看，OCA 並沒有什麼特別之處。它們基於商用 PC 組件，並由不同的供應商組裝在定制外殼中。如果您願意，您可以購買相同的計算機。


從軟體角度來看，OCA 使用 FreeBSD 作業系統和 NGINX 作為 Web 伺服器。是的，每個 OCA 都有一個網頁伺服器。使用 NGINX 進行視訊串流。

網站上的 OCA 數量取決於 Netflix 希望該網站的可靠性、從該網站傳送的 Netflix 流量（頻寬）以及網站允許串流的流量百分比。

當您按下播放鍵時，您將觀看來自您附近位置的特定 OCA（如上面的 OCA）的視訊串流。


===


其他影片服務（例如 YouTube 和 Amazon）在自己的主幹網路上提供影片。這些公司實際上建立了自己的全球網絡，用於向用戶提供視訊。這是非常複雜且非常昂貴的事情。

Netflix 採用了完全不同的方法來建立其 CDN。

Netflix 無法經營自己的網路；它也不再經營自己的資料中心。相反，網際網路服務供應商 (ISP) 同意將 OCA 放入其資料中心。 OCA 免費提供給 ISP 以嵌入其網路中。 Netflix 也將 OCA 放置在網際網路交換位置 (IXP) 或附近。

使用這種策略，Netflix 不需要經營自己的資料中心，但它可以獲得常規資料中心的所有好處 —— 它只是別人的資料中心。天才！


**使用 ISP 建置 CDN。**

ISP 是您的網際網路供應商。這是您從那裡獲得互聯網服務的人。它可能是 Verizon、康卡斯特或數千種其他服務。

這裡的要點是 ISP 遍布世界各地，並且距離客戶很近。透過將 OCA 放置在 ISP 資料中心，Netflix 也遍佈全球，並貼近其客戶。


Netflix 將所有這些影片保存在 S3 中。他們擁有遍布世界各地的所有視訊服務電腦。只缺少一件事：影片！

Netflix 使用一種稱為主動快取(_proactive caching_)的流程來有效地將影片複製到 OCA。


每個 OCA 都是您最可能想觀看的影片快取。

Netflix 透過預測您想要觀看的內容來快取影片。**

在世界各地，Netflix 都非常準確地了解其會員喜歡觀看什麼內容以及何時觀看。還記得我們說過 Netflix 是一家數據驅動的公司嗎？

Netflix 使用其受歡迎程度數據來_預測_會員明天可能想在每個地點觀看哪些影片。這裡，_位置_是指位於 ISP 或 IXP 內的 OCA 群集。

Netflix 將預測的影片複製到每個位置的一個或多個 OCA。這稱為_前置_。影片在任何人詢問之前就已放置在 OCA 上。

This gives great service to members. The video they want to watch is already close to them, ready and available for streaming.  

Netflix 運行所謂的_分層快取系統。

![[IMG-設計 youtube-20250810200302926.png|670]]


在一個地點內，像《紙牌屋》這樣的熱門影片會被複製到許多不同的 OCA。影片越受歡迎，它就會被複製到越多的伺服器。為什麼？如果一個非常受歡迎的影片只有一份副本，將影片串流傳輸給會員將會導致伺服器不堪重負。正如人們所說，人多力量大。

如果視訊僅複製到一個 OCA，則該視訊不會被視為即時視訊。 Netflix 希望能夠在世界各地同時播放相同的內容。只有當有足夠數量的 OCA 並擁有足夠的影片副本來適當提供服務時，該影片才會被視為已上線並可供會員觀看。


例如，2016 年的_《夜魔俠》_第二季是 Netflix 首次在所有國家 / 地區的所有裝置上同時發布節目的所有劇集。


==

為什麼 ISP 會同意在其網路中放置 OCA 叢集？乍一看，這似乎過於慷慨，但您會很高興知道它牢固地植根於自身利益。


Netflix 透過 Open Connect 所做的就是將其 OCA 叢集放置在 ISP 網路中。這意味著如果我觀看 Netflix 視頻，我將與康卡斯特網絡中的 OCA 交談。我所有的視訊流量都在康卡斯特的網路上；它永遠不需要接入主幹網路。

擴展視訊交付的關鍵是盡可能靠近用戶。當您這樣做時，您並沒有使用互聯網主幹網路。請求正在網路的本地部分得到滿足。

為什麼這是一件好事？回想一下，我們說過 Netflix 已經消耗了美國超過 37% 的網路流量。如果 ISP 不合作，Netflix 將使用更多的網路。網路無法處理所有視訊流量。 ISP 必須增加更多的網路容量，而建造成本很高。

目前，高達 100% 的 Netflix 內容是透過 ISP 網路提供服務的。這可以透過緩解 ISP 的網路擁塞來降低成本。同時，Netflix 會員也能體驗到高品質的觀賞體驗。每個人的網路效能都會得到改善。

It’s a win-win. 


==


## **Netflix 控制客戶端**

Netflix 可以優雅地處理故障，因為它控制著運行 Netflix 的每個裝置上的用戶端。

Netflix 自己開發 Android 和 iOS 應用程序，因此您可能希望他們能夠控制這些應用程式。但即使在智慧電視等 Netflix 不建置客戶端的平台上，Netflix 仍然擁有控制權，因為它控制著_軟體開發工具包_ (SDK)。

SDK 是_一組允許創建應用程式的軟體開發工具。_每個 Netflix 應用程式都會向 AWS 發出請求並使用 SDK 播放影片。

透過控制 SDK，Netflix 可以一致且透明地適應緩慢的網路、失敗的 OCA 以及可能出現的任何其他問題。


==

## **Finally: Here’s What Happens when You Press Play  
最後：按下 “播放” 鍵後會發生什麼**

到達這裡是一條漫長的路。我們學到了很多。以下是我們到目前為止所了解到的內容：

- Netflix 可以分為三個部分：後端、客戶端、CDN。
- 來自 Netflix 用戶端的所有請求都在 AWS 中處理。
- 所有影片均從 Open Connect CDN 中附近的 Open Connect Appliance (OCA) 進行串流傳輸。
- Netflix 在三個 AWS 區域中運營，通常可以在成員不知情的情況下處理任何區域的故障。
- Netflix 將新的影片內容轉換為多種不同的格式，因此可以根據裝置類型、網路品質、地理位置和會員的訂閱方案選擇最佳格式進行觀看。
- Netflix 每天都會透過 Open Connect，根據他們預測的每個地點的會員想要觀看的內容，在世界各地分發影片。

以下是 Netflix 描述播放過程的圖片：

![[IMG-設計 youtube-20250810200303120.png|779]]


現在，讓我們完成這張圖：

- 您可以使用在某些裝置上執行的用戶端選擇要觀看的影片。用戶端向在 AWS 中執行的 Netflix 播放應用程式_服務發送_播放_請求，指示您要播放哪個影​​片。
- 我們之前沒有討論過這一點，但您開始玩後發生的事情很大一部分與許可有關。並非世界上的每個地點都有觀看每個影片的許可證。 Netflix 必須確定您是否擁有觀看特定影片的有效許可證。我們不會談論它是如何工作的 —— 這真的很無聊 —— 但請記住它總是在發生。 Netflix 開始開發自己的內容的原因之一是為了避免授權問題。 Netflix 希望同時向世界上的每個人發布一個節目。創建自己的內容是 Netflix 避免擔心授權問題的最簡單方法。
- 考慮到所有相關訊息，播放應用程式服務會傳回最多 10 個不同 OCA 伺服器的 URL。這些 URL 與您在網頁瀏覽器中一直使用的 URL 類型相同。 Netflix 使用您的 IP 位址和 ISP 的資訊來確定哪些 OCA 叢集最適合您使用。
- 客戶端智慧地選擇要使用的 OCA。它透過測試每個 OCA 的網路連接品質來實現這一點。它將首先連接到最快、最可靠的 OCA。客戶端在整個視訊流程處理過程中不斷執行這些測試。
- 用戶端探索找出從 OCA 接收內容的最佳方式。
- 用戶端連接到 OCA 並開始將視訊串流傳輸到您的裝置。
- 您是否注意到觀看影片時影像品質有所不同？有時它會看起來像素化，過了一會兒圖片又恢復到高清品質？這是因為客戶端正在適應網路品質。如果網路品質下降，用戶端會降低視訊品質以進行匹配。當品質下降太多時，客戶會切換到另一個 OCA。

