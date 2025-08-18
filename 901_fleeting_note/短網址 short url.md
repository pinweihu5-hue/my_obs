


## alex book

###  需求
- 你丟這個網址(e.g. `https://www.systeminterview.com/q=chatsystem&c=loggedin&v=v3&l=long` )給這個服務, 他會變成短網址(e.g. `https://tinyurl.com/y7keocwj`)。你visit短網址, 回 redirect 去原本的網址。
- 100 M url 產生 per day
- 可以存 10 yr 的量
- 越短越好
- 短網址要好打，因此內容是`A-Za-z0-9`
- redirection shall low latency
- how to shorten the url shall unpredictable
	- if you can predict, 這樣任何人都可以在後面擺上別的alphnumic 去存取到當初創建人不希望被存取的頁面 影響到分析 跟 使用者的信任




###  Back of the envelope estimation
- Write: 一天 100M, qps 約是: 1157  ~> 1200
- read: 大多數的人都是使用, 假設 10 比 1 -> 因此 12000
- 10年需要 365* 10 ^9 -> 365 Billion 個 url
- 需要多少 storage?
	- 假設一個url約 100 bytes
	- 1年需要  365 billion * 100 -> 365 * 10^9 * 10^2 -> 365 * 10^11 -> 36.5 * 10^12  -> 36.5 TB
		- ref: [[power of 2]]
		- (3-> 6-> 9->12->15)
		- (kb, mb, gb, tb, pb )




###  API endpoints:
- POST api/v1/url/shorten
	- request parameter: {longUrl: `https://www.systeminterview.com/q=chatsystem&c=loggedin&v=v3&l=long`}
	- return { result: success, data: `https://tinyurl.com/y7keocwj`}
- GET api/v1/url
	- return  { result: success, data: `https://www.systeminterview.com/q=chatsystem&c=loggedin&v=v3&l=long`}



