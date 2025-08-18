
Webhook 機制是一種用於實現應用程式之間即時通信的技術。它允許一個應用程式在特定事件發生時，主動向另一個應用程式發送 HTTP 請求


主要是拿來取代 polling
不要一直去詢問 event 是否發生，一些確認是否好了
(app 問第三方支付機構：payment is done？  app 問 SMS 第三方系統: is SMS is sent 成功?)

而是讓另一個系統去呼叫我們定義好的 API 端點讓我們可以知道


下面的例子就是 warehouse 不用一直去用 CRM 好了沒?
而是 CRM 好了，可以直接呼叫 warehouse 已經定義好的一個 endpoint
![[IMG-webhook-20250810200245149.png|823]]


### Webhook 的工作原理
- 要先去註冊 Webhook：
	- 使用者或開發者在應用程式 A 中註冊一個 webhook URL，這是應用程式 B 將發送請求的地址。
	- 註冊時，通常需要指定要監聽的事件類型，例如用戶註冊、訂單創建等。
- 事件觸發：
	- 當應用程式 A 中發生特定事件時（例如用戶提交表單），它會自動向註冊的 webhook URL 發送一個 HTTP POST 請求。
	- 請求中通常包含事件的詳細信息，這些信息可以是 JSON 格式的數據。
- 處理請求：
	- 應用程式 B 接收到請求後，可以根據請求中的數據執行相應的操作，例如更新數據庫、發送通知等。

### 優點
- **即時性**：Webhook 允許應用程式之間即時通信，無需輪詢（polling），從而減少延遲。
- **資源效率**：由於不需要定期查詢事件，Webhook 可以減少不必要的網絡流量和伺服器負擔。
- **靈活性**：開發者可以根據需要選擇要監聽的事件，並在事件發生時自動執行相應的操作。


### 適用場景
- 支付通知：當用戶完成支付時，支付平台可以通過 webhook 通知商家系統。
- 持續集成/持續部署（CI/CD）：當代碼推送到版本控制系統時，可以觸發自動構建和部署流程。
- 即時消息推送：社交媒體或聊天應用可以使用 webhook 來即時推送消息或通知。
- more my experience example:
	- like every8Dcallback
	- like 第三方金流的確認
	- 或是 Oauth 中 redirect 後的 callback
	- CI/CD, 你在 Gitlab上設定一個 cb endpoint, 當你 commit 後，Gitlab 會 呼叫這個 endpoint → 觸發 Jenkin  or CI/CD 服務開始跑