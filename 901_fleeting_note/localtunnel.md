---  
alias:  
creation_date: 2024-06-10 19:06  
tags: 
related:
- [[source code reading]]
- [[tunnel_idx]]

---  


client side: [GitHub - localtunnel/localtunnel: expose yourself](https://github.com/localtunnel/localtunnel)
server side: [GitHub - localtunnel/server: server for localtunnel.me](https://github.com/localtunnel/server)


client side
- 連到 remove server and keep alive
```ts


// connection to localtunnel server
const remote = net.connect({
  host: remoteHostOrIp,
  port: remotePort,
});

remote.setKeepAlive(true);



```





server side
- use `koa` to setup a server
- use `tldjs` to easiy get subdomain, domain
- use `ClientManager` to 管理 建立通道的 client
	- use map 管理 `this.clients = new Map();`
	- 建立 client
		- 會先建立 agent instance from TunnelAgent
		- 然後再把 agent instance 注入 client and 建立 client instance
	- 其他 client 的 CRUD
- use `TunnelAgent` 管理 tcp 連線, extends http.Agent
	- `this.waitingCreateConn = []` as a queue, 如果沒有 available socket, conn 先放這裡
	- `this.server = net.createServer();`
	- 其他還有 listen 等等
- Client class, extends EventEmitter
	- handle req
	- handle upgrade
		- 這邊感覺是直接用 tcp 建立雙向資料流，沒有依賴 websocket
