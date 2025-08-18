
[PySkyWiFi: completely free, unbelievably stupid wi-fi on long-haul flights | Robert Heaton](https://robertheaton.com/pyskywifi/?utm_source=hackernewsletter&utm_medium=email&utm_term=fav)

- in flight wifi "不可以"讓你上網, 但是卻有一些地方可以讓你調整設定，類似可以調整你的 airmile a/c page 的 edit -> 這邊不就可以上網了嗎? XD
- first ver:  2 side use naming field update and can talk to each other (IM, instance message)
- second ver: before you go into airplane, setup a deamon at home and can receive msg from in-flight-pc and get the football score and send back to the in-flight-pc 
- thrid ver: 
	![[IMG-tcp over name editable fields-20250811203627948.png|990]]
- transport layer and network layer
	- transport layer: tcp, handle split out big msg into small chunk, and where to send to which client (setup connection)
	- network layer: handle send it self, like ip does
- this is not 雙工, like sender can send and receive can also send at the same time
	- both side only can be one role (sender or receiver) at the time
	- and receiver ned to send back ack to let sender know it received
	- and also need to carry with seq-id to let sender know what receiver did received
	- and send chunk by chunk
- also talk abt network layer and transport layer are sepration of concern and modulize
	- so we can use other airmile a/c (imple details in network layer) and the transport layer still work
	- transport layer only care about read and write, it's not his work to think about how to process underline of read and write to the network (let's network layer's work)
- what is HTTP CONNECT
	- when you must go thru a http(no s!) proxy to go the some bank/gamail/whateverButRequiredHTTPS website/service, the brower or app will use HTTP CONNECT to setup a connection from ur ori to your destination, the proxy server will not be able to parse/check the payload, just proxy thru.
	- when you will need to must go thru  http(not s!) proxy?
		- company/org often have a http proxy server to manage every outbound traffic
	- VPN over HTTP：
		- 某些 VPN 解決方案使用 HTTP CONNECT 來通過 HTTP 代理建立 VPN 連接。
	- WebSocket 連接：
		- 在某些網絡配置中，WebSocket 連接可能需要通過 HTTP 代理，這時也會使用 CONNECT 方法


[my – FigJam](https://www.figma.com/board/4MD4T4IegKTKPRN3RdJGE5/my?node-id=35-491&t=zep0NJjCCIKZE0YK-0)
![[IMG-tcp over name editable fields-20250811203629061.png]]



github
[GitHub - robert/PySkyWiFi: Completely free, unbelievably stupid wi-fi on long-haul flights](https://github.com/robert/PySkyWiFi)



reddit comment   [PySkyWiFi: completely free, unbelievably stupid wi-fi on long-haul flights : r/programming](https://www.reddit.com/r/programming/comments/1e1ejon/pyskywifi_completely_free_unbelievably_stupid/)
- [yarrick/iodine: Official git repo for iodine dns tunnel](https://github.com/yarrick/iodine) 
	- lets you tunnel IPv4 data through a DNS server. 
	- This can be usable in different situations where internet access is firewalled, but DNS queries are allowed
	- or called TCP/IP-over-DNS
- [Imgur: The magic of the Internet](https://imgur.com/cer0ZDT)
- [how to use use SSH tunnels to get free wifi? - Google 搜尋](https://www.google.com/search?q=how+to+use+use+SSH+tunnels+to+get+free+wifi%3F&rlz=1C5CHFA_enTW1035TW1035&oq=how+to+use+use+SSH+tunnels+to+get+free+wifi%3F&gs_lcrp=EgZjaHJvbWUyBggAEEUYOTIHCAEQIRigATIHCAIQIRigATIHCAMQIRigATIHCAQQIRigATIHCAUQIRigAdIBCDYyMjlqMGo3qAIAsAIA&sourceid=chrome&ie=UTF-8)



hacker news comment [PySkyWiFi: Free stupid wi-fi on long-haul flights | Hacker News](https://news.ycombinator.com/item?id=40915082)
- change host header to bypass paywall
	- allow strip acess (to be able to pay) and strip and reddit both use fastly CDN
	- connect to strip but modify the HOST header to trick the fastly think you are asking reddit, so author can visit reddit for free
	- since  一個 CDN 服務器通常同時為多個網站提供服務, and 當 CDN 接收到請求時，它需要知道應該提供哪個網站的內容, and it use host header to decide
- a good name! -> TCP-over-shared-editable-fields
- [cs.brown.edu/courses/cs168/s11/handouts/dtun.pdf](https://cs.brown.edu/courses/cs168/s11/handouts/dtun.pdf)
-  [aleixrodriala/wa-tunnel: Tunneling Internet traffic over Whatsapp](https://github.com/aleixrodriala/wa-tunnel)
	- a lot of flight or 渡輪 will not block you on whatapp...so you can use this...
- 大多數飛行 WiFi 網路不會阻止 DNS 流量，因此如果您設定自訂 DNS 伺服器，則可以透過 DNS 傳輸所有內容。雖然網速慢，但免費！
- 我曾經在飛機上發現，即使我沒有付費，ssh 也不會被阻止，so I just setup as a socks proxy to browse the web.
- [(602) Bob Wehadababyitsaboy - YouTube](https://www.youtube.com/watch?v=9JxhTnWrKYs)
	- 以前打電話要付費，但是如果通了，對方get first name and last name and if it's wrong, 掛掉，就不用付費
		- 把訊息放在 firstname and lastname XD
- 最近我發現 Cloudflare 的 WARP 應用程式可以讓我繞過一些長途航空公司的僅限聊天限制。你會得到大約 5kbps 的頻寬，但這足以讀取 HN。
- ZeroTier, Tailscale, and several other UDP based mesh protocols will sometimes work in “free” mode on planes, but it tends to be horrifically slow.
