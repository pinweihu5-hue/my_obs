
# What it is

![[IMG-RESTful API  設計-20250810200241669.png|604]]

1. **資源導向**：RESTful API 將所有內容視為資源，每個資源都有一個唯一的 URI（統一資源識別符）來標識。
2. **無狀態**：每個 API 請求都是獨立的，服務器不會在請求之間保留客戶端的任何狀態。這意味著每次請求都必須包含完成該請求所需的所有信息。
3. **使用 HTTP 方法**：RESTful API 使用 HTTP 方法來執行不同的操作。
4. **數據格式**：RESTful API 通常使用 JSON 或 XML 格式來傳輸數據，因為這些格式易於閱讀和解析。
5. **統一接口**：RESTful API 定義了一組統一的接口約束，這使得不同的客戶端能夠以一致的方式與服務器交互。



## HTTP 方法

![[IMG-RESTful API  設計-20250810200243049.png]]
PS: 
- safe mean no side effect, no state change, ready only
- Idempotent means 這樣的操作執行一次跟執行很多次的結果是一樣的。而HTTP method中，除了POST，其他method都預期應該是Idempotent的，Update好幾次，Delete好幾次，應該都跟只做一次的結果一致．

- GET
	- 顧名思義就是用來向server索取一些資源
	- Cacheable
	- 有資源，server就回應200以及資源，沒有資源，404
- HEAD
	- 跟GET都一樣，但server並不回傳資源本身，而是只回應headers
	- Cacheable

```text

<Response>
HTTP/1.1 200 OK
Content-Type: plain/html
Content-Length: 614

```



- OPTIONS
	- 用來詢問server對特定的資源，有支援哪些操作。

example:
```text
<Response>
HTTP/1.1 200 OK
Allow: GET, POST, PUT, OPTIONS
Content-Length: 0


```


- PUT
	- Replace the entire resource，對應到CRUD裡的update，就是叫server將我request body裡的資料存起來，如果資源本來不存在就create，在的話就replace．
	- 200 OK，成功
	- 204 No Content，成功但畫面不需要更新，也就是沒有新的資料產生，client不必取的新的資料，例如說client在更新表單按下save時．
	- 201 Created，如果是create的話，並且在header Location放上資源的URI
```text

<Response>
HTTP/1.1 201 Created
Location: https://api.example.com/users/123 


```


- PATCH
	- Update parts of the resource，跟PUT一樣是update，不過是partial update或者說是merge。
	- 比如說原本有個homuchen的user是長這樣：
```
{
    "name": "HoMuChen",
    "age": 30
}

```

- 當你PATCH時，想要將age更新為31
```text
<Request>
PATCH /users/homuchen HTTP/1.1
Content-Type: application/json
Content-Length: 11

{"age": 31}

```

結果為
```

{
    "name": "HoMuChen",
    "age": 31
}

```

如果是用PUT，則會整個replace掉，name就不見了
```

{
    "age": 31
}

```


- POST
    - 定義是: Perform resource-specific processing on the request payload
	- 一般來說是指把資料傳給server去做處理，當使用其他的method，語意無法符合時，就會使用POST，常見的有用於Create resources、Search query、Asynchronous tasks．
	- 200 OK
	- 201 Created
	- 202 Accepted，server已接受請求，但還需要時間去完成任務．

- DELETE
	- 顧名思義，用來刪除資源．
	- 200 OK．
	- 207，bulk delete．
	- 404 Not Found，要刪除的資源不存在．
	- 410 Gone，資源已被刪除．
- 使用 PUT、POST 和 DELETE 方法而不是 GET 方法來變更狀態




## 資源設計和命名
- 使用名詞表示資源：
	- 資源 URI 應該使用名詞而不是動詞，因為 URI 表示資源的地址。例如，使用 `/books` 而不是 `/getBooks`。
- 使用複數形式：
	- 通常使用複數形式來表示資源集合，例如 `/users` 而不是 `/user`，即使集合中只有一個元素。
- 層次結構表達資源關係：
	- 使用層次結構的 URI 來表示資源之間的關係。
	- 例如
	- /users/{userId}/orders
	-  /articles/:articleId/comments
	- GET /groups/{group id}/members → Returns a list of members in group with given id
	- GET /groups/{group id}/members/{member id} → Returns user with given user id for given group id
- 簡潔和一致性：
	- 保持 URI 簡潔且一致，避免不必要的層級和冗長的名稱。例如，使用 `/products` 而不是 `/allProductsList`.
- 使用標準格式：
	- 使用小寫字母和連字符（-）來分隔單詞，避免使用駝峰式命名或下劃線。例如，使用 `/product-categories` 而不是 `/productCategories` 或 `/product_categories`。
- 標識具體資源：
	- 當需要標識特定資源時，使用唯一標識符（通常是 ID）。例如，`/users/{userId}`。
- 支持過濾和排序：
	- 如果需要支持過濾和排序，可以使用查詢參數。例如，`/products?category=electronics&sort=price_asc`。
