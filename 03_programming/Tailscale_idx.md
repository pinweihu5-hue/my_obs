Tailscale是一個基於 WireGuard 的虛擬私人網路（VPN）服務，旨在簡化網絡連接，讓用戶能夠輕鬆地將設備連接在一起。它可以用於遠程訪問內部服務，無論這些服務是在本地網絡還是雲端。

related: [[dns_idx]]


# 設定

## taildrop
```sh

# from my acer to mac, remember the ending colon
tailscale file cp ./my-file.txt air:



# from my mac to acer
# mac side use gui
# linux side use below command at the locaiton you want to get it dl
 sudo tailscale file get .


```






## to win10 asus
- [ ] still can’t solve using pub and pri key
    - still need to use microsoft pass to login
```Bash
ssh re438@laptop-uo6djs4g.tailbcab9.ts.net
ssh weijh@laptop-uo6djs4g.tailbcab9.ts.net


```



- linux 安裝步驟
    ### Linux
    1. 參考 [官網](https://tailscale.com/download/linux)指示，使用指令稿安裝，支援Debian/Arch/Fedora系的發行版
    ```Plain
    curl -fsSL https://tailscale.com/install.sh | sh
    ```
    1. 安裝後設定開機自動啟用Tailscale服務
    ```Plain
    sudo systemctl enable --now tailscaled
    # 檢查服務狀態
    sudo systemctl status tailscaled
    ```
    1. 將裝置加入Tailscale網路，點選終端機顯示的連結，到Tailscale網頁後台驗證，並在後台按Approve同意加入。
    ```Plain
    sudo tailscale up
    ```
    1. Linux版Tailscale目前還沒有圖形界面，你可以加裝第三方開發的 [KTailctl](https://flathub.org/apps/org.fkoehler.KTailctl)或 [Trayscale](https://github.com/DeedleFake/trayscale)。裝完後記得將使用者加入Tailscale的operator：
    ```Plain
    sudo tailscale set --operator=$USER
    ```
  

## acl

- [ACL 和存取規則範例 · Tailscale 文檔 --- ACL and access rule samples · Tailscale Docs](https://tailscale.com/kb/1192/acl-samples#resource-level-access-policies)



## cli
[https://tailscale.com/kb/1080/cli#using-the-cli](https://tailscale.com/kb/1080/cli#using-the-cli)


## split DNS
- 如果你要連上 gcp, 你要把 gcp 的那個網域的 subnet 加入下面的 splitDNS
- 因為你電腦已經把 dns請求讓 ts處理，因此你需要跟 ts說特定的網域，你需要用透過哪些 dns server ip 來處理
- 不過這需要 gcp 接受 dns轉發
- 如果公司 gcp無法，那你也可以就該 device 暫時關閉 ts 的 dns 服務

  

## debug 延遲

感覺慢慢的，發現 tailscaled 讓 cpu 滿載 → 重開

```Bash
htop
sudo systemctl restart tailscaled
```
  

Tailscale會嘗試各種方式連線，但若P2P失敗就會走relay伺服器，導致延遲變高。遇到這種狀況就得開放通訊埠讓裝置之間做P2P連線。

使用`tailscale status`指令查看兩個節點是否有P2P成功，出現relay的話就是走官方伺服器，延遲較高。

```Plain
100.705  linux        qj985n2@     linux   active; -
100.125  windows     qj985n2@     windows  active; relay
```

Tailscale使用的通訊埠是`41641/udp`，在連線的二個裝置用指令開放防火牆：

```Plain
sudo ufw allow 41641/udp
```

再用`tailscale status`指令確認是否有顯示direct，表示P2P成功

```Plain
100.705  linux        qj985n2@     linux   active; -
100.125  windows     qj985n2@     windows  active; direct
```

  

走中繼伺服器的原因可能是防火牆阻擋了 UDP port，導致 tailscale 只能走中繼

若要改善 ping 延遲，可檢查路由器相關設定或防火牆來達成直連：

1. 例如開啟 UPnP 與 NAT-PMP （[較不安全](https://www.quora.com/What-does-disabling-UPnP-on-a-router-do)，但無腦自動設定好）。
2. [修改防火牆設定](https://tailscale.com/kb/1082/firewall-ports/)，關閉 UPnP，並允許防火牆內部裝置使用 UDP 通訊，並指定通訊端口為 41641，目標端口則是任意（不同廠家路由器有不同設定）。
3. Linux 中可能阻擋到端口，開啟終端機並輸入 sudo ufw allow 41641/udp 以開啟 UDP 端口。

```Bash
PS C:\Users\wells> tailscale ping nas
pong from nas (100.105.151.221) via 142.251.43.14:41642 in 12ms

PS C:\Users\wells> tailscale ping work
pong from work (100.122.114.19) via DERP(tok) in 190ms
pong from work (100.122.114.19) via DERP(tok) in 86ms
pong from work (100.122.114.19) via DERP(tok) in 88ms

PS C:\Users\wells> tailscale status
100.105.151.221  nas          wells@  windows active; direct 142.251.43.14:41642
100.122.114.19   work         wells@  linux   active; relay "tok"
100.206.21.12    server1      wells@  linux   idle, tx 22416 rx 28304
100.95.142.53    server2      wells@  linux   -
100.111.59.11    home-1       wells@  windows active; direct 142.251.43.14:41641, tx 10684092 rx 110457924
100.92.116.28    home-2       wells@  windows -
100.154.231.41   wells-iphone wells@  iOS     offline
```

  

  

## tailscale tunnel → 開出來的 https public 都可以看

related: [[tunnel_idx]]

[https://tailscale.com/kb/1223/funnel](https://tailscale.com/kb/1223/funnel)
有一些 gui 設定需要打開，請看文件，一次性的
```Bash
# 開一個server for test
python -m http.server 3001

# only first time, 建立 tls cert
tailscale cert air.tailbcab9.ts.net

# run up
tailscale funnel 3001


tailscale funnel status
```
限制：
- 漏斗僅限於偵聽連接埠 `443` 、 `8443` 和 `10000`
- 漏斗僅適用於 TLS 加密的連接
- 漏斗上的流量受頻寬限制。這些限制目前不可配置。


 **目前 mac OS funnel 還沒搞定 2024_05_26-17_38_47**
 

[MacOS：我需要將憑證從 https 容器中移出嗎？ ：r/尾鱗 --- MacOS: Do I need to move the certs out of the container for https? : r/Tailscale](https://www.reddit.com/r/Tailscale/comments/166x8h8/macos_do_i_need_to_move_the_certs_out_of_the/)

  
## tailscale serve → 開出來的 https 加入 tailnet 才可以看
- [Tailscale Serve · Tailscale Docs](https://tailscale.com/kb/1312/serve)
- 手機或是平板，比較不方便開 ssh local forward (也是可以辣) 的情況，直接用 tunnel 給一個 domain出來連會更方便

[https://www.reddit.com/r/Tailscale/comments/14gccua/funnel_serve_multiple_ports/](https://www.reddit.com/r/Tailscale/comments/14gccua/funnel_serve_multiple_ports/)

```Bash fold


sudo tailscale serve 3001


# 也可以吃 file path
sudo tailscale serve /a/b/c/hello.txt
sudo tailscale serve /a/b/c/folder1


# http 和 https 都可以
sudo tailscale serve --http 3003 http://127.0.0.1:3002
sudo tailscale serve --https 3002 http://127.0.0.1:3003


# 用例, for monitorix 
sudo tailscale serve --https 8081 http://127.0.0.1:8081

# set path
sudo tailscale serve --http 3002 --set-path /a2 http://127.0.0.1:3002
sudo tailscale serve --set-path /a3 http://127.0.0.1:3003

# run in bg
sudo tailscale serve --bg --set-path /one http://127.0.0.1:8082
sudo tailscale serve --bg --set-path /two http://127.0.0.1:3210


sudo tailscale serve --https=xxxx off



ts serve status

# 清掉 all serve
ts serve serve reset
```

  

限制

- 瀏覽器如果有設定 nextDNS 或是自己的設定，會掛掉
    - 因為你如果你進入 tailnet vpn, 你已經讓 ts 托管你的 dns設定，從這個vpn出去的 dns 需要由那邊去調整和設定
    - 因此如果瀏覽器客戶端自己調整，很高機率會打不通。
    - 你關掉，就可以 server 的通道就可以通了。
- DNS 名稱僅限於您尾網的網域名稱 ( `node-name.tailnet-name.ts.net` )

  


  

## Tailscale SSH

- [Tailscale SSH is now Generally Available | Hacker News](https://news.ycombinator.com/item?id=40060901)
- [Tailscale SSH · Tailscale 文檔 --- Tailscale SSH · Tailscale Docs](https://tailscale.com/kb/1193/tailscale-ssh#how-is-tailscale-ssh-different)
- 不同於傳統的keypaire → 改為可以用 ACL 在 ssh上
    - Tailscale ssh 為您處理身份驗證
        - Tailscale 附帶一個額外的 SSH 伺服器，它與其他 SSH 伺服器並行運作。它直接使用 Wireguard 密鑰，因此您無需管理密鑰。
        - 因此您無需將 authorized_keys 傳遞給所有主機
        - 可以集中配置 / 啟用 / 停用人們的存取
    - user 可以有 group
    - device 可以有 tag
    - 然後 group ↔ tag
    - 有 allow and check (一段時間需要 re-auth) 兩種 ACL
    - 有 ssh posture → 類似特定電腦和使用時間才可以登入
    - 可以設定登入的角色 root or 特定 user
    - 禁用，啟用，都是即時的
- Tailscale 不需要對 SSH 進行特殊處理。你還是可以很好地運行普通的舊 sshd

  

### DNS 設定

![[IMG-Tailscale_idx-20250811204403384.png]]



## exit node
> 出口節點捕獲所有網路流量，這通常不是您想要的。若要將 Tailscale 設定為僅路由某些子網路（更常見的設定），請改為閱讀 [subnet routers](https://tailscale.com/kb/1019/subnets)。
- why?
    - 你在不安全的 wifi環境，你想要先連到家裡，再連到網際網路，家裡要設為出口節點。
    - 你在A地，你想要讓你登入的網站認為你在B地， B地要作為出口節點。
- 設定
    - 那台設備需要宣告
        - 退出節點只能是 Linux、macOS、Windows 或 Android 裝置
    - admin 控制要允許這個宣告
    - 最後就是大家就可以選他作為出口節點了

```bash title:設定 fold


# 如果 有 /etc/sysctl.d directory, like acer:
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf

# 不然就用
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p /etc/sysctl.conf


# 如果有用firewalld
# 允許 Firewalld 對通過防火牆的數據包進行 NAT 轉換,也就是修改源 IP 地址
firewall-cmd --permanent --add-masquerade


sudo tailscale up --advertise-exit-node

# 然後要去admin console 去用允許打開


118.231.206.61
```

  

## exit node：去 gcp 上租一個 在日本的vm, 設定出口node → 登入 netflix 日本
[Access Google Compute Engine VMs privately using Tailscale · Tailscale Docs](https://tailscale.com/kb/1147/cloud-gce#step-2-allow-udp-port-41641)
1. 建立 vm 時
    - 要記得 把 ip 轉發打開
        
        ![[IMG-Tailscale_idx-20250811204405853.png]]
        
    - 記得選對國家
    - 用 ubuntu (比較familiar)
    - 用 e2-micro, 夠用了

2. 安裝ts
    - [install Tailscale on Linux](https://tailscale.com/kb/1031/install-linux)
    ```bash fold
    curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/focal.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null
    curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/focal.tailscale-keyring.list | sudo tee /etc/apt/sources.list.d/tailscale.list
    
    
    sudo apt-get update && sudo apt-get install tailscale -y && sudo tailscale up
    
    
    
    
    
    tailscale ip -4
    ```
    
3. GCP FW 要允許讓 ts 打洞
    1. 調整default to allow udp port on ipv4 and ipv6 (看 [doc](https://tailscale.com/kb/1147/cloud-gce#step-2-allow-udp-port-41641))
        
        ![[IMG-Tailscale_idx-20250811204406943.png]]
        
    2. 如果你用 default 的 fw, 一次性的
4. ubuntu 裡面也要開啟 ip 轉送
    
    ```Bash
    sudo vi /etc/sysctl.conf
    
    # ipv4
    net.ipv4.ip_forward=1
    \#ipv6
    ...
    
    sudo sysctl -p
    
    sysctl net.ipv4.ip_forward
    ```
    
5. 建立網段
    1. 找到 vm 的網段
        
        ![[IMG-Tailscale_idx-20250811204407083.png]]
        
    2. 用 ts宣告網段
    
    ```Bash
    # 網段開不同區域的話，會變，要確認
    sudo tailscale up --advertise-routes=<網段> --accept-dns=false
    
    # ex
    sudo tailscale up --advertise-routes=10.170.0.0/20 --accept-dns=false
    ```
    
    c. 另外 ts 的 machine 頁面也要點 enable subnet, 你會看到驚嘆號
    
    ![[IMG-Tailscale_idx-20250811204407534.png| 200]]
    
6. 建立新的 gcp DNS 政策 來允許 ts的入站轉送
    
    1. 一次性
    
    ```Bash
    
    # 
    gcloud dns policies create inbound-dns \
      --project="inner-topic-419501" \
      --description="Expose DNS endpoints per subnet" \
      --networks="default" \
      --enable-inbound-forwarding
    
    ```
    
    - 驗證您的 tailnet 是否可以辨識 tailnet 子網路的 DNS 解析器
        
        - 另外也要查GCP每個網段的 DNS resolver 的 IP
        
        ```Bash
        
        # 查GCP每個網段的 DNS resolver 的 IP
        gcloud compute addresses list \
          --project="inner-topic-419501" \
          --filter='purpose="DNS_RESOLVER"' \
          --format='csv(address, region, subnetwork)' 
          
          
        # 東京  
        # => 10.146.0.4,asia-northeast1,default
        
        # 台北
        # 10.140.0.3,asia-east1,default
        
        # hk
        # 10.170.0.2,asia-east2,default
        
         
        ```
        
7. 使用從此命令傳回的 IP 位址作為 ts 的 DNS 解析 (go to [admin dns](https://login.tailscale.com/admin/dns) in ts)
    
    ![[IMG-Tailscale_idx-20250811204407634.png]]
    

- 做到這邊， vm 已經正式進入 tailnet
    - 例如，你可以拿到gcp fw 的 `default-allow-ssh`  rule → 不需要透過ssh連入
    - Android、iOS、macOS、tvOS 和 Windows 上的用戶端會自動發現這個新建的 subnet routes
        - 但是 linux 預設不會自動偵測，需要打開:
            
            ```Markdown
            # 需要把其他的設定一次都也要打出來，類似宣告 exit mode --advertise-exit-node
            sudo tailscale up --accept-routes --advertise-exit-node
            ```
            

1. 設定 exit node
    
    1. [https://tailscale.com/kb/1103/exit-nodes](https://tailscale.com/kb/1103/exit-nodes)
    
    ```Bash
    # 調整tailscale 的路由設定, 允許轉發
    echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
    echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
    sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
    
    
    
    # 宣告可以是 exit node
    tailscale up --advertise-routes=<網段> --accept-dns=false --advertise-exit-node
    
    # ex jp
    sudo tailscale up --advertise-routes=10.146.0.0/20 --accept-dns=false --advertise-exit-node
    
    # ex hk
    sudo tailscale up --advertise-routes=10.170.0.0/20 --accept-dns=false --advertise-exit-node
    ```
    
    - 需要去 admin machine 那邊調整為 exit node, 剛進去會看到有 驚嘆號
2. 看看是否可以看到 netflix 的日本畫面! 成功!

  

  ,
  

## exit node：解決設定出口節點無法訪問公司內網的問題

→ 可以有空再看 HN comment 有沒有其他網友的解法

- ref: [https://mrkaran.dev/posts/travel-tailscale/](https://mrkaran.dev/posts/travel-tailscale/)
- 作者公司內部的vpn可以連到aws的vpc
    - 公司內部 vpn對應的一個網路介面(wt0)，啟動時會廣播連到aws vpc的路由
    - 因此當作者要連到aws vpc, 可以透過 wt0到達
- 作者遇到一個問題:
    
    - 作者去國外玩
    - 筆電要透過wt0h才可以連到公司的vpc
    - 但是設定 exit node後，筆電所有流量都往出口節點 via tailscale0介面 → 連不上wt0 → 進不去公司內網
    
    ```Bash
    $ ip route get 10.0.1.100
    10.0.1.100 dev tailscale0 table 52 src 100.78.212.33 uid 1000  // <-- 本來是 wt0 不是 tailscale0
    ```
    
- ts目前可以把哪些要去tailscale0的CIDR去建立白名單，但作者是反過來，需要黑名單，特定cidr不需要去tailscale0, 要去 wt0
- ts 用 table 52 作為路由表
    - `ip route show table 52` 可以看細節
- `ip rule show` 可以查看用哪些路由表
    
    - 分數越小表示優先越高
    
    ```Bash
    re4388 in 🌐 re4388-Swift-SF515-51T in ~
    ❯ ip route show table 52
    100.81.246.73 dev tailscale0
    100.100.100.100 dev tailscale0
    100.104.167.17 dev tailscale0
    
    re4388 in 🌐 re4388-Swift-SF515-51T in ~
    ❯ ip rule show
    0:	from all lookup local
    5210:	from all fwmark 0x80000/0xff0000 lookup main
    5230:	from all fwmark 0x80000/0xff0000 lookup default
    5250:	from all fwmark 0x80000/0xff0000 unreachable
    5270:	from all lookup 52              # <--- ts 的路由表, 優先權 5270
    32766:	from all lookup main
    32767:	from all lookup default
    ```
    
- 透過新增一個更高優先的路由規則
    - 往`10.0.0.0/16` 都看主路由表 (裡面就會通往作者要的 wt0)
    - `sudo ip rule add to 10.0.0.0/16 pref 5000 lookup main`
        
        - result
        
        ```Bash
        $ ip rule show          
        0:	from all lookup local
        5000:	from all to 10.0.0.0/16 lookup main   # <--- 新建的路由規則，更高的優先權
        5210:	from all fwmark 0x80000/0xff0000 lookup main
        5230:	from all fwmark 0x80000/0xff0000 lookup default
        5250:	from all fwmark 0x80000/0xff0000 unreachable
        5270:	from all lookup 52                    # <--- ts 的路由表, 優先權 5270 
        32766:	from all lookup main
        32767:	from all lookup default
        ```
        
- 因為這是暫時的網路設置，最好記起來且自動化 → 寫一個腳本是最適合的了!
    
    ```Bash
    #!/bin/bash
    
    # Function to add IP rules for specified CIDRs
    add() {
        echo "Adding IP rules..."
        sudo ip rule add to 10.0.0.0/16 pref 5000 lookup main
        # ... others if needed ...
    }
    
    # Function to remove IP rules based on preference numbers
    remove() {
        echo "Removing IP rules..."
        sudo ip rule del pref 5000
        # ... others if needed ...
    }
    
    # Check the first argument to determine which function to call
    case $1 in
        add)
            add
            ;;
        remove)
            remove
            ;;
        *)
            echo "Invalid argument: $1"
            echo "Usage: $0 add|remove"
            exit 1
            ;;
    esac
    ```
  

# Link
- [使用 Tailscale 輕鬆建立安全且私密的通道 - WellWells](https://wellstsai.com/post/tailscale/)
- [Category: VPN服務 - CRE0809's Blog](https://www.notion.so/Category-VPN-CRE0809-s-Blog-68fdde34fb004b3ab716f90e988c20f6?pvs=21)
- [(351) 接上期：TailScale子网路由配置，实现使用原生内网IP远程访问，异地组网更加优雅！TailScale Subnet Router使用教程！ - YouTube](https://www.youtube.com/watch?v=TSCFHzbaAco&t=75s)
- [Tailscale設定教學，免費內網穿透VPN · Ivon的部落格](https://www.notion.so/Tailscale-VPN-Ivon-266212f65f704fc7aa3efdaefa71f29c?pvs=21)
- [足夠簡單且易用的 VPN 工具 – Tailscale](https://www.notion.so/VPN-Tailscale-12fce8f5e908451dbbe9c8d762d8c1be?pvs=21)
- macOS 也可以用 deamon版 + cli
    - [macOS 上的 Tailscaled · tailscale/tailscale Wiki --- Tailscaled on macOS · tailscale/tailscale Wiki](https://github.com/tailscale/tailscale/wiki/Tailscaled-on-macOS)
    - [https://tailscale.com/kb/1080/cli?tab=macos](https://tailscale.com/kb/1080/cli?tab=macos)

  

  

# 衍生軟體
- [好用的開源內網穿透軟體比較，ZeroTier 與 Tailscale・Ivon 的部落格](https://ivonblog.com/posts/foss-nat-traversal-solutions/)
- [ZeroTier使用教學：建立虛擬區網的VPN軟體 · Ivon的部落格](https://www.notion.so/ZeroTier-VPN-Ivon-521557bb0b544a359d2d2955f4ecf55a?pvs=21)
- [架設自己的 Tailscale – Headscale](https://www.notion.so/Tailscale-Headscale-82105889c0554207a5276f2145eada3d?pvs=21)
- [ZeroTier・Ivon 的部落格](https://ivonblog.com/tags/zerotier/)
- [在 Linux 上使用 ZeroTier](https://blog.cre0809.com/archives/582/)
- [Tailscale 建立私有的中繼伺服器 – DERP](https://blog.cre0809.com/archives/721/)



  

## HN 討論  Clouflare vs Tailscale?
[Tailscale SSH is now Generally Available | Hacker News](https://news.ycombinator.com/item?id=40060901)

- Clouflare 隧道通常更多用於 http (s) 流量，而不是 SSH
- Tailscale 中的 http (s) 產品是 Tailscale Funnel，儘管它不完整，因為您無法將 BYO 網域連接到 TS Funnel。
	- Tailscale Funnel 允許用戶通過 Tailscale 的基礎設施將 HTTP (S) 流量引導到內部服務。這意味著，通過 Funnel，用戶可以從互聯網訪問在 Tailscale 網絡中的服務
	- 雖然 Tailscale Funnel 允許你將 HTTP (S) 流量引導到內部服務，但目前不支持將你自己的域名（BYO，即 "Bring Your Own"）連接到 Tailscale Funnel。這意味著你無法使用你自己擁有的域名來訪問這些服務，而只能使用 Tailscale 提供的域名或 URL。
- 本質上，CF 隧道 = Tailscale Funnel w/ BYOD + Tailscale SSH
- 另一個角度說，兩者也可以說是非常不同的
	- CF 隧道是您的服務的反向代理
	- Tailscale 管理對 VPN 內的電腦的身份驗證。
- 我的理解是，只有 Tailscale 是端對端加密的
	- 隧道和 TLS 均終止於 Cloudflare。不是端到端的。事實上，CF 會掃描流量以查找惡意軟體


# tailscale 不同 WireGuard 的是

- 使用 mesh 網路, 不是 輻射網路
- 支持 nat 打洞 （NAT Hole Punching） 然後直連 p2p (via UDP)
- p2p失敗， fallback to relay 中繼模式 via 協調 server [DERP（Detour Encrypted Routing Protocol）伺服器](https://tailscale.com/blog/how-tailscale-works/#encrypted-tcp-relays-derp)
- derp本來就會負責一開始的資訊交換，like ip, port, status等等
- 另外支援magicDNS(ip→domain轉換), tailDrop(airdrop), tailscaleFunnel(ngrok)
- 如果我有幾個 WG 對等設備（手機、筆記型電腦、平板電腦)…那我有必要安裝ts嗎?
    - 如果您必須管理許多用戶，並且想要與其他人共用節點，Tailscale 非常好。因為他具備：安裝簡單、SSO、點對點隧道（而在 VPN 存取伺服器中，伺服器可以看到隧道內部）、無開放埠、DNS、私有 dns 伺服器、在不同出口節點（或無出口節點）之間輕鬆切換、子網路路由器、ACL（如雲端防火牆）、從一台裝置傳送檔案到另一台裝置、每個裝置的 TLS 憑證、當 udp 不可用時回退到 TCP / 中繼。
    - 您是否曾經發現自己處於所有 NAT 穿越策略都失敗而唯一的方法是透過 DERP 伺服器的情況。除非你自架 DERP中，否則這是 Tailscale 可能有用的情況之一。
    - 缺點：
	    - 您必須在所有裝置上安裝以 root 身分執行的ts代理
	    - 在某種程度上信任協調伺服器。
    - Tailscale 非常適合以下情況：
	    - 您擁有大量漫遊設備，這些設備可能想要相互通信並與中央集線器通信，或者您想要將所有這些都控制在某種 SSO 後面。
    - 我看來，Tailscale 之所以出色，主要是因為它的 initial connection bit
        - 在其非常基礎的層面上，它是 WireGuard 的一個很好的包裝，幫你處理可能會停止您的初始連接的繁瑣事情。
- it’s like linux 中的 ubuntu, vpn 中的 dropbox

  

  

  
# How Tailscale works
- [https://tailscale.com/blog/how-tailscale-works](https://tailscale.com/blog/how-tailscale-works)
- 基於 wireguard [WireGuard → VPN](https://www.notion.so/WireGuard-VPN-5f40cb1b87464e3cb29e1483cc87a89e?pvs=21)
    - 中心輻射網路
        - 比較簡單的網路拓譜，不然你10個node, 每個都連，那就是90個通道，你需要管理這90個的ip, port, pubkey等
        - 缺點：如果你在ny, 但hub在加州，你要連到ny下面而已，你要先過跑到 hub再連回來XD
        - 解法？ multi-hub
        - 衍生的缺點？ → 加新server, 你需要讓其他 c 知道 s, 你加新的s, 每個c都要知道, 你有分配問題需要處理
    - 網格網路mesh
        - 大家都連在一起？那不是會有上面的90個通道問題?
        - intorduce 控制平面和資料平面
        - 控制平面
	        - 有一個協調 server 會需要收大家的 pub key and ip:port, 然後這個協調server 也會派發每個node所可以連到的 pubkey and ipPort等meta data
        - 資料平面
	        - 大家各自p2p, 更快，不需要route 去hub
        - 混合模式（集權只負責meta-data，分散讓資料處理，也沒有瓶頸問題）
        - 時序
            1. 每個node gen 自己的pri/pub key, pub key也和自己身分關連
            2. node 會傳自己的 domain, ip, port pubkey等資料給協調server
            3. node 下載(from 協調 server) 他需要連到其他node 的這些資料
            4. node 利用底層 wireguard, pubey可以開始和其他node 建立輕量隧道
    - 登入和授權?
        - tailscale 不直接處理
        - 協調server 透過 oath2, oidc(openId connect), saml協議 跟google, github 的 auth server 要到需要的使用者授權資料來進行登入和授權
        - 好處?
            - 不需要留存使用者的pii
            - good UX, 你一登入就拿到你授權的資料，馬上就建立你的網域可以讓你用了
    - NAT穿越?
        - 如何 穿越 每個機器fw and nat(fw)的問題 ?
        - via udp
        - 底層透過 TURN (可以讓你知道你的私有ip從網路看的ip和等等技術) and ICE (互動連線建立機制)
        - 看另一個文章 [How NAT traversal works](https://tailscale.com/blog/how-nat-traversal-works) or below note [[Tailscale_idx#How NAT traversal works]]
    - DERP: 加密TCP中繼
        - DERP (Designated Encrypted Relay for Packets)
        - 當你遇到一些網路連 udp都不允許時, 就用透過這個
        - 進入 derp到出去都是加密狀態
    - ACL 和安全策略
        - vpn 通常會和 fw, 安全策略（認證和授權）綁在一起
            - 因為你透過vpn連到某個地方(公司等)，通常要看是誰和可以連到哪些
            - 綁在一起也比較好設定，不會你用a家的vpn, 但你 auth&athor系統用另一家，不好設定
            - 另外 vpn 網路層的 fw 通常是綁ip → 不好管理，相對於based on role and group
            - 然後如果你有很多內部vpn節點，你又需要每個地方協調管理安全策略
        - tailscale作法
            - 協調 server 會把相關的acl 策略pass 給需要知道的node
            - 白名單機制
    - audit
        - 所有node的log會非同步傳到中央log
        - 源目node都會傳，所以會有兩份(可以比對，確認一致性，更安全)
        - 幾乎是即時的，因此想要竄改的時機也只有 個位數 ms, 相對安全。
    - 增量部屬
        - 公司如果要部屬，可以一台一台加入網路
        - 因為本質是 mesh “overlay” nerwork
            - 就是跟公司既有的網絡系統的平行存在的
        - 目標是 zero trust network （不太懂）

  
# How NAT traversal works
-> [[How NAT traversal works]]


skype 也用類似技術 -> [(592) How Skype scaled to 100 million users with ONLY 3 developers - YouTube](https://www.youtube.com/watch?v=idOsaKg8lPM)
- p2p, each pc is client and server at the same time
- use superNode as reply server (superNode 就是比較多頻寬的 client)
- need to puch hole to overcome NAT and firewall

