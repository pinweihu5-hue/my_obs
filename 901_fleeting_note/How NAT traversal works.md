[[Tailscale_idx]]
[[MyIp repo 的 WebRTC test]]



---
## goal

**我們的目標是讓 UDP 封包在兩個裝置之間雙向流動，
以便我們的其他協定（WireGuard、QUIC、WebRTC 等）可以做一些很酷的事情**

![[IMG-How NAT traversal works-20250811203718693.png]]
[How NAT traversal works](https://tailscale.com/blog/how-nat-traversal-works)


- p2p不是新東西，webRTC, voip, 視訊軟體電話已經很多技術積累
- 需要一層代理做事情，這樣不用改到原本的app


## p2p 要解決 fw and nat
- 有狀態fw
	- 當然不可以改每台電腦的 fw
	- fw設定 default是都可以出，但都不可以進
		- 有沒有覺得怪怪的? 這樣你怎麼跟網站溝通? 你不是 打 req有 res嗎？可以進吧?
		- 對，所以真正的規則是：如果你有打過的 ip:port, 那 fw 會記住(因此 fw 是有狀態的) 並讓他進
		- 這邊的問題就是，要先自己打，才允許對應的 ip:port可以進來
	- 因此tailscale做的 nat遍歷就是
		- tailscale的協調 server 會讓每個網路中的電腦都知道要 直連的 ip:port
		- 架設有a(1.1.1.1), b(2.2.2.2)兩個電腦, 要直連
		- a(1.1.1.1) 打 b(2.2.2.2) → 因為 fw, b也沒看過1.1.1.1 → 拒絕
		- 這時候，tailscale的協調 server 會讓 b 也打給a → 因為 a 已經打過 b, 因此 a的fw可以接受b打過來的!
		- 然後這時候如果a再打給b, 就可以打了（因為b有打進來過!）
		- 簡單來說:協調 server 幫忙”偷”打，讓a以為有打到b且b回應, 但其實是協調叫b直接打給a(但因為a有打出去過，因此可以收)
- 上面的方式還是需要一個out of band 的側通道
	- 因為 nat 對udp連線通常有 30 sec timeout
	- 需要一個timer去記錄然後讓 2 side 可以差不多時間發出
	- 這邊使用 協調server的 自己做的derp(繞道加密路由協定) 作為 側通道 (side channel)
- what is NAT(network address translate)
    - ipv4不夠，因此pub ip有限，內網都用 pri ip
    - 透過 nat 來轉換 pub pri ip
    - nat 會重寫資料包的 ip:port
- 問題：建立p2p前，我們要如何知道另外一方的ip:port?
    - 我們上面已經解決了兩邊如何通過fw的問題，但目前是，對方連溝通都還沒溝通，我們連 ip:port都不知道
    - 需要先了解 STUN 

## STUN

- STUN（Session Traversal Utilities for NAT，NAT session穿越)
    - STUN是有關 nat穿越的研究和協議
    - 一個事實：一旦當你連到網路，你的 pub ip:port就可以知道了
    - 你可以問stun server: 請問我的udp ip:port從你的角度看是多少, stun 可以回答你


**stun 可以知道，從 internet 來看，你的 對外 ip 是多少**
**這樣 server 就可以知道如何連到你了**
![[IMG-How NAT traversal works-20250811203718887.png]]



- stun 還有更多功能，類似混淆，和跟 TURN and ICE 有關，但那是另一件事情了。
- 這就就夠了？
    - 對於簡單的NAT設置，that’s all!


但是難的 NAT?
每一個對外服務，甚至 port
NAT 都會展現出不同的mapping

像是下圖，5.5.5.5 要回去的話，需要對應到 2.2.2.2:5678
但是如果 stun 提供的是 2.2.2.2:7890就連不回去了
![[IMG-How NAT traversal works-20250811203719131.png]]



## NAT分類
- NAT 端口獨立
	- 換了目標端點，nat不會建立新的ip/port
- NAT 端口依賴
	- 換了目標端，nat會換ip/port
FW 也是
- FW 端口獨立
	- 換了目標端點，FW 不會建立新的ip/port
- FW 端口依賴
	- 換了目標端，FW 會換ip/port

最難穿越的就是對稱型 NAT，就是 FW and NAT 都端口依賴, 兩個都會換 ip/port
最簡單的就是全錐 (full cone )NAT

