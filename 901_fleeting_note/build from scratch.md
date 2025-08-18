
## dns go
- [https://github.com/rezanatha/codecrafters-dns-server-go](https://github.com/rezanatha/codecrafters-dns-server-go)
    JS: code /Users/re4388/project/personal/nodejs/codecrafters-dns-server-javascript
    - ref to iohansson
    - tip
        For local testing, you can use something like:
        ```
        echo "Your Message" | nc -u 127.0.0.1 2053
        ```
        or
        ```
        dig @127.0.0.1 -p 2053 codecrafters.io
        ```

## build Git using JS
- `code /Users/re4388/project/personal/nodejs/codecrafters-git-javascript`
- [Write yourself a Git!](https://wyag.thb.lt/#objects)
- [Git - Git Objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)
- [.git 目錄中有什麼？ --- What is in that .git directory?](https://blog.meain.io/2023/what-is-in-dot-git/)


## bulid a redis using JS
- [課程 |程式碼工匠 --- Courses | CodeCrafters](https://app.codecrafters.io/courses/redis/stages/1?repo=d3e5ae54-e5c6-4c85-b971-3e2bfa23bd61) → bulid a redis using JS
    - `code /Users/re4388/project/personal/nodejs/codecrafters-redis-javascript`
        - 正在尋找如何編寫乾淨的 Redis 協定解析器的靈感嗎？開源 Redis 用戶端是一個很好的起點！
            Examples:  例子：
            - [**Redcon**](https://github.com/tidwall/redcon/blob/bc9875b4b00f22ed2575ea7602ca83d3eeef88c8/resp.go) (Go)
                 雷德康（去）
            - [**Carmine**](https://github.com/ptaoussanis/carmine/blob/c9ed46c6a53b0c525b3275ae395dd62eef73f6d4/src/taoensso/carmine/protocol.clj#L125) (Clojure)
                 胭脂紅 (Clojure)
            - [**Redix**](https://github.com/whatyouhide/redix/blob/81a2e85275c0139ceaa4547ca9e3c0d46db1742f/lib/redix/protocol.ex) (Elixir)
                 Redix（長生不老藥）
            - [**Jedis**](https://github.com/redis/jedis/blob/11a4513ff9581a40530a84e6c8ee019c4a3f9e38/src/main/java/redis/clients/jedis/Protocol.java) (Java)
                 絕地武士（Java）
        - [Redis serialization protocol specification | Redis](https://redis.io/docs/reference/protocol-spec/)
        - Julia Evans 有一個關於編寫 TCP 伺服器的 YouTube 視頻，我發現它很有用。 [**YouTube video on writing a TCP server**](https://www.youtube.com/watch?v=1HF-UAGcuvs)
        - [Redis 協議非常棒。 |非理性繁榮 --- The Redis Protocol is pretty great. | Irrational Exuberance](https://lethain.com/redis-protocol/)
            ```Bash
            
            # redis
            > set a hello
            
            *3\r\n        # 客戶端以 *3 格式的一行開始每個命令，它聲明該命令將具有三個參數
            $3\r\n        # 每個參數都是兩行，第一行以 $ 開頭，後面跟著一個整數，它宣告下一行中的位元組數，不包括終止的 \r\n 。
            SET\r\n
            $1\r\n
            A\r\n
            $5\r\n
            hello\r\n
            
            
            
            # 可以用 ngrep 來看
            sudo ngrep -d lo0 -t '' 'tcp and port 6379'
            ```
            
        - 需要複習一下 TCP，或只是想測試您的知識？我們的互動指南可能會有所幫助：
            - [**Network protocols**](https://app.codecrafters.io/concepts/network-protocols) [](https://app.codecrafters.io/concepts/network-protocols) [**網路協定**](https://app.codecrafters.io/concepts/network-protocols)
                - Learn about various network protocols like HTTP, TCP, UDP, SMTP etc.
                    
                    了解各種網路協議，如 HTTP、TCP、UDP、SMTP 等。
                    
                - Learn about the TCP/IP model, which categorizes network protocols into 4 "layers"
                    
                    了解 TCP/IP 模型，該模型將網路協定分為 4 個 “層”
                    
            - [**TCP: An overview**](https://app.codecrafters.io/concepts/tcp-overview)[**TCP：概述**](https://app.codecrafters.io/concepts/tcp-overview)
                - Learn about the TCP protocol and its guarantees
                    
                    了解 TCP 協定及其保證
                    
                - Learn about TCP connections and the TCP handshake process
                    
                    了解 TCP 連線和 TCP 握手過程
                    
        
          
        
        - not many but [**https://youtu.be/4IMc3CaMhyY**](https://youtu.be/4IMc3CaMhyY) was fine about tcp
        
          
        
    - code example
        - [redis-clone/README.md at acd91a8b084d9d8acded4d0452ae5ed3524efaa2 · LakshyaMittal3301/redis-clone](https://github.com/LakshyaMittal3301/redis-clone/blob/acd91a8b084d9d8acded4d0452ae5ed3524efaa2/README.md)



## http server

- [https://github.com/LakshyaMittal3301/NodeHTTPServer](https://github.com/LakshyaMittal3301/NodeHTTPServer)
    - `code /Users/re4388/project/personal/nodejs/codecrafters-http-server-javascript`



## build docker using go
- [https://github.com/rezanatha/codecrafters-docker-go](https://github.com/rezanatha/codecrafters-docker-go)
    - **Process isolation 行程隔離**
        - Here's a talk from a Docker employee that goes over how Docker uses cgroups, namespaces and more: [**https://www.youtube.com/watch?v=sK5i-N34im8.**](https://www.youtube.com/watch?v=sK5i-N34im8.)
        - This was a really helpful read for this step => [**https://itnext.io/container-runtime-in-rust-part-0-7af709415cda**](https://itnext.io/container-runtime-in-rust-part-0-7af709415cda)
    - **從 Docker 註冊表取得映像**
        Docker API can be very confusing:Docker API 可能非常令人困惑：
        1. You need to get an auth token, but you don't need a username/passwordSay your image is `busybox/latest`, you would make a GET request to this URL: `https://auth.docker.io/token?service=registry.docker.io&scope=repository:library/busybox:pull`You'll send the token you receive in following API calls as a header: `"Authorization = Bearer <token>"`
            
            您需要取得身份驗證令牌，但不需要使用者名稱 / 密碼
            
            假設您的圖像是 `busybox/latest` ，您將向此 URL 發出 GET 請求： `https://auth.docker.io/token?service=registry.docker.io&scope=repository:library/busybox:pull`
            
            您將在以下 API 呼叫中將收到的令牌作為標頭發送： `"Authorization = Bearer <token>"`
            
        2. Next step is to get the manifest. The URL to get the manifest will be: `https://registry.hub.docker.com/v2/library/busybox/manifests/latest`I'd recommend using `"Accept = application/vnd.docker.distribution.manifest.v2+json"` header to get v2 of this response, which tells the media type for each layer
            
            下一步是獲取清單。取得清單的 URL 為： `https://registry.hub.docker.com/v2/library/busybox/manifests/latest`
            
            我建議使用 `"Accept = application/vnd.docker.distribution.manifest.v2+json"` 標頭來獲取此回應的 v2，它告訴每個層的媒體類型
            
        3. The last step is to download the layer and extract the files. The URL for the API call will be: `https://registry.hub.docker.com/v2/library/busybox/blobs/<sha256:xxxx>`
            
            最後一步是下載圖層並提取檔案。 API 呼叫的 URL 為： `https://registry.hub.docker.com/v2/library/busybox/blobs/<sha256:xxxx>`


## build sqlite
- [https://github.com/LakshyaMittal3301/SQLiteJS](https://github.com/LakshyaMittal3301/SQLiteJS)
    - [https://app.codecrafters.io/courses/sqlite/stages/8/code-examples](https://app.codecrafters.io/courses/sqlite/stages/8/code-examples) 參考 lakshya…
    1. 很多 byte operation, 用來比較，用來拿 byte, 都有抽象函數出來操作
        1. 用一個 BufferReader 來處理這些 byte 操作
        2. 然後 BTreePage extends 來自於 BufferReader 就可以有哪些操作
        3. page → node
        4. cell → rows
        5. page contains rows/cell/record
        6. page also contains header, like meta data, like size of the page
    2. 有一個簡單的 parser 去確認 from, where and =
    3. use dfs 去 get value
    4. use idx
        1. 先確認是有idx table
        2. 有的話，透過 idx 先拿到 rowId vis dfs in idx page tree
        3. 然後再用這些 rowId 去拿到 values via dfs at 要找的 ree


## redis clone
- [https://github.com/LakshyaMittal3301/redis-clone](https://github.com/LakshyaMittal3301/redis-clone)
    - client ↔ server via tcp socket
        ```JavaScript
        // client
        
        // Create a new socket client
        const client = new net.Socket();
        client.connect(PORT, HOST, () => {
          console.log(`Connected to server on ${HOST}:${PORT}`);
        
          // Start taking user input
          function sendDataToServer() {
            rl.question("Enter command ", (input) => {
              input = input.toLowerCase();
              // Check if the user wants to exit
              if (input === "exit") {
                // Close the connection to the server
                client.end();
                // Close the readline interface
                rl.close();
              } else {
                // Parse the user input into data to send to the server
                let dataToSend = parseInput(input);
        
                // Send the data to the server
                client.write(dataToSend);
                // 送過去後，可以一直送，繼續問
                // Prompt for more user input
                sendDataToServer();
              }
            });
          }
        
          sendDataToServer();
        });
        
        
        ////////////////////////////
        
        
        // server
        
        function initializeServer(port = PORT, isReplica = false, isReplicaOf = -1) {
          config["isReplica"] = isReplica;
          config["isReplicaOf"] = isReplicaOf;
          const server = net.createServer((socket) => {
            socket.on("data", (data) => {
              data = data.toString();
              executeCommand(data, socket);
            });
          });
        
          server.listen(port, LOCALHOST, () => {
            console.log(`Server Listening on ${LOCALHOST}:${port}`);
          });
        }
        ```
        
    - 這一段體現的 redis 的 protocol, client 要送那個 protocol 傳給 server
        ```JavaScript
        function parseSimpleString(string) {
          return `+${string}\r\n`;
        }
        
        function parseNullBulkString(string) {
          return "$-1\r\n";
        }
        
        function parseBulkString(string) {
          return `$${string.length}\r\n${string}\r\n`;
        }
        
        function parseArray(array) {
          ret = `*${array.length}\r\n`;
        
          for (const element of array) {
            ret += parseResponse(element);
          }
        
          return ret;
        }
        ```
        
          
        
    - 這邊實作了不同 command
        ```JavaScript
        function executeCommand(data, socket) {
          let { command, args } = inputParser(data);
          let res;
          console.log(`Executing command: ${command}`);
          switch (command) {
            case "ping":
              res = ping();
              break;
            case "echo":
              res = echo(args[0]);
              break;
            case "set":
              if (args.length == 2) {
                res = set(dataStore, args[0], args[1]);
              } else {
                res = set(dataStore, args[0], args[1], args[2], args[3]);
              }
              break;
            case "get":
              res = get(dataStore, args[0]);
              break;
            case "config":
              res = getConfig(config, args[1]);
              break;
            case "keys":
              res = keys(dataStore, args[0]);
              break;
            case "info":
              res = infoCommand(config);
              break;
          }
        ```
        
    - 實作 set 
        ```JavaScript
        function set(dataStore, key, value, arg, argVal){
            let expiryTime = null;
            if(arg == "px"){
                let timeDelay = 1*argVal;
                expiryTime = new Date(Date.now() + timeDelay);
            }
            
            dataStore.set(key, {value, expiryTime})
            
            return createResponseObject("OK", dataType.simpleString);
        }
        ```
        
    - 這邊實作 server 傳給 client 的protocol, 類似 client → server
        ```JavaScript
        // Function to parse a simple string
        function parseSimpleString(string) {
          return `+${string}\r\n`;
        }
        
        // Function to parse a null bulk string
        function parseNullBulkString(string) {
          return "$-1\r\n";
        }
        
        // Function to parse a bulk string
        function parseBulkString(string) {
          return `$${string.length}\r\n${string}\r\n`;
        }
        
        // Function to parse an array
        function parseArray(array) {
          ret = `*${array.length}\r\n`;
          for (const element of array) {
            ret += parseResponse(element);
          }
          return ret;
        }
        ```
        
    - 有一個從 buf 讀幾個 char string 的 util
        ```JavaScript
          /**
           * Converts a buffer slice to a string.
           *
           * // JavaScript
           * let dbParser = new DBParser();
           * dbParser.buffer = new Uint8Array([72, 101, 108, 108, 111, 32, 87, 111, 114, 108, 100]); // ASCII values for "Hello World"
           * dbParser.counter = 0;
           *
           * let result = dbParser.getString(5); // Get the first 5 characters
           * console.log(result); // Outputs: "Hello"
           *
           * @param {number} len - The length of the buffer slice.
           * @returns {string} The converted string.
           */
          getString(len) {
            return String.fromCharCode(
              ...this.buffer.subarray(this.offset, this.offset + len)
            );
          }
        ```
        
    - DBParser…可能就…需要背景知識才看的懂了 XD



## node http server

- [https://github.com/LakshyaMittal3301/NodeHTTPServer](https://github.com/LakshyaMittal3301/NodeHTTPServer)
    - 實作200回傳
        ```JavaScript
          setResponseToContent200(content, contentType) {
            let response = `${this.httpVersion} 200 OK\r\n`;
            response += `Content-Type: ${contentType}\r\n`;
            response += `Content-Length: ${content.length}\r\n`;
            response += `\r\n`;
            response += `${content}\r\n`;
            this.response = response;
          }
        ```
        
    - 實作不同 method 的處理
        ```JavaScript
          getResponse() {
            switch (this.httpMethod) {
              case "GET":
                this.handleGet();
                break;
              case "POST":
                this.handlePost();
                break;
            }
            return this.response;
          }
        ```
        
    - parse header logic
        ```JavaScript
          readHeaderLine(line) {
            let headerKey = "";
            let headerVal = "";
        
            let offset = 0;
            while (line[offset] !== ":") {
              headerKey += line[offset++];
            }
        
            offset += 2; // Skip the ": " characters
            while (offset < line.length) {
              headerVal += line[offset++];
            }
        
            console.log(headerKey, headerVal);
        
            return { headerKey, headerVal };
          }
        ```
        
    - built upon TCP
        ```JavaScript
        // Create a new TCP server
        const server = net.createServer((socket) => {
          socket.on("data", (data) => {
            console.log(`Received data: ${data}`);
            // Create a new HTTPRequest object with the received data and directory path
            let httpObject = new HTTPRequest(data.toString(), directoryPath);
            let response = httpObject.getResponse();
            console.log(`Response to client: ${response}`);
            // Write the response to the client socket
            socket.write(response);
        
            // End the socket connection
            socket.end();
          });
        ```
        
    - 體現了 http protocol 的規則, use \r\n, 依序放什麼 等等
        
        ```Bash
            let lines = req.split("\r\n");
            let startLine = lines[0];
        
            let arr = startLine.split(" ");
            this.httpMethod = arr[0];
            this.path = arr[1];
            this.httpVersion = arr[2];
        ```