- 避免深層次的嵌套：
	- 避免過於複雜的嵌套 URI，這樣會使得 API 難以理解和使用。通常建議不要超過三層嵌套。
	- 資源使用名詞而非動詞。例如，`/users` 而不是 `/getUsers`。
	- 使用層次結構來表示資源的關係，例如：`/users/{userId}/orders`。
- 常用 query 可以考慮建立別名
    - 為了讓 API 體驗對於一般消費者來說更加愉快，請考慮將條件集打包到易於存取的 RESTful 路徑中。例如，當查詢最活躍、建議的群組等時，我們可以擁有類似的端點
        - GET /groups/most-active → _Returns list of most active groups_
- 拿很多用複數
    - GET /articles
    - 拿一個的話： GET /article/:id
- API 要有合理的 sorting, paging, filter,  等機制. 例子：
	- 分頁
	    - GET /groups?offset=20&limit=20
	    - [[REST API Design Pagination]]
	- 欄位
	    - GET /groups?status=active
	- sort
	    - GET /groups?sort=status,-name
	        - Returns list of groups in ascending order of status
	        - Within the same status, groups returned will be sorted by name in descending order
	- query
		- GET /groups/?status=active&sort=-name&q=test
        - Return list of all active groups sorted by name (desc) via `sort=-name`
        - which contain ‘test’ in their names `q=test`
	- 上面都有
	    - GET /groups/?fields=id,name,owner,status&status=active&sort=-name
- 無法用 CRUD 表示
    - Use PATCH to apply the set of changes described in the patch document (request payload)
        - *PATCH  /groups/{group id}       payload: {“action”:”activate|deactivate”}*
    - the activate/deactivate group action can be handled via a PUT to the sub resource
        - *PUT  /groups/{group id}/status         payload: {“status”:”active”|”inactive”}*
    - 有時您確實無法將操作對應到合理的 RESTful 結構。
        - 例如，將多資源搜尋應用於特定資源的端點並沒有真正的意義。在這種情況下，即使 `/search` 不是資源，它也是最有意義的。
        - 這沒關係 —— 只需從 API 用戶的角度做正確的事情，並確保清楚地記錄下來以避免混淆。
	- 比如說你想要lock及unlock某篇文章，此時只有一個人可以讀取或修改它， HTTP Method裡沒有可以表示lock及unlock的操作，所以就自己創造一個資源叫article-locks， 並使用PUT、DELETE來操作。
		- PUT     /article-locks/{article_id}     -> Lock住某篇文章
		- DELETE /article-locks/{article_id} -> Unlock某篇文章
- 牽涉多個資源
	- 當你的一個任務需要同時對多個資源進行操作時，有兩種做法，一個是讓client發出多個api請求， 另一個則是定義一個新的endpoint，來幫使用者做完所有的事。比如說API的使用者想要發起一筆交易，需要從A帳戶扣一筆錢，在B帳戶新增一筆錢， 如果讓client自己送出兩個api請求也可以，但是他就要處理其他許多狀況，比如說其中一個api成功，另一個失敗怎麼辦。

```
#還要確保兩個api request同時成功或同時失敗

PUT     /accouts/A/withdrawal   -> 從A帳戶扣一筆錢
PUT     /accouts/B/deposit      -> 在B帳戶新增一筆錢

```
- 或者可以創造一個新的資源叫transaction，使用PUT來發起一筆交易，而api server則幫client處理了所有該注意的事， 使這個交易atomic、檢查A帳戶裡的餘額夠不夠等等…

```

PUT /transactions -> 發起一筆交易

```