also see
- [[什麼是 全錐 (full cone) NAT 和對稱 NAT?]]
- [[不良林-代理进阶篇#UDP, fullcone全椎, 對稱]]
![[IMG-How NAT traversal works-20250811203719285.png]]
以前是用椎cone命名
更多資訊： RFCs [4787](https://tools.ietf.org/html/rfc4787) (NAT Behavioral Requirements for UDP), [5382](https://tools.ietf.org/html/rfc5382) (for TCP) and [5508](https://tools.ietf.org/html/rfc5508) (for ICMP).



這邊從穿越打洞的角度來說，我們都要穿越，因此下面我們會簡化成，簡單nat和難nat (就是好穿的 nat/fw 和難穿的 nat/fw)


## DERP
- fallback 使用 relay中繼
    - 有些nat是無法穿越的，像是柏克萊分校的 guest wifi, block all udp出去，除了 dns → 那就需要fallback方案
    - fallback?
    - 經典的資料包中轉協定是用 TURN (Traversal Using Relays around NAT):
	    - 你可以跟 TURN server 說你是誰之後(驗證)，TURN server 會分配給你 ip:port 和你想要去的地方的 ip:port 幫你中轉
        - 但是不太好用，且沒有 open server, 我們不用
    - 我們自己搞自己的 [DERP (Detoured Encrypted Routing Protocol)](https://tailscale.com/blog/how-tailscale-works#encrypted-tcp-relays-derp)
        - 也是通用的資料包中轉協定
        - 透過 http → 更適合嚴格outbound的網路環境
        - 根據目的地的 pubkey去加密



## 已達到 90% 連接性，但想要更高的話呢?
- 架設有一端為 hard nat → 直接用 stun 拿 IP, 假設 ip 是對的去處理 → 很多時候都可以猜到 (If you’re curious why, see REQ-2 in [RFC 4787](https://tools.ietf.org/html/rfc4787).)
- 那就剩下要猜 port, 有 65535個要猜。一秒傳10個包(10次) → worse case 要 10 分鐘可以猜到 → 問題是可能會被偵測到，因為你想是在做 port scan XD
- 假設你知道 [birthday paradox](https://en.wikipedia.org/wiki/Birthday_problem)…
	- 在難nat 那邊開 256的 port, 傳給簡單nat ip:port, 然後簡單nat那邊隨機match看是否可以成功
	- 簡單nat的隨機探測數量的增加，開放埠發生衝突（即成功通訊）的可能性，並假設困難側有 256 個連接埠：

| 隨機偵測的數量 | 成功機率  |
| ------- | ----- |
| 174     | 50%   |
| 256     | 64%   |
| 1024    | 98%   |
| 2048    | 99.9% |
[https://github.com/danderson/nat-birthday-paradox](https://github.com/danderson/nat-birthday-paradox)

- 因此，如果我們keep 1 秒有100個 port check → 2秒就可以猜到, 就算運氣不好，幾乎20秒就基本上可以找到
- 如果有兩端都是 hard nat 的情況下 → 又回到 10 分鐘
	- 日悖論讓我們將 `N` 「努力」轉換成 `sqrt(N)` 量級的東西。但我們對搜尋空間的大小進行了平方，因此即使減少了工作量，仍然需要付出更多的努力。為了達到 99.9% 的成功率，我們需要雙方發送 170,000 個探測。以每秒 100 個資料包的速度，我們需要嘗試 28 分鐘才能進行通訊。 50% 的情況下，我們「只」收到 54,000 個資料包後就會成功，但這仍然需要在沒有連線的情況下等待 9 分鐘。儘管如此，這還是比沒有生日悖論所需的 1.2 年要好
- 除非 nat reboot 和重清 cache, 上面都是一次性的
- 不過一些 nat, 像是 Juniper SRX 300 可以存的 session 就只有 64,000 個 → 因此我們的 port scan 可能很快就讓他滿了


## port mapping protocols
- 有其他更好的方法嗎？ 有的！ port mapping protocols 接埠映射協定
	- 1990有一個叫做 [UPnP IGD](https://openconnectivity.org/developer/specifications/upnp-resources/upnp/internet-gateway-device-igd-v-2-0/) 
		- 做什麼？ “嗨，請將 WAN 連接埠轉送到 `lan-ip:port` ”，以及 “好吧，我” 已經為您分配了 `wan-ip:port` 。
		- 推出幾年後，Apple 推出了名為 NAT-PMP（NAT 連接埠對映協定）的競爭協定
		- 不久之後，NAT-PMP v2 重生為 PCP（連接埠控制協定）
	- 你可以幫這些當做是強化版的 TURN! 如果local default gateway 有設定的話，用他們就簡單了！
	- 不過這些協定比較舊，然後有出現過漏洞（雖然已經補上)，因此有些nat策略是禁用的。
- 如果一台機器前面有多個，say 2 個nat 呢？
![[IMG-How NAT traversal works-20250811203719517.png]]

- 可以影響的還是離網路最近的那一層
- 距離目標比較近的還是得靠 port mapping 協議
- 不少人都說盡量不要使用雙nat
	- 降低視訊，多人連線遊戲的效能
	- 無法使用ipv6



## CGNAT carrier-grade NAT    /  營運等級NAT

![[IMG-How NAT traversal works-20250811203719665.png]]

- 營運商會用，isp, 社區網路業者
- 除了你的 router外，外面又有一層 nat, 因為 ipv4太少了    
- 如上圖， stun 只能幫我們到 離 internet最近的那一層，但是裡面怎麼辦？
- 有時候，port mapping 協議 就可以幫我們處理掉裡面了
- 不然就要靠 nat 有沒有支援 髮夾 hairpinning, 直接裡面內網就可以路由過去了


## ipv6?
- 用 ivp6
	- 可以擺脫 stun, 不用管暴力解+生日悖論技巧，是否有port mapping 協議，髮夾
	- 還是得處理中繼，狀態fw, 誰要先溝通問題，傳遞 ip:port等 meta data
- 不過ipv6的 suppprt 目前全世界約1/3而已，而且不是均勻分配（就是沒有的地方區域大概就是都沒有）
- nat64設備
	- 64表示內部ipv6 ↔ 外面ipv4
		- 內網ipv6可以跟外網ipv4的世界溝通
		- 因此現在一些設備叫做 net44, 或是上面的cgnat叫做 net444
- 我們需要 CLAT (Customer-side translator — from Customer XLAT)
	- clat 這一層幫我們做到net64的事情, 我們可以假設就是ipv4的連接
	- clat 在 mobile 是標配，但桌機，筆電和server就不是，這邊需要自己實作。


## ICE (冰人 -> 動態建立連線)
Interactive Connectivity Establishment (ICE) protocol [RFC 8445](https://tools.ietf.org/html/rfc8445)   

- 也來自電話時代就有的東西
	- like stun, turn
- 簡單說：ice會 try out 可用的路徑，找出有效的！說完了。
- 可用路徑包括哪些？
	- ipv6:ip:port
	- 區網LAN的ipv4, ip:port
	- stun 提供的網際網路ipv4 wan的ip:port (可能 via nat64/clat)
	- port mapping協議 提供的網際網路ipv4 wan的ip:port
	- 運營商提供的 endpoint, 靜態的
- 通常會從好找的開始找，類似 LAN > WAN > WAN+NAT
- 如果發現剛好的連接性，會自動且透明的把連接升級
- 也會準備幾組 warm fallback, 如果連接有問題，就降級，最後就是relay

- 安全性
    - 這邊的連線都需要包在https quic(tls) 和 wireguard pub key 來傳輸
    - 如果沒有？那你自己要負責實作加密喔

## ltdr
1. 基於udp
2. 可以直接在程式存取 網路 socket
3. 需有side channel來溝通
4. 幾個 stun server
5. 需要有 fallback relay
6. enumerate 連接上的可能的所有 ip:port
7. 跟 stun server 拿到 wan ip:port 和識別 簡單和難的nat
	1. what is wan see 註一
8. 用 port mapping協議找到 wan ip:port
9. 檢查 nat64, 找到 wan ip:port
10. 使用 side channel來跟 peers 交換這些 ip:port 和 金鑰 等訊息
11. 如果有狀況, fallback to relay
	1. relay也用來幫助你傳遞訊息來快速建立通道
12. 透明升級連線如果有更好的連線方法
13. 如果連線出問題，透明的降級連線
14. 內容都 e2e 加密和身分驗證






註一
WAN 代表 **廣域網路**（Wide Area Network）。它是一種電信網路，覆蓋範圍廣泛，通常可以跨越多個城市、國家甚至大陸。WAN 用於連接較小的網路，例如局域網（LAN）或城域網（MAN），使它們能夠互相通信。常見的 WAN 技術: 包括
- **MPLS（多協議標籤交換）**：一種加速和塑造網路流量流的方法。
- **VPN（虛擬私人網路）**：一種通過互聯網建立的安全連接，允許遠程用戶訪問私有網路。
- **租用線路**：提供兩個位置之間穩定可靠的專用電信線路。
- **衛星通信**：在傳統有線連接不可行的偏遠地區使用。