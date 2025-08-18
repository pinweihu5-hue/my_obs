ref: [【DNS泄露】实战演示DNS泄漏，全局模式就不会dns泄漏？解答DNS泄露的争议，最简单clash配置DNS方法，fakeip到底会比realip提升网速吗？跨境电商网络环境要求高该如何防止DNS洩露 - YouTube](https://www.youtube.com/watch?v=TOhF5RcTGiM&list=PL5TbbtexT8T3JJdJAy73A0T2NXZL2JEJY&index=6)

- 從 dns 來知道你有沒有科學上網是好最查的
    - 因此 躲審查避免dns leak 很重要。


- 科學上網下， what is DNS leak? 兩種定義：
    - 沒有透過fw外的節點幫發dns就算是leak
    - 只有針對 google, yt 等黑名單的網站去發 明文dns 請求才算leak

- 如果你用舊的 clash for win, 就算是全局模式，使用 mixin 設定就會有 redir-host → 會本地發 dns 請求
    - wireshark就可以看到你訪問的 ip, like 8888
    - 新版已經棄用 redir-host → 會用fake ip 模式
    - fake ip 模式
        - 就是 本地會向電腦內其他網卡(win下)發起 dns 請求
        - 要去win策略組禁用多宿主dns解析
        - 關掉後 → dns 請求就不會被dns leak up 網站看到國內了
        - 只會看到國外 → 表示應該透過節點發起 dns
    - 但是接著訪問 google → 你會看到 wireshark 還是有發給外面 dns
        - 這是因為 clash for win 只要遇到http3支援的quic/udp → 就會發起 dns 請求
        - 因此需要關掉瀏覽器的 quic
    - 這時候，就變成用 tcp, 你看 wireshark 也不會有 dns了



    - **看 wireshark 有沒有發 dns 最準！**



- 不用全局，用分流模式
    - ip leak 網站可以看到
    - 但是下面用黑名單模式，就是說，黑名單會proxy, 只有國內才會直連，國外就不會發 dns
    - 因此的確會漏，但是這邊會漏的就是非黑名單的
    - 這個就看你對於漏的定義了。
![[IMG-不良林 more on DNS leak-20250811203630327.png]]





        
- 另一個例子
![[IMG-不良林 more on DNS leak-20250811203631873.png]]
- 把 ipleak 加進去，因此撞到規則，就會走代理 → ip leak 網站不會看到國內 → 沒啥意義，你是檔查的 XD
- 但是paypal沒在黑名單 → 因此還是會漏, 不會走代理



- 另一種就是用白名單
	- 國內直連加入白名單，剩下全部都走proxy, 基本上會把大的國內網站都加入
	- 缺點就是你國內的大概也會漏掉一些小網站，變成會走節點這樣



- 另外dot and doh, isp看不到你的dns, 但在國內，這些dot等服務商還是會看到
- 國外dot不普遍，這些流量也會被重點看待
- 另外這個影片有利用 sing_box 使用 fake ip and real ip 的配置
![[IMG-不良林 more on DNS leak-20250811203634005.png]]


real ip and fake ip 差在 dns 解析速度 (fake ip 就是電腦裡面轉一圈)
![[IMG-不良林 more on DNS leak-20250811203635481.png]]