## Response Handling
- 基本的要懂 [Common HTTP Status Codes | saipraveenblog](https://saipraveenblog.wordpress.com/2014/09/29/common-http-status-codes/)
	- 2xx
		- 200 OK
		- 201 資訊建立:Response to a POST that results in a creation
		- 202 Accepted: 代表你成功接受了請求，然後會asynchronously地處理。
		- 204 沒有內容:沒有response body，會用到情境像是使用**POST**或**PATCH**做update時， 成功的話，server沒有額外的資訊需要return給client知道。
	- 3xx
		- 301 永久重定向: 表示所請求的資源已被永久地移動到由 Location 標頭給出的 URL。瀏覽器將重新導向到新的 URL，並且搜索引擎會更新對該資源的連結
		- 302 Found，臨時重新導向: 重新導向回應碼表示所請求的資源已暫時移動到由 Location 標頭給出的 URL。瀏覽器將重新導向到此頁面，但搜索引擎不會更新對該資源的連結
		- 304 資源沒有修改:   Used when HTTP caching headers are in play
	- 4xx
		- 400 bad request: 請求的內容有誤, 最好也要要跟前端說為何錯誤
		- 401 Unauthorized : Authenticate失敗，例如帳號密碼打錯呀，Token不對呀。
		- 403 禁止: User有驗證成功，但他沒有足夠的權限來做這個請求
		- 404 not found: 請求的資源不存在
		- 405 Method Not Allowed: 對於請求的資源，不支援所要求的method。
		- 429 too many req: rate limiting triggered
			- 429 回應可以提供更多有關應用程式限制原因的詳細資訊（例如，免費增值用戶的配額較低，或系統正在進行維護）。
			- 通常也可以考慮是否要加上對應的 header
	- 5xx
		- 500 internal error: when an unexpected condition was encountered, 系統層級的問題, app level is not able to know
		- 501 Not Implemented 
		- 502 Bad Gateway:upstream server 無法正確回應 gateway, 或是 upstream server 和 gateway網路有狀況, overload, config error
		- 503 Service Unavailable: The server is currently unavailable (because it is overloaded or down for maintenance)
		- 504 Gateway Timeout: upstream server 回應 gateway 的時間 timeout了
			- [[504 gateway error 可能會是什麼原因]]
- 成功欄位要獨立出來，方便判定
    `{result: success/fail, data: {…}}`
- error code 以外，可以有 msg or detail info 更好， like below
```
{ 
	"code" : 1234,
	"message" : "Something bad happened"
	"description" : “More details about the error here"
}
```


## 版本管理
- 設計 API 時考慮版本控制，以便在未來進行升級時不會破壞現有用戶端。常見做法是在 URI 中包含版本號，例如：**`/v1/users`**。
- 另一種方法是通過 HTTP 標頭來管理版本。


## 安全考慮
- 最小權限原則
- 不相信前端拿的任何東西，都要validate
- 可以限制格式就要限制
- 意義上的限制（類似預定某個東西, 會不會只能是今天以後才可以限制)
- regex 盡量不要用 `+, *, .`   → 用具體的match pattern  譬如說我想用 \d{5} 來檢查輸入是不是五位數字 → 越具體越好
- mail 相關的
	- consider block gmail的 sub account
	- consider block disposal mail
- 檔案上傳限制大小，確認 mime type
- 使用 HTTPS 來對數據加密，預防中間人攻擊。
- 實施身份驗證和授權機制，例如 OAuth 2.0，以確保只有授權用戶才能訪問 API。
	- [[Oauth]]
- 考慮使用速率限制來防止濫用。
	- 關連 header
		- X-Rate-Limit-Limit- The number of allowed requests in the current period  目前時段允許的請求數
		- X-Rate-Limit-Remaining – The number of remaining requests in the current period  目前期間剩餘請求數
		- X-Rate-Limit-Reset – The number of seconds left in the current period  目前週期剩餘的秒數
- [[http header for enhance security]]
- [[Access-Control-Allow-Credentials]]
- [Day02 - 入口管制（一）](https://ithelp.ithome.com.tw/articles/10265902)
- [Day03 - 入口管制（二）](https://ithelp.ithome.com.tw/articles/10266665)
- [Day04 - 入口管制（三）](https://ithelp.ithome.com.tw/articles/10267428)
- [Day05 - 入口管制（四）](https://ithelp.ithome.com.tw/articles/10268192)


## 性能優化
- 使用緩存來提高性能，通過 HTTP 標頭（如 ETag、Cache-Control）實現。
	- see [[cache_idx]]
- 分頁，以減少大型數據集的傳輸量。
	- see [[REST API Design Pagination]]


more:
- [What are HTTP Methods？Which to use and How to use them correctly? | HoMuChen](https://homuchen.com/posts/http-methods-which-to-use-and-how-to-use-them-correctly/)
- [API-Security-Checklist/README-tw.md at master · shieldfy/API-Security-Checklist](https://github.com/shieldfy/API-Security-Checklist/blob/master/README-tw.md)
- [REST API Best practices | saipraveenblog](https://saipraveenblog.wordpress.com/2014/09/29/rest-api-best-practices/)
- [Best practices for REST API design - Stack Overflow](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/)
- [Best Practices for rest APIs. This article is composed of a summary… | by Jolly Fish | Technically True🚀 | Medium](https://medium.com/technically-true/best-practices-for-rest-apis-b6d22d36d327)
- [awesome-software-architecture/docs/rest.md at main · mehdihadeli/awesome-software-architecture](https://github.com/mehdihadeli/awesome-software-architecture/blob/main/docs/rest.md)
- [RESTful API 设计指南 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)
- [程式設計 - 簡明 RESTful API 設計要點 - Twincl](https://tw.twincl.com/programming/*641y)
- [淺談 REST 軟體架構風格 (Part.I) - 從了解 REST 到設計 RESTful！ - Soul & Shell Blog](https://blog.toright.com/posts/725/representational-state-transfer-%E8%BB%9F%E9%AB%94%E6%9E%B6%E6%A7%8B%E9%A2%A8%E6%A0%BC%E4%BB%8B%E7%B4%B9-part-i-%E5%BE%9E%E4%BA%86%E8%A7%A3-rest-%E5%88%B0%E8%A8%AD%E8%A8%88-restful%EF%BC%81.html)
- [Day26 - RESTful 架構開發 與 MVC 設計 - iT 邦幫忙：：一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10187575)