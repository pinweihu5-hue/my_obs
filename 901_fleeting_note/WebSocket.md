
- [How Web Sockets work | Deep Dive - YouTube](https://www.youtube.com/watch?v=G0_e02DdH7I)
- [[WebSocket vs MQTT]]

# 建立連線
![[IMG-WebSocket-20250811203807497.png]]
ref: [淺談 WebSocket 協定：實作一個簡單的即時聊天室吧！ | Medium](https://heidi-coding.medium.com/%E6%B7%BA%E8%AB%87-websocket-%E5%8D%94%E5%AE%9A-%E5%AF%A6%E4%BD%9C%E4%B8%80%E5%80%8B%E7%B0%A1%E5%96%AE%E7%9A%84%E5%8D%B3%E6%99%82%E8%81%8A%E5%A4%A9%E5%AE%A4%E5%90%A7-9611a29cc141)





## WebSocket vs http

WebSocket  一開始 透過 req-res 後，兩方就可以建立長連線 (vs. http 的短連線)
接著兩邊就可以雙向溝通 via 長連線建立起來的通道

好處？
1. 雙向
2. 快，低延遲

when to use? 需要即時雙向主動通訊且即時可以讓訊息互相溝通的系統
- 串流 streaming
- 聊天系統
- 多人遊戲等等會用
	- 你記得 game server 因為人太多登不進去嗎？ server 不夠多 XD
- 等等..



### 如果websocket那麼好的話, why not just use websocket, and give up http?
- 一個 server 可以建立的長連線的數量是有限的
    - 因此你要有更多的 server
    - 機制更複雜，code 也比較複雜，也要使用套件
        - PS: http 的功能, client and server 基本上都是內建, webSocket or MQTT are not!
        - 因此，如果你沒有這個即時、雙向的需求，用 http make things way simple!



## how you impl online chat?
-  how you impl online chat?
	- say you only use HTTP and use long polling to check if other user send msg 
- 這篇文章談
	- long pooling
	- SSE 
	- websocket
	- STOMP (like pubsub protocol)
- figma -> see   [ref](https://www.figma.com/board/4MD4T4IegKTKPRN3RdJGE5/my?node-id=28-475&t=jlJO5hzUGtbdvr9Q-0)
ref: - [《菜农升职记》之 Websocket - 知乎](https://zhuanlan.zhihu.com/p/469864345)




## WebSocket 與 STOMP 比較
- stomp 位於 http(s) 層級，websocket 則是以 tcp 為基礎，後者屬於較低層級的協定。
- websocket 類似於 TCP socket，並作為升級版的 http 連線運作。
- websocoket 在雙方之間交換可變長度的訊框 (frames)。
- stomp 可以建立在 websocket 之上：
	- 將 `STOMP` 作為 `WebSocket` 之上的 (子) 協定。
	- 在 websocket 之上提供更高級別的抽象化，一些 stomp 類型會映射到 websocket 類型，例如：連線、訂閱、取消訂閱、發送（發送到伺服器的訊息）、訊息（從伺服器發送的訊息）、BEGIN、COMMIT、ROLLBACK（交易管理）。
- STOMP 也可以在沒有 websocket 的情況下使用，例如透過 Telnet 連線或訊息代理服務。
- **STOMP** 協定定義了一種機制，讓客戶端和伺服器可以協商在 **WebSocket** 之上使用的子協定（即更高級別的訊息傳遞協定），以定義以下內容：
	- 雙方可以發送哪些類型的訊息，
	- 訊息的格式為何，
	- 每條訊息的內容等等。
- **WebSocket** 協定定義了兩種訊息類型 **（文字和二進制）**，但未定義其內容。
- **WebSocket** 確實暗示了一種訊息傳遞架構，但並未強制使用任何特定的訊息傳遞協定。它是在 TCP 之上非常薄的一層，將位元組流轉換為訊息流（文字或二進制），僅此而已。應用程式需要自行解釋訊息的含義。
	- 與 HTTP 這種應用層協定不同，在 WebSocket 協定中，傳入訊息中沒有足夠的資訊讓框架或容器知道如何路由或處理它。因此，WebSocket 可以說是太低級了，除了非常簡單的應用程式之外，其他應用程式都不太適合使用。雖然可以使用，但很可能會導致需要在上面建立一個框架。這就好比現今大多數 Web 應用程式都是使用 Web 框架編寫，而不是僅使用 Servlet API。
	- 基於這個原因，WebSocket RFC 定義了子協定的使用。在建立連線時，客戶端和伺服器可以使用 `Sec-WebSocket-Protocol` 標頭來協商子協定，即要使用的更高級別的應用層協定。並非強制要求使用子協定，但即使不使用，應用程式仍然需要選擇客戶端和伺服器都能理解的訊息格式。該格式可以是自訂的、特定於框架的，也可以是標準的訊息傳遞協定。
	- 相對低層級的協定。
	- 沒有定義訊息內容。
	- 需要自行透過子協定定義，例如使用 `Sec-WebSocket-Protocol` 標頭讓雙方先確定。
- **STOMP** - 一種簡單的訊息傳遞協定，最初是為腳本語言設計的，其訊框設計靈感來自 HTTP。STOMP 得到廣泛支援，並且非常適合在 WebSocket 和 Web 上使用。

- 問答
	- 直接使用 stomp 協定與訊息代理（如 RabbitMQ 或 Kafka）通訊是否有任何效能優勢？
		- 您不能「僅僅」使用 STOMP 與伺服器或訊息代理通訊。您必須使用傳輸層來發送這些 STOMP 訊息。
		- 事實上，當使用 [sock.js](https://github.com/sockjs/sockjs-client) 時，您可以使用許多傳輸層作為後備方案，並且仍然使用 STOMP。
	- 除了處理客戶端連線到伺服器/訊息代理所需的建立連線過程之外，使用 STOMP 作為 WebSocket 的子協定還有什麼好處嗎？
		- STOMP 並未處理 websocket 的建立連線過程，事實上，它根本不知道 websocket 的建立連線過程。您可以在 HTTP 之上透明地使用 STOMP，並且從 STOMP 的角度來看，這沒有任何區別。

**簡要說明:**
WebSocket 是一個相對低層級的協定，它建立在 TCP 之上，主要負責建立和維護連線，以及傳輸資料。它本身並沒有定義訊息的內容和格式，需要應用程式自行定義。
STOMP 是一個更高級別的訊息傳遞協定，它可以建立在 WebSocket 之上，也可以使用其他傳輸層。STOMP 定義了訊息的類型、格式和內容，讓應用程式可以更方便地進行訊息傳遞。
總之來說，WebSocket 就像是一條管道，而 STOMP 則是在管道中傳輸的資料格式。您可以根據自己的需求選擇使用 WebSocket 或 STOMP。

- ref:
- [What is the difference between WebSocket and STOMP protocols? - Stack Overflow](https://stackoverflow.com/questions/40988030/what-is-the-difference-between-websocket-and-stomp-protocols)



- [ ] more about stomp
	- [jmesnil/stomp-websocket: Stomp client for Web browsers and node.js apps](https://github.com/jmesnil/stomp-websocket?tab=readme-ov-file)




---

## 應用

- [Traffic 101: Packets Mostly Flow - Slack Engineering](https://slack.engineering/traffic-101-packets-mostly-flow/)






## JS node

- 安裝 ws 函式庫: 在終端機執行 `npm install ws`。
- 啟動伺服器: 在終端機執行 node server.js (假設您將伺服器端程式碼儲存為 server.js)。
- 開啟客戶端: 在瀏覽器中開啟 index.html 檔案。
```ts title:clent-side fold


<!DOCTYPE html>
<html>
<head>
  <title>簡易聊天室</title>
</head>
<body>
  <h1>聊天室</h1>
  <div id="chatbox"></div> <input type="text" id="messageInput" placeholder="請輸入訊息...">
  <button id="sendButton">發送</button>

  <script>
    const websocket = new WebSocket('ws://localhost:8080'); // 建立 WebSocket 連線，連線至伺服器

    // 取得 DOM 元素
    const chatbox = document.getElementById('chatbox');
    const messageInput = document.getElementById('messageInput');
    const sendButton = document.getElementById('sendButton');

    // 監聽 'open' 事件，當連線建立成功時觸發
    websocket.onopen = () => {
      console.log('WebSocket 連線已建立');
    };

    // 監聽 'message' 事件，當收到伺服器訊息時觸發
    websocket.onmessage = (event) => {
      const message = document.createElement('p'); // 建立新的段落元素
      message.textContent = event.data; // 設定段落文字為接收到的訊息
      chatbox.appendChild(message); // 將訊息新增至聊天室
    };

    // 監聽發送按鈕點擊事件
    sendButton.onclick = () => {
      const message = messageInput.value; // 取得輸入框的訊息
      websocket.send(message); // 發送訊息至伺服器
      messageInput.value = ''; // 清空輸入框
    };
  </script>
</body>
</html>


```

```ts title:server-side fold

const WebSocket = require('ws'); // 載入 WebSocket 函式庫

const wss = new WebSocket.Server({ port: 8080 }); // 建立 WebSocket 伺服器，監聽 port 8080

// 使用 Set 來儲存所有已連線的客戶端
const clients = new Set();

// 監聽 'connection' 事件，當有新的客戶端連線時觸發
wss.on('connection', (ws) => {
  console.log('客戶端已連線');
  clients.add(ws); // 將新的客戶端加入 clients 集合

  // 監聽來自客戶端的 'message' 事件，當收到訊息時觸發
  ws.on('message', (message) => {
    console.log(`收到訊息: ${message}`);

    // 將訊息廣播給所有已連線的客戶端
    clients.forEach((client) => {
      // 檢查客戶端連線狀態是否為開啟
      if (client.readyState === WebSocket.OPEN) {
        client.send(message); // 發送訊息
      }
    });
  });

  // 監聽 'close' 事件，當客戶端斷線時觸發
  ws.on('close', () => {
    console.log('客戶端已斷線');
    clients.delete(ws); // 將斷線的客戶端從 clients 集合中移除
  });
});

console.log('WebSocket 伺服器已啟動，監聽 port 8080');

```


---




## related link
- [《菜农升职记》之 Websocket - 知乎](https://zhuanlan.zhihu.com/p/469864345)
- [《 Socket.IO》 解决 WebSocket 通信！ - 知乎](https://zhuanlan.zhihu.com/p/480351690)
- [(688) How Web Sockets work | Deep Dive - YouTube](https://www.youtube.com/watch?v=G0_e02DdH7I)
