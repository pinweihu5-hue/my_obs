


reverse tunnel -> 目前最簡單的還是 [ngrok](https://dashboard.ngrok.com/get-started/setup/macos) and vscode 的 tunnel



[anderspitman/awesome-tunneling: List of ngrok/Cloudflare Tunnel alternatives and other tunneling software and services. Focus on self-hosting.](https://github.com/anderspitman/awesome-tunneling)



- [[tunnel 的玩法]]
- [[what_is_tunnel]]
- [[edgetunnel]]
- [[How NAT traversal works]]



- [ ] setup my new gcp a/c and try this -> [GitHub - yarrick/iodine: Official git repo for iodine dns tunnel](https://github.com/yarrick/iodine)

# 其他 tunnel 
- https://boringproxy.io/usage/
- [Tinyproxy](https://tinyproxy.github.io/)
- [jpillora/chisel：基於 HTTP 的快速 TCP/UDP 隧道 --- jpillora/chisel: A fast TCP/UDP tunnel over HTTP](https://github.com/jpillora/chisel?tab=readme-ov-file) → 需要 websocket → gcp vm 無法穿透的樣子
    - 相當於 ssh 隧道，只不過它周圍有一些 HTTP/websocket 包裝器
    - TCP over websockets
    - why 使用 TCP over websocket?
        - 您可以將 HTTP 主體用作雙向流。 → websocket 在一些限制性設定中會被阻止。
        - 因為作者嘗試透過直接 run SSH （當前的 TCP 多路復用協定）over http，發現存在問題，因此將 WS 新增到其中
        - 大多數公司 + 學校防火牆都會阻止 https 或讓用戶添加 MITM 證書（嗯，它是透過公司電腦的活動目錄進行管理的）。這些防火牆很少允許 Websocket。
    - [IppSec - Search](https://ippsec.rocks/?#)
    - [Chisel（惡意軟體家族） --- Chisel (Malware Family)](https://malpedia.caad.fkie.fraunhofer.de/details/win.chisel)
- [https://github.com/nicocha30/ligolo-ng](https://github.com/nicocha30/ligolo-ng)
    - 用 TUN （虛擬网络隧道）不是用 socks
    - TUN接口和SOCKS（Socket Secure）都是网络概念，但用途不同。
        - TUN 接口： TUN 接口是一个虚拟网络设备，它在OSI模型的网络层（第3层）操作。它允许用户空间程序在更低的层次与网络流量进行交互。
            - TUN 接口通常用于实现虚拟私人网络（VPN）解决方案。
            - 它们可以创建安全隧道，对网络数据包进行加密和封装，以便在不受信任的网络上进行安全传输。
            - TUN 接口通常用于建立点对点连接，将网络流量路由通过隧道以确保隐私和安全。
        - SOCKS：SOCKS 是一种在OSI模型的应用层（第7层）操作的协议。
            - 它充当客户端和服务器之间的中间人，为网络连接提供代理服务。
            - SOCKS 代理通常用于绕过网络限制、增强隐私并访问受限资源。
            - 当客户端通过 SOCKS 代理进行连接时，代理服务器代表客户端中继流量，使请求看起来像是来自代理服务器本身。
            - SOCKS 代理可以用于各种协议，包括HTTP、FTP等。
- [https://github.com/proxytunnel/proxytunnel](https://github.com/proxytunnel/proxytunnel)
    
    - 它支援透過 HTTPs 連接，因此透過以下設置，流量不會顯得可疑：
        - SSH 用戶端 -> HTTPs 加密 -> 企業封包嗅探代理程式 -> 您的伺服器 -> HTTPs 解密 -> SSHD 伺服器
- [https://github.com/juanfont/headscale](https://github.com/juanfont/headscale) / Tailscale 控制伺服器的開源、自架實現
    - 基於 Wireguard 建構的現代 VPN。
    - 工作原理類似於網路電腦之間的 overlay網路 - 使用 NAT 遍歷 [How NAT traversal works](https://tailscale.com/blog/how-nat-traversal-works)
    - 支援透過 HTTP 進行 Wireguard 封包轉送（協定的自訂新增；標準 Wireguard 僅在 UDP 上執行），用於棘手的 NAT 遍歷用例，但這僅在非常棘手的情況下使用。否則，它是一個常規 VPN，即封裝和加密的 IP over UDP。


- [autossh](https://www.google.com/search?q=autossh&rlz=1C5CHFA_enTW1035TW1035&oq=autossh&gs_lcrp=EgZjaHJvbWUyCQgAEEUYORiABDIHCAEQABiABDIHCAIQABiABDIHCAMQABiABDIHCAQQABiABDIHCAUQABiABDIHCAYQABiABDIHCAcQABiABDIHCAgQABiABDIHCAkQABiABNIBBzM0MWowajGoAgCwAgA&sourceid=chrome&ie=UTF-8)
    - 當您需要一個無處不在的工具來提供近乎永久的隧道來保持連接並使用標準加密時，沒有什麼比它更好的了





- [https://github.com/bryanpkc/corkscrew](https://github.com/bryanpkc/corkscrew) 透過 HTTP 代理建立 SSH 隧道的工具
- [https://github.com/q3k/crowbar](https://github.com/q3k/crowbar) 透過普通 HTTP 會話建立 TCP 隧道
    - not this [https://github.com/galkan/crowbar](https://github.com/galkan/crowbar)
        - 大多數暴力破解工具使用使用者名稱和密碼進行 SSH 暴力破解，但 Crowbar 使用 SSH 金鑰。這使得在滲透測試期間獲得的任何私鑰都可以用來攻擊其他 SSH 伺服器
    - When a [corkscrew](http://www.agroman.net/corkscrew/) just isn't enough..
    - 當您的網路連線受到僅允許基本連接埠 80 HTTP 的 Web 代理限制時，它允許您使用現有的加密 TCP 端點建立安全連接。
    - 僅使用 GET 和 POST 請求透過 HTTP 會話建立 TCP 連線隧道。
        - 這與大多數重複使用 CONNECT 動詞的隧道系統形成鮮明對比。
    - 提供基本身份驗證，以確保偶然發現服務器的人不會竊取您的代理以從 Silkroad 訂購藥品。
    - 如果您在辦公室環境中使用它，可能會被解僱


https://robotmoon.com/ssh-tunnels/


# to see


[Telebit™ Cloud](https://telebit.cloud/) [Code](https://git.coolaj86.com/coolaj86/telebit.js).
[GitHub - localtunnel/localtunnel: expose yourself](https://github.com/localtunnel/localtunnel)
[GitHub - vitobotta/docker-tunnel: A Docker-based self hosted alternative to Ngrok](https://github.com/vitobotta/docker-tunnel)
[GitHub - berstend/hypertunnel: ✨ Expose any local TCP/IP service on the internet.](https://github.com/berstend/hypertunnel)
[GitHub - jeffreytse/cactus-tunnel: 🌵 A charming TCP tunnel over WebSocket and Browser.](https://github.com/jeffreytse/cactus-tunnel)
[GitHub - DigitallyRefined/docker-wireguard-tunnel: Connect two or more Docker servers together sharing container ports between them via a WireGuard tunnel](https://github.com/DigitallyRefined/docker-wireguard-tunnel)
[GitHub - degola/punchmole: Punchmole is a simple tool to give your locally running HTTP servers a public URL and is an easy to self-host alternative to \`ngrok\` and/or \`tunnelmole\`.](https://github.com/Degola/punchmole/)
