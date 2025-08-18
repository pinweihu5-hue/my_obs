
影片 see dropbox wemo_stff

# meeting note 2023-07-07

### Log System Administration Guide
- [https://docs.google.com/document/d/1d3hcYEJZmm-RlMe9hqKb1bHsrz9OBwr0jEdePCzTUj4/edit](https://docs.google.com/document/d/1d3hcYEJZmm-RlMe9hqKb1bHsrz9OBwr0jEdePCzTUj4/edit)



### 想要把 hermes db  shrink
[https://docs.google.com/document/d/17lSS6cI3h7p0u5puuraPsSOWZslvr9C7FPPa9f2Pq18/edit](https://docs.google.com/document/d/17lSS6cI3h7p0u5puuraPsSOWZslvr9C7FPPa9f2Pq18/edit)

1. another read replica -> then promote master node cannot, since read replica must same or bigger vol XD
2. another indep master -> become master/slave arch -> sync -> slave to master
    - need to go to that db instance, modify setting
    - but cloud sql, you can't go inside to do stuff
    - as a manager service, so you just cannot go inside db instance to modify as u will
        - you need to modify setting via provided sdk
    - you don't need to ssh into(aws experience)
    - but master/slave no need to go into, you can run sql to do that, but this is also depend on if gcp allow you can do it
    - google migration service -> a blog is about new db size is no need to have the same size
    - we can run experiement for pg9.6 in qat

### redis and cloudDB 升級

redis and cloudDB upgrade, any caveat?
- stop redis service and update and start service
- check if persistency
- < 30 min
- prod need to consider downtime
- downtime is easier, 30 min is fine(double write...more complicated...hard to mgmt)
- 4 -> 6 建新拆舊
- step
    - 建立 new_db
    - 把舊的資料 from old_db dump into new_db, like 前一天
    - 停機
    - 停機時，這這一天的其他資料也轉到 new_db
    - 這時候  new_db 和 old_db 的資料一樣了
    - 把流量切到 new db
    - 確認everything work (整合測試)
    - 上線

how to control traffic? service, cronjob,demon, all 關停 
security group change rules to not go to service deploy your service(new setting to redis) 
and then integration test -> go online


a new fireall rule -> target to redis all deny, priority set high

=

redis lock auth token
新站 下站 復站



#  meeting note 2023_06_08
- L4 is for ha, redirect traffic into later
- replace L4 load-balance + ingress -> gcp L7 load-balance
- currently, ingress will gen l7 load-balance
- how to migrate ceres from vm to k8s, ip part
- currently, ceres vm have a public ip and 3rd party `lock` this ip (meaning 3rd party allow this ip to hit them)
    - k8s traffic 流到外面是透過 cloudNAT, which 3 ips are public (3? is it fixed). So when we migrate (ceres VM to K8s), we need to align 3rd party to allow current cloudNAT(3 ips) and ip in old vm all be their 白名單 (allow to hit their service)
    - current ceres, reverse proxy to allow internal connection
        - say if we move ceres goes into k8s, how to setup?
            - k8s are all internal env, so we need to use cloud NAT, which for 流量由內往外
                - so if k8s service need to 流到 internet, need to config cloudNAT
            - 外往內的流量, config LB have active the internet ip
        - but, if we move ceres into k8s, we shall be able to follow the current way of service already in k8s
- grafana prometheus can we move back to gcp? or other good monitoring?
    - if no cost issue, datadog? 幾十萬, like cloudflare
    - gcp/amz, support not that good, cannot do customize
    - cheaper, 只要機器錢 and need to do most customization
- 單點失效 -> 收車子指令 iris
- peer 2 peer network
    - no need to go pubic if it's internal
    - all internal just go internal
    - a lot of effort
- kong, 2 vm
    - api gateway
    - 重疊 cdn 功能
        - 也可以選擇用 cdn replace it
        - 但是 cdn 還是是管理往外面的流量
        - 如果往內要有 route, 還是需要有一個機制去處理
        - but cdn is expensive solution
        - cdn, waf, api-gateway, 都會互相 cover 到一些功能
        - put a a cdn before kong?
            - ddos
            - many security
    - 0.x version, too old, many security issue
    - modify header
    - many rules on up stream, some ip goes into some upstream
    - some rate limit
    - possible to migrate to gcp-api-gateway
- GCP shall already update for you
    - docker -> containerd
    - cgroupv1 -> v2
- helm template -> yaml -> [https://github.com/doitintl/kube-no-trouble](https://github.com/doitintl/kube-no-trouble)
- gilab-runner need to update -> then can run on continuer-d
    - 一樣，建立新的，導過去，再sunset舊的


# meeting note 2023_06_09

## on l4 and l7 lb

on LB
- 因為不同 office 的內網沒有打通
    - 因此很多內部服務，還是需要繞到 internet 進來 via l4 external lb
    - so 很多內部服務的access還是要透過裡面的 nginx ingress 建立 white ip to block public access
- mqtt only can use l4 lb
- 管理進來的樓量，for better mgmt -> suggest to use l7 lb to replace l4lb + ingrss_nginx
- also, l7 can add ssl cert(L6)
    - l4 is only know ISO level4 below stuff
    - also l7 can provide WAF related stuff
    - who want to use l4? 
	    - 博奕 wana use l4 for its high perf, like million level traffic
- can l7 can replace Kong?
    - Kong as api gateway, 同性質 as lb
    - but lb and apigateway 側重的東西不同
- tcp/ip socket private protocol, can we use l7? maybe not


## 上 code

- what happened when you 上 code
    - you need to have a dockerfile, dockerize
    - need to have gitlab CI yaml, which need to have logic about.. test, build, deploy, you will call a deploy script. called [deploy.sh](http://deploy.sh/), this will call a http req with many param
    - the work is then at the gitops-helper,
    - this will update the argocd manifest
    - by go to argocd manifest repo, update its value and argocd detect it, and then sync (if it is auto sync)
    - argo will lint and and check against schema



## clusterIp vs nordPort, ingress class

- gcp k8s need to use nordPort, can't use clusterIp
    - but many helm chart need to use clusterIp
    - so, fine, we use clusterIp and add ingress class at front
    - => this the reason we use clusterIp
    - one of the limitation is need to use nordPort
    - k8s server 的 type review:
        - clusterip
            - use k8s port -> iptable
            - virutalIp -> trafffic forward to pod
            - only this cluter can use
        - nodeport
            - each worker node, have a hole
            - as long as out traffict can find this hole/port
            - 後面的 pod 都可以吃
        - lb
            - 比 node port 多了一層, 幫你加上外面的 lb, same fixed ip facing client
            - no need to worry about nodeport
- ingress-class object l7 lb
    - under the hood, gcp create l7 lb
    - limit you need to use nodeport in k8s
    - why cannot go to pod ip?
    - just need to use the same ip range, therotically, but gcp don't allow you use i
    - the reason is why we don't use l7 lb is cuz in the past we can't easily change to nodeport, need to many effort
        - (many old helm chart use cuslterIp, so we need to update manually, which affect our upgrade easilyness)



- augus vm (grafana) 隔離一台在外面，可以避免服務掛了，監控也掛了的問題
    - 如果放在裡面，你得處理網路打通的問題, 比較好收prometuthis
- 可以把 component 搬到 argoCd?
    - 目前只有 application argocd
    - 因此目前升級是比較麻煩一點
    - 這樣可以讓管理比較方便
        - 有gui, diff, sync, 會比較方便
    - 我們覺得因為沒有常常改，所以感覺不需要用argo?這是目前的思維
    - 但是如果是 api 改很多的情況，那還適合這樣放入 argoCD嗎
        - 這樣的好處也是可以減少手殘
- nice to have
    - 把辦公室 and gcp 變成內網 private zone
        - 好處：目前dns, 網路 很亂, → 好管理
        - dns from IONOS to gcp, 很痛
        - 內部服務才不會出去被人家知道 domain 叫什麼
        - 一個人和一個it, 建立新的，半年到一年，搬過去另外算
- before Kong, cdn, waf?
- all gcp use Terraform 管理
    - iaas
        - 不需要通靈
        - 也進入 ci/cd



# meeting note 2023_06_15

## ceres VM

- 前提提要 -> [[why ceres migrate to k8s]]
- 是否可以 add 主機，避免 spof (aka, HA), ip? vm use cloudNAT to connect? what else we need to think about?
	- if we only upgrade vm (but still SPOF)
	- relk:
	- when i left, ceres already have 2-3 vm, I guess it's cuz covid so need to cost down..so now, it's just 1 vm.
	- can vm use cloudNAT, yes, but u need to test, u need to use VM w/ private ip and cloudNAT for outbound traffic (but in the past, we have strange connection lost, don't know why)
		- but if not, we need to have a public ip to go out
		- cloud NAT is a simple IP change, internalIP->publicIP (via IP table maybe), 當初是不了了之。
		- reik -> don't know. unknow issue
	- if we add vm
		- make it private
		- go out via clouodNAT
		- ceres as a kong upstream -> hit direct to ceres
		- not sure may also need upstram to reverse proxy
		- kong and reverse proxy need to pt to new VM
		- make a machine-img to open a new vm? I like to open a clear one, add stuff, add doc, for new vm
		- 3rd service need to add witelist need use public ip
		- cloudNAT ips shall already in whilte list for our 3rd party? need to double confirm w/ 3rd party
		- why we need this extra layer of cloudNAT?
		- 目前是 directly link ceres vm, no go thru cloudNAT
		- ceres magic 1: sysctl -a to see if any setting
		- ceres magic 2: each ceres have a nginx in front of it, need to setup a nginx in front of node
- Ceres 新增 vm for HA:
	- 加一台 VM 會建議完全新開一台
	- CI/CD 在 gitLab 上 Ceres Project 設定環境變數，目標主機可以寫入多 IP, 用逗號分隔
	- 網路要給 Cloud NAT 的 private IP 區段，VM 不給 public IP 的話， 對外就會走 Cloud NAT 
		- 不建議把 10.140 網段加入 Cloud NAT 以避免預期外的行為發生
	- 需要盤點線上目前所有三方哪些有綁定 IP，綁定的 IP 是什麼應該要文件化
	- Kong 的 Upstream 要加入新的 VM IP 來 導 到 新 upstream VM
	- Check Ceres Revers Proxy 設定是否須要加入新的 VM IP (印象中 Hermes K8s 會走 Revers Proxy (Nginx)進 去 Ceres) 
	- Note:
		- YC 有調整一些設定，需要用 `sysctl -a` (見註)確認後依序在新的 VM 調整
		- 每個 Ceres 都有一個 Nginx，入口進去後才會打到服務上



- how to upgrade base-image
    - see hermes-api Dockerfile
    - go to sre/node.js project, see its dockerfile
    - this shall build a new img after the running the docker file
    - build it every 3 day (update cronjob)



- emqx internal usage
    - gcp dns overwrite
    - same server (emqx)
        - setup a new internal nginx class
        - external (public zone, 預設)
        - internal (gcp server visit -> resolve to internal ip, private zone 設定 內網 ip)
        - use dns to decide which goes which
        - 類似 私人 -> gcp 內才會知道的設定


- gke have cloud router
    - can we have ceres router -> instance group -> ceres vm, using k8s to autoscaling ceres vm in this way?
    - 流量外對內, need to have a lb
    - 流量內對外, cloudNAT



- fluentD upgrade
    - pubsnb-fluentd 收 box, 移動軌跡等等
    - fluent@monitoring
        - get log, but tag is not matching
        - use stateful set, it's a official one
        - volume setting => to put service account's secret
        - diff old and new helm chart
            - to know how to feed their value
        - see their release note
            - see what changes
        - basically hard work


- kb question
    - 需要乖乖升級
    - new dev-cluster, sandbox, 小的規格
    - helm 差太多版，無法直接砍，或是客製化，只能一個一個砍，但這樣也無法rollback了。












# 註
- NAT 掛 第三方廠商的白名單 ip
	- tappay - 後台就可以, 玉山 - 有申請, 設定好了, 藍新, 墨功, 悠遊付, 街口
	- NAT 是一種技術，主要用於將私有 IP 位址轉換為公共 IP 位址，反之亦然。透過 NAT，位於私有子網路中的虛擬機器可以安全地訪問互聯網，而不必直接暴露其私有 IP 位址。
	- Cloud NAT 的特點 自動擴展：Cloud NAT 可以根據需求自動擴展，以支持大量的出站連接。 簡單配置：用戶可以輕鬆地在 GCP 控制台中配置 Cloud NAT，而不需要管理底層的基礎設施。 高可用性：Cloud NAT 提供高可用性，確保 NAT 服務不會成為單一故障點。
- `sysctl -a` 是一個非常有用的命令，能夠幫助系統管理員快速查看和了解 Linux 系統的內核參數及其當前值
	```bash

kernel.ostype = Linux                # 顯示了操作系統的類型
kernel.version = 5.4.0-42-generic    # 顯示了內核的版本號 
net.ipv4.ip_forward = 0              # 控制 IPv4 的 IP 轉發功能, 0 is off, 1 is on
vm.swappiness = 60                   # 使用交換空間（swap space）傾向。`0`~`100`，數值越高，系統越傾向於使用交換空間
fs.file-max = 100000                 # 可以同時打開的最大文件數量

```