###  redirct 機制:
當你 輸入短網址後按下 enter:
301 redirect
- [[RESTful API  設計#Response Handling]]

- HTTP 301 Moved Permanently 重新導向回應碼表示所請求的資源已被永久地移動到由 Location 標頭給出的 URL。瀏覽器將重新導向到新的 URL，並且搜索引擎會更新對該資源的連結。
- HTTP 302 Found 重新導向回應碼表示所請求的資源已暫時移動到由 Location 標頭給出的 URL。瀏覽器將重新導向到此頁面，但搜索引擎不會更新對該資源的連結
![[IMG-短網址 short url-20250810200251134.png]]

![[IMG-短網址 short url-20250810200252198.png]]

選 301 永久 redirect，後續訪問會瀏覽器自動幫你轉向，但是你就失去分析，因為不會打到 server了
看需求。

Todo: check popular short url system which they choose 301. 302
###  how shorten long URL shorten

基本思路就是 hash
![[IMG-短網址 short url-20250810200255383.png]]

如何選 hash function?
因為要 A-Za-z0-9, 共 62個, 共有多少組合看你要多長?
n = 6 -> 56 Billion, 我們需要 365 billion, 因此需要 n = 7
![[IMG-短網址 short url-20250810200256688.png]]


我們可以把 url 取 hash(md5, CRC32等等) ->  取前7個 -> 但會有conflict -> 需要偵測衝突重新 gen

如下
gen 好後，如果發現 db 有了，需要重新建立
這樣建立的速度，如果撞到重複，就會不夠快
![[IMG-短網址 short url-20250810200257585.png|659]]




另一個思路是用 base62 + unique ID generator

我們把  unique ID generator 產出來的 numeric id 用 base62 轉化就是短網址

一個例子

- unique ID generator 產出 11157
- 11157 mod 62 ->59
- 59 -> base62 -> X
![[IMG-短網址 short url-20250810200258401.png]]


### 兩個 gen 短網誌的方案比較

| 唯一id 產生器+base62   | bash + 衝突解決機制 |
| ----------------- | ------------- |
| 短網誌的長度會隨著id變長而變長  | 短網誌的長度固定      |
| 需要 唯一id產生器        | 不需要唯一id產生器    |
| 不需要衝突解決機制, 因為id唯一 | 需要衝突解決機制      |



==

下面是用  唯一id 產生器+base62 的方案的流程圖
![[IMG-短網址 short url-20250810200259020.png]]




### DB  
下面的 id 可以就是上面的 unique ID generator 產生的押上去
![[IMG-短網址 short url-20250810200300613.png]]

也可以用一個 crobjob 去清理很久都沒用過的的 url
類似我們  api 設計可以押上一個 expired yr or default expired 是 2yr



![[IMG-短網址 short url-20250810200300772.png]]



### telemery
what stat we might interested (profitable or gaine UX)
- how many times a short url been used
- where was the user location?
- some statics is good to know
    - number of url visit
    - datetime of url access
    - url visit






# 另一個 短網址作法


在長網址後面加個 increasing number 再 hash 來確保唯一性



# TinyURL 的可能作法?



TinyURL 或其他短網址服務，將長網址轉換成短網址的核心原理是建立一個長網址和短網址之間的**雙向映射**，並將這個映射關係儲存在資料庫中。  以下詳細說明：

1. **雜湊 (Hashing) 或亂數產生:** 
	- 當使用者提交一個長網址時，系統會使用一個演算法，例如雜湊函數 (例如 MD5, SHA1, 但考量碰撞機率，現在更傾向於 murmurhash, xxhash 等) 或亂數產生器，將長網址轉換成一個較短的字串。  
	- 理想的雜湊函數應該盡量減少碰撞 (不同的長網址產生相同的短網址) 的可能性。

3. **編碼 (Encoding):**  
	- 雜湊或亂數產生的結果通常是一串數字，為了更方便使用和記憶，系統會將其編碼成一個包含數字、字母的短字串。 常見的編碼方式是 Base62 (0-9, a-z, A-Z) 或 Base64。  
	- Base62 可以產生更短的網址，而 Base64 則更常用於資料編碼。

5. **資料庫儲存:** 系統會將長網址和生成的短網址的對應關係儲存在資料庫中。  資料庫表結構通常包含以下欄位：

    * `id` (主鍵，自動遞增)
    * `long_url` (原始長網址)
    * `short_url` (生成的短網址)
    * `created_at` (创建时间)  (可选)

6. **短網址重定向:** 當使用者訪問短網址時，系統會在資料庫中查找對應的長網址。  如果找到匹配項，系統會將使用者重定向到原始的長網址。  這個過程通常使用 HTTP 301 (永久重定向) 或 302 (臨時重定向) 狀態碼來完成。

**簡化流程圖：**

```
長網址 --> [雜湊/亂數產生] --> 數字 --> [編碼] --> 短網址 --> [儲存至資料庫]

短網址 --> [資料庫查找] --> 長網址 --> [重定向]
```


**處理碰撞 (Collision):**

即使使用良好的雜湊函數，也可能發生不同的長網址產生相同的短網址的情況，這就是碰撞。  處理碰撞的常見方法包括：
* **在雜湊值後附加計數器或亂數字串:**  如果發生碰撞，就在雜湊值後面附加一個數字，例如 `abc1`, `abc2` 等，直到找到一個未使用的短網址。
* **重新雜湊:**  使用不同的雜湊函數或不同的鹽值 (salt) 重新計算雜湊值。
* **增加短網址長度:**  使用更長的短網址可以減少碰撞的機率。


**其他考量：**
* **自訂短網址:**  有些服務允許使用者自訂短網址的一部分，這通常需要檢查資料庫以確保自訂的短網址尚未被使用。
* **統計數據:**  許多短網址服務會記錄點擊次數、來源等統計數據。


總而言之，短網址服務的核心是利用雜湊/亂數產生、編碼和資料庫查詢來實現長網址和短網址之間的快速轉換和重定向。  理解這些基本原理，就能夠理解 TinyURL 或其他類似服務的運作方式。




## link
- [系統設計 - 設計縮網址服務・jyt0532's Blog](https://www.jyt0532.com/2019/12/05/design-tiny-url/)
- [(4) URL shortener system design | tinyurl system design | bitly system design - YouTube](https://www.youtube.com/watch?v=JQDHz72OA3c)
- [(4) 系统设计(五) Design Tiny URL System - YouTube](https://www.youtube.com/watch?v=Gz-kAjmr7M0&list=PLbaIOC0vpjNUM49bXSCbN-ap7UrfhZgCO&index=6)
- [(4) How to implement TinyURL (System Design Interview) - YouTube](https://www.youtube.com/watch?v=eCLqmPBIEYs)
- [(4) System Design: URL Shortener Service - YouTube](https://www.youtube.com/watch?v=He-V_RuHwek)