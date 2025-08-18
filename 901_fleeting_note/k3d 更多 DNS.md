[[k3d]]

---
這邊大部分內容來自 [DNS for Services and Pods](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/) 。

前面介紹了 Service，Pod 可以向 Service 發請求，例如 `curl http://<service-name>` ，Service 會幫忙把流量轉到 target Pods；
也提到其實每個 Pod 都有自己的 IP，只是 Cluster 外部無法直接訪問 Pod IP。這一切是怎麼做到的呢？




先來看 kubernetes cluster networking 會需要處理什麼問題:
- Pod 之中，Container & Container 之間的溝通
- Pod 與 Pod 之間的溝通
- Pod 與 Service 的溝通
- 外部到 Service 的溝通


先說，Pod 是怎麼知道 Service 在哪的？  

Kubernetes 有提供兩種模式來讓 Pod 找到 Service。
一種是設置環境變數
一種是透過 DNS。

# 環境變數

當 Pod 被建立時，kubelet 會將現在存在的 service 相關的資訊設成環境變數。添加的規則會是 `{SVCNAME}_SERVICE_HOST` & `{SVCNAME}_SERVICE_PORT` ，這邊的 SVCNAME 會是 service name 大寫，並將 dash 轉成底線。
**但以這種方式來讓 Pod 連接到 Service 必須要讓 Service 在 Pod 建立之前先建立，不然 Pod 之中就不會有相關的環境變數。**

底下為官網例子，名為 `redis-primary` 的 service 被分到的 cluster IP 是 10.0.0.11，暴露 TCP port 6379，會建立以下的環境變數：

```bash
REDIS_PRIMARY_SERVICE_HOST=10.0.0.11
REDIS_PRIMARY_SERVICE_PORT=6379
REDIS_PRIMARY_PORT=tcp://10.0.0.11:6379
REDIS_PRIMARY_PORT_6379_TCP=tcp://10.0.0.11:6379
REDIS_PRIMARY_PORT_6379_TCP_PROTO=tcp
REDIS_PRIMARY_PORT_6379_TCP_PORT=6379
REDIS_PRIMARY_PORT_6379_TCP_ADDR=10.0.0.11
```

實測了一下在 Pod 中只會建立同個 namespace 的 service 相關環境變數，不同 namespace 的 service 似乎沒有。  

```sh

$ k apply -f simple_nginx.yaml
pod/nginx-simple-pod created

$)   13h
pod/php-apache-598b474864-tlc67   1/1     Running   1 (17m ago)   13h
pod/nginx-simple-pod              1/1     Running   0             8s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   22h
service/php-apache   ClusterIP   10.43.19.8   <none>        80/TCP    13h


$ k exec -it nginx-simple-pod -- /bin/sh
# ls


# env | grep SERVICE
PHP_APACHE_SERVICE_HOST=10.43.19.8
PHP_APACHE_SERVICE_PORT=80
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_HOST=10.43.0.1

```


可以看出這方法有些缺點，service & pod 建立順序會影響環境變數的建立、不同 namespace 會找不到 service。
**因此現在大部分的 Kubernetes 都會使用 DNS 的方式來讓 Pod 能找到 Service。**

# DNS

在建立 Kubernetes Cluster 時可以使用 add-on 來設置 DNS service。
**目前 Kubernetes ≥ 1.1 版本的 DNS 服務是使用 CoreDNS**。但也有別種 DNS 解決方案可選擇。

