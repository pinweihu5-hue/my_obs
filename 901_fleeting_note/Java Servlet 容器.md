

**Servlet 容器**（也稱為 **Servlet 引擎**）是 Java EE（現在稱為 Jakarta EE）的一部分，負責管理和執行 Java Servlet 的環境。
Servlet 是一種用於擴展伺服器功能的 Java class，通常用於處理 HTTP 請求和生成 HTTP 響應。
Servlet 容器提供了執行這些功能 所需的基礎設施。

### Servlet 容器的主要功能
1. **請求處理**：Servlet 容器接收來自客戶端（通常是瀏覽器）的 HTTP 請求，並將這些請求轉發給相應的 Servlet 進行處理。
2. **生命週期管理**：
	- Servlet 容器負責管理 Servlet 的生命週期，包括初始化、請求處理和銷毀。
	- 具體來說，容器會調用 Servlet 的 `init()` 方法來初始化，然後調用 `service()` 方法來處理請求，最後在不再需要時調用 `destroy()` 方法來銷毀 Servlet。
3. **多線程支持**：Servlet 容器通常會為每個請求創建一個新的線程，這樣可以同時處理多個請求，提高應用的並發性。
4. **會話管理**：Servlet 容器提供會話管理功能，允許在多個請求之間保持用戶的狀態。這通常是通過使用 Cookie 或 URL 重寫來實現的。
5. **安全性**：Servlet 容器提供安全性功能，例如用戶身份驗證和授權，確保只有授權用戶才能訪問特定的資源。
6. **資源管理**：Servlet 容器可以管理數據庫連接、JNDI 資源等，並提供給 Servlet 使用。
    

### 常見的 Servlet 容器
- **Apache Tomcat**：最流行的開源 Servlet 容器，支持 Servlet 和 JSP（JavaServer Pages）。
- **Jetty**：輕量級的 Servlet 容器，適合嵌入式應用和微服務架構。
- **GlassFish**：一個完整的 Java EE 應用伺服器，支持 Servlet、JSP 和其他 Java EE 技術。
- **WildFly**（以前稱為 JBoss）：一個開源的 Java EE 應用伺服器，支持多種 Java EE 技術。


## 請問 Java Servlet 跟我目前經常使用的 sping boot 的關係是？

好的，這是一個很好的問題！讓我們來探討 Java Servlet 和 Spring Boot 之間的關係。

**Java Servlet：Web 開發的基礎**

正如我們之前討論的，Java Servlet 是 Java Web 開發的基礎。它提供了一個底層的 API，用於處理 HTTP 請求和產生 HTTP 回應。Servlet 本身是一個 Java 類別，需要部署在 Web 容器 (例如 Tomcat、Jetty) 中才能運行。

**Spring Boot：簡化 Web 開發的框架**

Spring Boot 是一個基於 Spring Framework 的框架，它旨在簡化 Spring 應用程式的開發、部署和管理。Spring Boot 提供了許多便捷的功能，例如：

*   **自動配置：** Spring Boot 可以根據專案的依賴自動配置應用程式，減少了手動配置的工作量。
*   **內嵌 Web 容器：** Spring Boot 可以內嵌 Web 容器 (例如 Tomcat、Jetty)，使得應用程式可以直接運行，無需額外部署到 Web 容器中。
*   **簡化依賴管理：** Spring Boot 提供了許多 starter 依賴，可以方便地引入常用的函式庫。
*   **快速開發：** Spring Boot 提供了許多便捷的功能，可以加速應用程式的開發過程。

**Servlet 和 Spring Boot 的關係：**

Spring Boot 實際上是建立在 Java Servlet 之上的。也就是說，Spring Boot 應用程式仍然使用 Servlet 來處理 HTTP 請求，只是 Spring Boot 將 Servlet 的使用細節隱藏起來，讓開發者可以更專注於業務邏輯的開發。

**Spring Boot 如何使用 Servlet：**

1.  **內嵌 Web 容器：** Spring Boot 應用程式通常會內嵌一個 Web 容器 (例如 Tomcat)。
2.  **DispatcherServlet：** Spring Boot 會自動註冊一個名為 `DispatcherServlet` 的 Servlet。`DispatcherServlet` 是 Spring MVC 的核心 Servlet，它負責接收所有 HTTP 請求，並將請求分發給對應的 Controller 方法。
3.  **Controller：** 在 Spring Boot 應用程式中，你可以使用 `@Controller` 或 `@RestController` 註解來定義 Controller 類別。Controller 類別中的方法會使用 `@GetMapping`、`@PostMapping` 等註解來映射 HTTP 請求到對應的方法。
4.  **請求處理：** 當 Spring Boot 應用程式接收到 HTTP 請求時，`DispatcherServlet` 會接收請求，並根據請求的 URL 將請求分發給對應的 Controller 方法。Controller 方法會處理請求，並返回回應。
5.  **回應產生：** Spring Boot 會將 Controller 方法的回傳值轉換為 HTTP 回應，並將回應發送回客戶端。

**Spring Boot 如何簡化 Servlet 開發：**

*   **無需手動部署 Servlet：** Spring Boot 內嵌了 Web 容器，你無需手動部署 Servlet。
*   **無需手動配置 `web.xml`：** Spring Boot 使用自動配置，你無需手動配置 `web.xml` 檔案。
*   **使用 Controller 簡化請求處理：** Spring Boot 使用 Controller 來處理請求，而不是直接使用 Servlet。
*   **使用註解簡化開發：** Spring Boot 使用註解來簡化開發，例如 `@GetMapping`、`@PostMapping`、`@Autowired` 等。

**總結：**

*   Java Servlet 是 Java Web 開發的基礎，它提供了一個底層的 API 來處理 HTTP 請求和回應。
*   Spring Boot 是一個基於 Spring Framework 的框架，它簡化了 Spring 應用程式的開發、部署和管理。
*   Spring Boot 仍然使用 Servlet 來處理 HTTP 請求，只是它將 Servlet 的使用細節隱藏起來，讓開發者可以更專注於業務邏輯的開發。
*   Spring Boot 使用 `DispatcherServlet` 作為核心 Servlet，並使用 Controller 來處理請求。

**簡單來說：**

你可以把 Java Servlet 看作是 Web 開發的「磚塊」，而 Spring Boot 則是一個使用這些磚塊來建造房屋的「工具」。Spring Boot 讓你無需關心磚塊的細節，可以更快速地建造出功能完善的 Web 應用程式。

希望這個解釋對你有幫助！如果你還有其他關於 Java Servlet 或 Spring Boot 的問題，歡迎繼續提問。


===

- [为什么要有 Servlet ，什么是 Servlet 容器，什么是 Web 容器？ - yes 的练级攻略 - 博客园](https://www.cnblogs.com/yescode/p/14099868.html)
- [何謂容器](https://openhome.cc/Gossip/ServletJSP/Container.html)
- [谈谈你对 Spring IoC 理解？-codewd](https://codewd.com/question/mdrspfht)