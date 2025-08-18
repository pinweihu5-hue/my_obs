

- [x] 好奇那個Josh那個網卡的虛擬網卡有什麼功能？
	- ### [TP-Link](https://www.momoshop.com.tw/search/searchShop.jsp?keyword=TP-Link&brand=TP-Link&brandNo=20160808155951347) ER605-UN-SafeStream Gigabit 多 WAN VPN 路由器 / 分享器
	- 多wan可以lb (fail over) -> assume you have 中華電信和第四台 XD
	- router可以建立 vlan, 類似如果你是包租公，你可以讓每個房客都有自己虛擬網段


- try note taking app
    - [lostdesign/linked: 🧾 Daily journaling without distraction. An easy, distraction-free way to record your thoughts, declutter your mind and keep the things you want to remember. Join the discord at](https://github.com/lostdesign/linked) [https://discord.gg/uNjJzZvccr](https://discord.gg/uNjJzZvccr)
        - 或許簡單就是他的優點吧
        - but obdiain can cover his need
    - [https://github.com/tw93/MiaoYan](https://github.com/tw93/MiaoYan)
        - doc is put at
            - `/Users/re4388/Library/"Mobile Documents"/iCloud~com~tw93~miaoyan`
        - good at ppt making
        - I like its 美學
        - all saved into no local
            - no cloud sync, at least in its out-of-box form
        - pure markdown → see need to use preview mode to see IMG (like other md editor)



- 點兩下 exe, 剪輯app試用 → 中木馬 → 偷 browser session cookie → 改你的yt或是其他東西的帳密
    - [(420) 頻道被盜進行詐騙直播 我三小時搶回來了 黑客是如何突破 Google 賬號的安全防衛的？ - YouTube](https://www.youtube.com/watch?v=CB7m8-VMKzw&list=WL&index=1)
    - 他不等官方，官方要等一個月至少
        - 你還得夠大有專屬客服為前提
    - 可以用倍用帳戶？無法，因為google 無法讓你一直登入
    - how he get back by himself?
        - 他用另一個電腦，弄了一個新帳號，跑木馬軟體，然後刻意真的會被偷session cookie, 登陸，然後他可以用backup a/c 確認是那個攻擊者在那個區域
        - 用 vpn去那個區域，就可已用back a/c登入了 XDD → 然後就是拿回主帳號
        - 作者後來改用硬件auth的方法XDD
    - 虛擬機裡面看到的那個 ip, 只是 control and command server, CNS server
        - 因此偷cookie 的那個是加拿大ip
        - 用來登入受害者電腦的是美國印第安那ip



- 看 netflix
    - [(416) 【新手科普】中国大陆用户观看奈飞的必要条件，什么是解锁奈飞？什么是原生IP？什么是合租？为什么原生IP无法解锁奈飞？Netflix新手科普教程 - YouTube](https://www.youtube.com/watch?v=iTCAhv8F-v8)
        - 解鎖netflex
            - 租的vps的ip分三類
                - 住宅ip, 就是一般的靜態ip
                - 原生ip,資料中心idc配發的, ip歸屬地和vps機房位置一樣
                - 廣播ip, 資料中心idc配發的, ip歸屬地和vps機房位置不同
            - 鎖ip?
                - 多用戶，大流量，長時間用同一個ip → 容易被偵測不合理 → go 黑名單, 這是代理ip
                    - 可能封整個ip段，封整個idc, 封整個服務（vulter）
                        - 因此會導致被波及，你用原生的一樣被ban
                        - 因此會有找純淨ip的用法 → 就是還沒被加入黑名單
            - vpn or 機場用戶 → 直接問或查是否可以看
            - 自建
                - 需要確認ip是否被封過
                - 利用dns劫持, 指向一台可以解鎖耐飛的server
            - 帳號
                - 去低價買，類似土耳騎 → 但是很多人這樣搞可能很難搞了
                - [奈飞小镇](https://netflixtown.com/) → 這個服務幫你去低價買還幫你弄合租

[(416) 原生IP、住宅IP新手科普，为什么需要住宅IP？通过虚拟网卡中转使住宅IP代理高速稳定，解决住宅IP国内无法连接的情况，911S5替代品，住宅IP与机房IP有什么区别？住宅IP类型检测、IP纯净度检测 - YouTube](https://www.youtube.com/watch?v=rjHlF3oskvk&t=47s)


[(416) 奈飞解锁方式大全，原生IP解锁、二级代理解锁、WARP解锁、DNS劫持解锁，详细介绍每种解锁方式，总有一种适合你。disney+、HBO、hulu、Spotify等流媒体平台可参考 - YouTube](https://www.youtube.com/watch?v=Vj4TGd9IaQc)

[(416) 【全网首发】连接节点就能免费观看奈飞？！速蛙云跑路了，可以把福利社留下，免费看netflix、P站、disney+的实现原理，删除曾经安装的证书保护隐私 - YouTube](https://www.youtube.com/watch?v=vxd2qdEIsvY)

[(416) 网赚必备的住宅IP使用技巧，让你的脏IP节点重新支持解锁流媒体、chatgpt、paypal、跨境电商等高要求网络环境，静态住宅IP无需链式代理直接使用的方法，手机端使用住宅IP方法 - YouTube](https://www.youtube.com/watch?v=W-fDjn0MxeI&t=0s)