more on [coreDNS]([CoreDNS簡單除錯：解決你遇到的一般問題. 最近在部署一些解決方案時，碰到了關於名稱解析上的一些問題，雖然有時候不難解決，但… | by Albert Weng | Medium](https://weng-albert.medium.com/coredns%E7%B0%A1%E5%96%AE%E9%99%A4%E9%8C%AF-%E8%A7%A3%E6%B1%BA%E4%BD%A0%E9%81%87%E5%88%B0%E7%9A%84%E4%B8%80%E8%88%AC%E5%95%8F%E9%A1%8C-71d255e39548))

用 k3d 建立 cluster 時會幫忙裝好 CoreDNS，可以下指令來觀察看看 -  

k get deploy,pods,svc -n kube-system
![[IMG-k3d 更多 DNS-20241003104934010.png]]

重要組件會放在 `kube-system` 這個 namespace 中。可以看到 CoreDNS 是由 Pod + Service 所組成。
(這邊 service 叫 kube-dns 是因為過去的 k8s 版本是使用 kube-dns)

以下的內容前一天也提過：**當新的 Service 被建立時，DNS server 會建立相對應的 DNS records**。而這個 DNS records 會考慮到 Service 所在的 Namespace:   → `<service_name>.<namespace>.svc.<domain_name>`

對所有 Cluster 的 Pod 來說，Service 的 DNS name 可被解析並被 Pod 連接上。
kubelet 會設置 Pod 的 `/etc/resolv.conf` 檔案，讓 Pod 能夠透過 service name 來找到 service，不用透過 service IP。  
![[IMG-k3d 更多 DNS-20241003104948912.png]]

直接跑個 pod 進去裡面看，可以看到 `/etc/resolve.conf` 中的內容如下 -

```bash
search default.svc.cluster.local svc.cluster.local cluster.local
nameserver 10.43.0.10
options ndots:5
```

- `nameserver` 就是要做 DNS 解析的 server 
	- 可以發現這邊 `10.43.0.10` 是 kube-system 中 kube-dns service 的 Cluster IP。
	- 而 kube-dns 會連到 core DNS 的 Pod 去做 DNS resolving
- `search` 是域名的搜尋列表，使用 search 的配置可以讓 DNS query 時省略完全的域名 (FQDN)，只要提供主機名稱。在解析時會自動用 search list 中的 domain 來自動擴展，以找到正確的 IP。白話一點就是…… 當我用 service name `php-apache` 來做 DNS query 時，DNS server 會自動去找 `php-apache.default.svc.cluster.local` 這個位置對應的 IP，在同一個 Namespace 的 Pod 就不用打完整的域名才找得到 `php-apache` Service。
- `ndots` : 這邊是 5 個點的意思，表示查詢的域名中點`.`的個數。
	- 如果域名字串中點的數量比 5 還要小，則會按照 search 配置的內容，照順序去添加對應的後綴域名去查詢，直到解析出 IP 為止。
	- 如果加上所有 search 配置的後綴都無法解析，就照一開始的請求域名去解析。
    - 為什麼是 5 呢？因為 service 的完整域名是 `<service name>.<namespace>.svc.cluster.local` → 有四個點，所以小於 5 個點的域名要通過 search 配置的後綴去補
    - 再給例子，假設有另一個 namespace 是 `uat` ，要連到其中的 service `frontend`，那 Pod 請求的位置會寫為 `frontend.uat` ，在做 DNS 解析時判斷點的數量小於 5，就會用 search list 中的後綴依序添加去查找，等加上 `svc.cluster.local` 這個域名後就能解析到正確的 Service IP

# DNS records

除了 Services 以外，Pods 其實也會有 DNS records。

## Services

根據 Service 的 IP family (IPv4 or IPv6)，Services 會被加上 DNS A and/or AAAA records，
格式是 `<service name>.<namespace>.svc.<domain name>` ，對一般的 Service 來說這個 record 會被解析成 service 的 cluster IP。

Headless Services 不會有 Cluster IP，因此這類型的 Services 雖然也是會被加上 A or AAAA records，但解析出來的 IP 會是這個 Headless Services 目標連接的 Pods IP。

除了 DNS A/AAAA records，如果 port 有名稱的話還會有 SRV record。
格式會是 `_<port name>._<port protocol>.<service name>.<namespace>.svc.<domain name>`


例如建立以下的 service:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: test
spec:
  ports:
    - port: 6379
      name: tcp-6379
  selector:
    run: test
```

建立一個 nginx pod 使用 nslookup 查詢  
![[IMG-k3d 更多 DNS-20241003105009568.png]]

用途的話我還不確定我的理解是否正確，但假設一個 Service 開了 multi ports，其他的 client Pod 想連接這個 Service，就可以用到 SRV record 來使用特定的 port

## Pods

通常 Pods 會有以下的 A records -  
`<pod IP>.<namespace>.pod.cluster.local`

例如 default namespace 中的 Pod IP = 172.17.0.3  
DNS 名稱會是 `172-17-0-3.default.pod.cluster.local` ，這邊 IP 的點點會變成 dash

另外如果這個 Pod 被 Service 暴露，也可以透過以下的 DNS name 找到 -  
`<pod IP>.<svc name>.<namespace>.svc.cluster.local`

例如底下這個 pod 是由 deployment 控制，是 `php-apache` service target Pod

```sh

# check pod ip

$ k get pods
NAME                          READY   STATUS    RESTARTS      AGE
php-apache-598b474864-4r2bk   1/1     Running   1 (53m ago)   13h
php-apache-598b474864-tlc67   1/1     Running   1 (53m ago)   13h
nginx-simple-pod              1/1     Running   0             35m


$ k describe pod php-apache-598b474864-4r2bk | grep IP
IP:               10.42.0.20
IPs:
  IP:           10.42.0.20



# ssh into and use nslookup to check 
k exec -it nginx-simple-pod -- /bin/sh

apt update
apt install dnsutils -y

nslookup 10-42-0-20.php-apache.default.svc.cluster.local

Server:		10.43.0.10
Address:	10.43.0.10#53

Name:	10-42-0-20.php-apache.default.svc.cluster.local
Address: 10.42.0.20



```



### Pod’s DNS Policy

在建立 Pod 的時候也可以配置 DNS Policy，由 spec 中的 `dnsPolicy` 決定

類型包括
- Default
    - **非預設的 DNS Policy**，如果設成 `Default` ，Pod 的 DNS 解析配置會跟它所在的 Node 一樣
- ClusterFirst
    - **預設**，如果 DNS query 搭配上 cluster 後綴沒有辦法找到對應的 IP，例如 `www.kubernetes.io` 並非 cluster 內的 domain，這個 query 會轉給上游的 DNS server 做解析。通常這會是 Node 的 nameserver。
        
        可以看一下 CoreDNS 的 ConfigMap -  
        ![[IMG-k3d 更多 DNS-20241003105035926.png]]  
        forward 的部分即是轉發相關的設置 (但這個 `/etc/resolv.conf` 我還不確定是 host 的檔案還是某個 container 的檔案，因目前本機是以 k3d 的方式建置 cluster …)
        
- ClusterFirstWithHostNet
    - 如果是用 Host 的網路 (`hostNetwork: true`)，則 DNS Policy 需配置為 `ClusterFirstWithHostNet`
- None
    - 設成 None 的話，就會套用 `dnsConfig` 的配置



例如以下這個 Pod 套用 `dnsConfig`
```yaml hl:10-20
apiVersion: v1
kind: Pod
metadata:
  namespace: default
  name: dns-example
spec:
  containers:
    - name: test
      image: nginx
  dnsPolicy: "None"
  dnsConfig:
    nameservers:
      - 192.0.2.1 # this is an example
    searches:
      - ns1.svc.cluster-domain.example
      - my.dns.search.suffix
    options:
      - name: ndots
        value: "2"
      - name: edns0
```

進到 Pod 裡面看， `/etc/resolv.conf` 內的配置會與 spec 中設置的一樣  
![[IMG-k3d 更多 DNS-20241003105101587.png]]

---

### 名詞解釋

放一些我寫文章時查詢的名詞

#### FQDN
Fully Qualified Domain Name = 完整網域名稱，包含主機名稱 (Host Name) 及網域名稱 (Domain Name)。例如 [www.youtube.com](http://www.youtube.com/)，www 是主機名稱，youtube.com 是網域名稱。
- 點跟點之間不能超過 63 個字母
- FQDN 不能超過 255 個字母

#### A record
給定網域的 IPv4 位址

#### AAAA record
給定網域的 IPv6 位址

#### CName record
將某個域名指向另一個域名 (Not IP)  
例如 [ftp.example.com](http://ftp.example.com/) → example.com

#### SRV record
指向 IP 位址 & 連接埠 (port)

---

真的是寫到迷路，如果有任何怪異的部分請多多指教🥲

_Reference_  
[https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)  
[https://kubernetes.io/docs/concepts/services-networking/service/#discovering-services](https://kubernetes.io/docs/concepts/services-networking/service/#discovering-services)  
[https://kubernetes.io/docs/concepts/cluster-administration/networking/](https://kubernetes.io/docs/concepts/cluster-administration/networking/)  
[https://www.jianshu.com/p/57b8dc914bbc](https://www.jianshu.com/p/57b8dc914bbc)  
[https://www.reddit.com/r/kubernetes/comments/duj86x/help_understanding_how_dns_works_and_what_ndots/](https://www.reddit.com/r/kubernetes/comments/duj86x/help_understanding_how_dns_works_and_what_ndots/)  
[https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/)  
[https://itnext.io/kubernetes-hacks-and-tricks-5-pod-dns-policy-and-dns-config-options-c176f4a581c3](https://itnext.io/kubernetes-hacks-and-tricks-5-pod-dns-policy-and-dns-config-options-c176f4a581c3)

