[Request lifecycle - FAQ | NestJS - A progressive Node.js framework](https://docs.nestjs.com/faq/request-lifecycle)


NestJS 請求生命週期的主要步驟

1.	請求進入（Request Entry）
- 請求由客戶端發送到伺服器，進入 NestJS 應用。
- NestJS 應用通常會經過一個 HTTP 伺服器（如 Express 或 Fastify）來處理請求。


2.	中介軟體（Middleware）
- 任何實作 _NestMiddleware_ 介面的註冊類別都會處理該請求。
- 與 Express 中間件一樣，它們可以更改請求 / 回應物件或設定上下文值。
- 非常適合從標頭讀取 JWT 令牌和在請求上設定使用者資料等任務。
- NestJS 在這裡支援 Express 中間件庫。


3.	守衛（Guards）
- 接下來呼叫實作 CanActivate 介面的類別。
- 這些「警衛」決定是否接受或拒絕請求。
- 範例：檢查中間件設定的 JWT 令牌有效性並相應地允許或拒絕存取。


4.	攔截器（Interceptors）
- NestInterceptor 介面類別在處理程序**回應之前和之後**執行回呼。
- 攔截器也可用於**日誌記錄、性能分析和緩存**。
- 攔截器主要用於轉換和處理請求/回應。攔截器可以修改進入的請求數據，或包裝/格式化響應數據。
- 請求繼續傳遞給控制器之前，攔截器可以對請求進行預處理，並在請求結束後對回應進行後處理。


5.	管道（Pipes）
- 在到達處理程序之前，實作 PipeTransform 的類別會處理參數。
- ValidationPipe 和 ParseIntPipe 等內建管道可確保正確的格式化和驗證。


6.	控制器（Controller）
- 請求通過以上的處理後，進入控制器層。控制器負責處理路由請求並調用相應的服務邏輯。
- 控制器中的處理方法通常會將請求轉發到一個或多個服務（Service）來執行具體的業務邏輯。


7.	服務（Services）
- 服務（通常使用 @Injectable 註解）執行核心業務邏輯，處理請求數據，並返回處理結果。
- 在控制器中可以注入和調用服務來完成業務操作。


8.	攔截器（Interceptors，後處理）
- 控制器返回數據後，攔截器可以再次運行，進行響應數據的後處理。
- 可以在這裡進行日誌記錄、格式化回應數據或處理錯誤。


9.	例外過濾器（Exception Filters）
- 若在請求處理過程中發生異常，會由例外過濾器捕獲。例外過濾器可以捕獲並處理異常，提供自定義的錯誤響應。
- NestJS 內建了基本的異常處理，並允許開發者自定義例外過濾器來處理不同類型的錯誤。


10.	回傳響應（Response）
- 經過以上所有處理後，最終的響應數據會返回給客戶端。
- NestJS 會將處理完成的數據回傳給 HTTP 伺服器，再由伺服器回傳給客戶端。



請求生命週期流程圖

1. Incoming request
2. Middleware
    - 2.1. Globally bound middleware
    - 2.2. Module bound middleware
3. Guards
    - 3.1 Global guards
    - 3.2 Controller guards
    - 3.3 Route guards
4. Interceptors (pre-controller)
    - 4.1 Global interceptors
    - 4.2 Controller interceptors
    - 4.3 Route interceptors
5. Pipes
    - 5.1 Global pipes
    - 5.2 Controller pipes
    - 5.3 Route pipes
    - 5.4 Route parameter pipes
6. Controller (method handler)
7. Service (if exists)
8. Interceptors (post-request)
    - 8.1 Route interceptor
    - 8.2 Controller interceptor
    - 8.3 Global interceptor
9. Exception filters
    - 9.1 route
    - 9.2 controller
    - 9.3 global
10. Server response


![[IMG-nest.js req life cycle-20250807203854091.png]]


請求生命週期中的擴展點
1.	中介軟體（Middleware）：可用於身份驗證、日誌、CORS 等。
2.	守衛（Guards）：用於權限驗證和角色控制。
3.	攔截器（Interceptors）：可用於緩存、日誌、格式化回應、性能測量等。
4.	管道（Pipes）：可用於數據驗證和轉換，確保請求數據正確。
5.	例外過濾器（Exception Filters）：自定義的錯誤處理。

總結
NestJS 的請求生命週期由一系列的中介組件組成，這些組件提供了強大的擴展性，讓開發者可以在不同階段自定義請求處理。
這種設計使得 NestJS 成為一個高可擴展且適合大規模應用的框架。



ref:
- [nestjs study](https://www.learnnote.site/nestjs)