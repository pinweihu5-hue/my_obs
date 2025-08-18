---  
alias:  
creation_date: 2024-05-23 08:26  
tags: 
---  

related:
[[source code reading]]


![[IMG-edgetunnel-20241003104933927.png]]



this src is a very very good example on HOW TO use nodeJS stream
基本nodeJS stream 教學可以參考 -> /Users/re4388/project/personal/Node.js-Design-Patterns-Third-Edition





https://github.com/zizifn/edgetunnel/tree/main


放在 cf edge fn

如果不是 ws, 就是 http
- 一個 healthy check at `/
- 另一個是拿  `vlessConfig` 路徑是 `/${userID}`


如果是 ws (可以透過 有沒有帶上 Upgrade header 來判斷)
- 呼叫 vlessOverWSHandler
	- 實例 WebSocketPair
	- 建立 readableWebSocketStream


websocket -> remote socket
	- 利用 readableWebSocketStream 來 pipe 資料 into 遠端socket
		- 需要 實例 new WritableStream
		- 解析 vless header
		- 如果是 udp
			- 建立 udp 連線 via handleUDPOutBound
			- 如果不是，繼續下去建立 vcp 連線 via handleTCPOutBound
			- 這時候透過上面解析，已經有 hostname and port 了


也要處理  remote socket 回傳 websocket
- 語法類似 
```ts

await remoteSocket.readable
    .pipeTo(
      new WritableStream ...

...

if (vlessHeader) {
	webSocket.send(await new Blob([vlessHeader, chunk]).arrayBuffer());
	vlessHeader = null;
}

```


