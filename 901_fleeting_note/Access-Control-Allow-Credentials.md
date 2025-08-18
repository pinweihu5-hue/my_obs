- response header
- 用於控制在使用 [[CORS]]（Cross-Origin Resource Sharing，跨來源資源共享） 時，是否允許瀏覽器發送或接收 cookies、authorization headers 或 TLS client certificates 等憑證
- 假設你正在開發一個電子商務網站，該網站的前端 `www.myshop.com` 和後端 API `api.myshop.com `分別部署在不同的domain下。
- 當用戶登錄網站時，後端會生成一個包含用戶身份信息的 session cookie，並將其發送給前端。
- 當用戶在前端進行操作（如添加商品到購物車）時，前端需要將 session cookie 發送回後端，以便後端識別用戶身份並進行相應的處理。**由於前端和後端屬於不同的域名，因此這種情況下需要進行跨域請求**。
- 為了允許前端在跨域請求中發送 session cookie，後端服務器需要將 Access-Control-Allow-Credentials 標頭設置為 true。
- 同時，後端還需要將 `Access-Control-Allow-Origin` 設置為前端的域名 `www.myshop.com`，以明確允許該域名的跨域請求。
- 以下是後端 API 的響應標頭示例：
    
    ```
    
    Access-Control-Allow-Origin: https://www.myshop.com
    Access-Control-Allow-Credentials: true
    
    ```
    
- 這樣設置後，前端在發送跨域請求時，瀏覽器會自動將 session cookie 包含在請求中發送給後端。
- 後端收到請求後，可以根據 session cookie 中的用戶身份信息進行相應的處理，如獲取用戶的購物車數據等。
- 通過將 Access-Control-Allow-Credentials 設置為 true，並正確設置 Access-Control-Allow-Origin，可以實現跨域請求中的用戶身份驗證和狀態保持，提供更好的用戶體驗。
- 需要注意的是，由於開啟了 Access-Control-Allow-Credentials，後端必須明確指定允許的前端域名，建議不要使用萬用字元 `*`，以確保安全性。
- 總之，當跨域請求需要發送 cookies 等憑證，並且需要根據用戶身份進行處理時，我們就需要將 Access-Control-Allow-Credentials 設置為 true，並正確設置 Access-Control-Allow-Origin。這在需要用戶登錄、身份驗證和狀態保持的 Web 應用程序中非常常見。