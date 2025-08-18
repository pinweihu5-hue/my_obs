

攻擊者會將偽造的 DNS 資訊注入 DNS 快取，使 DNS 解析到錯誤的 IP 地址。當用戶嘗試訪問一個網站時，他們的電腦會向 DNS 伺服器查詢網站的 IP 地址。如果 DNS 快取已被污染，用戶就會被導向到一個偽造的網站，而不是真正的網站。



**攻擊過程：**
1. **目標 DNS 伺服器：** 攻擊者選擇一個目標 DNS 伺服器，通常是用户常用的 DNS 伺服器，例如 ISP 提供的 DNS 伺服器或公共 DNS 伺服器。
2. **偽造 DNS 回應：** 攻擊者偽造一個 DNS 回應，將目標網站的域名解析到一個攻擊者控制的 IP 地址。
3. **注入 DNS 快取：** 攻擊者利用 DNS 協議的漏洞或其他手段，將偽造的 DNS 回應注入目標 DNS 伺服器的快取。
4. **用戶被導向偽造網站：** 當用戶嘗試訪問目標網站時，他們的 DNS 查詢會被目標 DNS 伺服器解析，由於快取中存在偽造的記錄，用戶會被導向到攻擊者控制的偽造網站。
![[IMG-DNS Spoofing-20250810200233710.png]]

很多層級可以下手
hack into host → 改 local dns → etc/host
瀏覽器, os, 路由, 中間的路由，cache 都可以跟你 spoofing

例子
ips or 網路公司 給你偷換變成廣告網頁, 等等




**如何防範 DNS Spoofing：**

- **使用 DNSSEC (DNS 安全擴展)：** DNSSEC 可以驗證 DNS 記錄的真實性，防止 DNS 欺騙。
- **使用安全的 DNS 伺服器：** 選擇信譽良好且安全的 DNS 伺服器，例如 Google Public DNS 或 Cloudflare DNS。
- **防火牆：** 配置防火牆規則，阻止來自可疑 IP 地址的 DNS 流量。
- **定期清除 DNS 快取：** 定期清除本地電腦和 DNS 伺服器的 DNS 快取，可以減少 DNS 欺騙的影響。
- **監控 DNS 流量：** 監控 DNS 流量，可以及早發現 DNS 欺騙攻擊。


ref:
- [什麼是 DNS 快取記憶體中毒？ | DNS 詐騙 | Cloudflare](https://www.cloudflare.com/zh-tw/learning/dns/dns-cache-poisoning/)
