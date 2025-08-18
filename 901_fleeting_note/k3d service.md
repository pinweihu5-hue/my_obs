[[k3d]]


pod will up and down, so we need to have a "service" (nodePort, lb, clusterIp) in front of them which have a static ip service use label selector to find pods

service use label selector to find pods


- k8s 的service
    - clusterIp
        - 只能用在內部互相連
    - nodePort
        - 可用在外部
    - lb
        - 會開gcp 的lb


- k8s 的 ingress
    - 官方的
    - 自己客製化的，我們用這個

---




Service 是個虛擬概念的元件，用途是讓 cluster 中的 Pod 能互相溝通，或是與 cluster 外部互相溝通。

那為什麼 Pod 跟 Pod 之間不能直接溝通，還要透過 Service? 
Pod 其實只要知道目標 Pod 的 IP，它們還是可以互相溝通的。但 Pod 如果不健康被砍掉，Pod 重啟之後 IP 是會改變的，這時就會出現問題了。

那該怎麼辨識同一個 Pod? Service 就能在這個時候派上用場。


Service 可以設定 selector，用於將流量導向帶有特定標籤的 Pod，這樣一來 Pod IP 改變還是可以透過 Service 來訪問同一個 Pod (或是說訪問某個服務)。

而在這邊 Service 也能夠做到 load balancing 的作用，將流量均勻分布到多個帶有相同標籤的 Pod 上，提供服務的可擴展性跟穩定性。



Service 分成幾個種類：
1. ClusterIP
2. NodePort
3. Loadbalancer
4. External

ClusterIP & NodePort 的部分就讓我再拿 Day 4 & Day 5 的登入網站來作例子。

# ClusterIP

**如果建立了 Service，control plane 會分配給 service 一個 cluster IP，這是 cluster 內部網路中的虛擬 IP，只能在內部被訪問。**
Cluster 中的所有 Pod 都能訪問這個 Service。
如果要讓外部訪問這個 service 可以再接 Ingress 或是 Gateway。

```yaml hl:2,10,11
apiVersion: v1
kind: Service
metadata:
  namespace: ironman
  name: db-service
  labels:
    name: db-service
    app: prac-node-login
spec:
  type: ClusterIP
  selector:
    name: db-pod
    app: prac-node-login
  ports:
    - name: mysql-port
	  protocol: TCP
      port: 3306
      targetPort: 3306
```

spec 中 `type` 若不寫則預設就是 ClusterIP。

`selector` 需指定要將流量導向有哪些 label 的 Pod。如果沒寫 selector 就會是另外的用途。

`ports` 可設置要開的 port 的資訊，可以開不只一個 port。
- `name` - 如果只開一個 port，name 可以省略；但如果一個 service 開多個 port，需要幫 port 取名來讓它們不被混淆
- `protocol` - 預設是 TCP
- `port` 是 Service 開的 port，為必要資訊。
- `targetPort` 是目標 Pod 的 port，可以是數字 (1 - 65535) 或是名字。如果是名字，那會對應到 Pod spec 中定義的 port name。如果沒有指定 targetPort，則會預設跟 service port 一樣數字。

上面的範例就是替 MySQL Pod 接上 ClusterIP Service，提供給其他的 Pod (Backend Server) 連接。
可見 [Day 5 利用 Deployment 及 Service 部署服務 - 2](https://ithelp.ithome.com.tw/articles/10323111)。

# NodePort

**前面的 Cluster IP 類型只能在 Cluster 內部存取，Node Port 則是將 Pod (application) 暴露給外界的一種方式。**
使用 NodePort Service 會讓 Cluster 中的每個 Node 都開放同樣的 port (`NodePort`) 讓外界連接。
外界可以透過 :<`NodePort`> 訪問 Service，請求會被轉給對應的 Pod。

```yaml hl:2,10,18,11
apiVersion: v1
kind: Service
metadata:
  namespace: ironman
  name: login-app-service
  labels:
    name: login-app-service
    app: prac-node-login
spec:
  type: NodePort
  selector:
    name: login-app-pod
    app: prac-node-login
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 8000
      nodePort: 30001
```

這邊比 ClusterIP 的類型多了 `nodePort` 的設定。
`nodePort` 必須介於 30000 - 32767 之間。如果不指定，Kubernetes 會隨機分配 30000 - 32767 其中一個 port 出來。
**要注意用過的 Node Port 就不能再重複使用。所以如果使用 Node Port 來暴露服務，能使用的 port 是有限的，並且管理上需要記錄哪個服務使用了哪個 port。再來還有個缺點是如果透過 Node IP 來訪問服務，Node 掛掉了話就無法使用服務啦。**  
-> 因此多用於 debug or test purpose

這邊範例是將 web server 利用 Nord Port 開給外界連接，可見 [Day 4 利用 Deployment 及 Service 部署服務 - 1](https://ithelp.ithome.com.tw/articles/10322186)。

# LoadBalancer

另一種將 service 暴露給外界流量的方式是使用外界的 load balancer。

**Kuberrnetes 本身並沒有提供 load balancing 這樣的元件，如果要用要自己建一個。**

如果是使用託管的 Kubernetes (雲端服務)，在建立 type 為 LoadBalancer 的 service 時，供應商會提供一個 External Load Balancer，外界可透過訪問這個 Load Balancer 來使用服務。

而 LoadBalancer Service 背後也是基於 Node Port 的方式來實現的。它也是會為每個 Node 開放一個 port，透過 Load Balancer (雲端供應商提供的那個) 將流量導向這些 Node。至於流量怎麼分配就看 Cloud Provider 的設置。

```yaml hl:2,6,11
apiVersion: v1
kind: Service
metadata:
  name: example-service
spec:
  type: LoadBalancer
  selector:
    app: example
  ports:
    - port: 8765
      targetPort: 9376
```

related: [[k3d Ingress & Ingress Controller - 1]]

# ExternalName

這個類型的 service 用途跟前面幾種比較不同，**這是讓 Cluster 內的 Pod 能夠透過 service 去訪問外部的服務**。
當 Pod 向這個 Service 發出請求時，cluster DNS 服務會回應 `my.database.example.com` 這個 CNAME record。 Service 會將請求轉給外部的 DNS name。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: prod
spec:
  type: ExternalName
  externalName: my.database.example.com
```

看到這邊很自然會有一個疑問：那為什麼 Pod 需要訪問外部服務時不直接發請求過去，還要透過 Service? 
目前想到的應該是將 Pod (app) 與外部服務的位置解耦，如果外部服務的位置更改可以改 Service 就好，不需要到每個 Pod 去改這個外部服務的 IP 或域名。

# Endpoints & EndpointSlice

前面提到 service 需要定義 selector，也就是目標 Pod 是誰。
在講沒有用 Selector 的 Service 之前，先了解一下 Endpoints & EndpointSlice。

在建立 Kubernetes Service 時 (先假設是 cluster IP)，kubernetes 會為 service 分配一個 cluster IP 以及 spec 中定義要用的 port。
有 Selector 的情況下，同時也會自動建立 Endpoints & EndpointSlice 這兩種元件。
**根據 selector，kubernetes 會去找相對應的 Pod，並把 Pod IP 以及 Pod 開的 port (就是要訪問這個 Pod 使用的資訊) 存在 Endpoints 中**。

不過 Endpoints 中能夠存的資訊是有限的，如果這個 Service 要接超過 1000 個 endpoints (也就是 1000 個 Pods)，後面的資料會被截斷。另外如果 endpoints 更新 - 例如 Pod 死掉重啟、自動擴展 Pod 數量增加 - Endpoints object 也要更新。而這對 cluster 來說可能會造成負擔，因為 control plane, nodes, api server 之間要不斷溝通更新這個 object。

**而 EndpointSlice 是較新推出的 API，這個元件中也會儲存 target Pod 的 IP & port。它最多能存 100 個 endpoints，如果超過的話會建立新的 EndpointSlice objects 來儲存 Pod 的訊息。因此在 target Pods 非常多的情況下，擴展及 Pod 資訊的更新上就不用更新整個 Endpoints object，可以指更新相對應的 EndpointSlice 就好。**

來觀察一下 -  
php-apache 這個 service，會接到 php-apache deployment 產生出來的 pods。現在有兩個 pods。  
![[IMG-k3d service-20241003104934000.png]]

list Endpoints & EndpointSlice，可以看到各自有對應 php-apache service 的 Objects 產生。 
endpoints 對應到了兩個 IP 位置，這兩個 IP 位置就是 Pod 的 IP。  
![[IMG-k3d service-20241003104948905.png]]

使用 describe 也可以看到其中儲存的 target Pod 的資訊。  
Endpoints  
![[IMG-k3d service-20241003105009088.png]]  
EndpointSlice  
![[IMG-k3d service-20241003105035639.png]]

不過這邊我還是有個疑問，看起來 EndpointSlice 是可以取代 Endpoint 的，不過建立 Service 還是會兩種 object 都有。如果砍掉 Pod，新長出的 Pod IP 改變，這兩個 Object 內儲存的資訊也的確都有更新。

# Service without Selector

再來看沒有 Selector 的 Service。
**如果沒有 Selector，EndpointSlice 就不會自動被建立。**
**這時你可以手動建立 EndpointSlice，指定要將流量導向哪個 IP。**

```yaml hl:12,19,27
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
---
apiVersion: discovery.k8s.io/v1
kind: EndpointSlice
metadata:
  name: my-service-1 # by convention, use the name of the Service
                     # as a prefix for the name of the EndpointSlice
  labels:
    # You should set the "kubernetes.io/service-name" label.
    # Set its value to match the name of the Service
    kubernetes.io/service-name: my-service
addressType: IPv4
ports:
  - name: '' # empty because port 9376 is not assigned as a well-known
             # port (by IANA)
    appProtocol: http
    protocol: TCP
    port: 9376
endpoints:
  - addresses:
      - "10.4.5.6"
  - addresses:
      - "10.1.2.3"
```

使用情境的話官網有舉幾個例子：
- 生產環境跟測試環境使用的 DB 不同
- 希望指向別的 Namespace 或別的 Cluster 中的 service
- 有部分服務搬到 Kubernetes 上了但有些還沒

# Multi Port

最後放個 multi port 的範例檔：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: 9376
    - name: https
      protocol: TCP
      port: 443
      targetPort: 9377
```

這個 service 能接 HTTP 以及 HTTPS 的 request。
或是之後會提到的 ingress controller (Ingress-Nginx Controller)，其中的 LoadBalancer service 也可開多個 port，支援不同協議：

```yaml hl:2,5,18,13
apiVersion: v1
kind: Service
metadata:
  namespace: ingress-nginx
  name: ingress-nginx-controller
  labels:
    app.kubernetes.io/component: controller
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
    app.kubernetes.io/version: 1.8.1
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ipFamilies:
    - IPv4
  ipFamilyPolicy: SingleStack
  ports:
    - appProtocol: http
      name: http
      port: 80
      protocol: TCP
      targetPort: http
    - appProtocol: https
      name: https
      port: 443
      protocol: TCP
      targetPort: https
    - name: tcp-5432
      port: 5432
      protocol: TCP
      targetPort: 5432
  selector:
    app.kubernetes.io/component: controller
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/name: ingress-nginx
```

還有一種 Headless service，如果 service spec 中 `clusterIP` 等於 `None` 就會是 Headless service。這跟 Stateful Set 一起提。

---

Kubernetes 到底是怎麼讓 Pod 知道這個 service name, or pod IP 要接去哪、能夠使用 Service、那些 routing 規則是什麼 …… 希望三十天內能讀懂然後寫出來。

_Reference_  
[https://blog.csdn.net/yang75108/article/details/101268208](https://blog.csdn.net/yang75108/article/details/101268208)  
[https://godleon.github.io/blog/Kubernetes/k8s-Service-Overview/](https://godleon.github.io/blog/Kubernetes/k8s-Service-Overview/)  
[https://www.f5points.com.tw/2022/09/15/kubernetes - 網路入門 /](https://www.f5points.com.tw/2022/09/15/kubernetes-%E7%B6%B2%E8%B7%AF%E5%85%A5%E9%96%80/)  
[https://stackoverflow.com/questions/34443138/difference-between-nodeport-and-loadbalancer](https://stackoverflow.com/questions/34443138/difference-between-nodeport-and-loadbalancer)  
[https://stackoverflow.com/questions/65887993/when-to-choose-loadbalancer-over-nodeport-service-typeor-vice-versa-in-kub](https://stackoverflow.com/questions/65887993/when-to-choose-loadbalancer-over-nodeport-service-typeor-vice-versa-in-kub)  
[https://kubernetes.io/docs/concepts/services-networking/service](https://kubernetes.io/docs/concepts/services-networking/service)  
[https://kubernetes.io/docs/concepts/services-networking/endpoint-slices](https://kubernetes.io/docs/concepts/services-networking/endpoint-slices)  
[https://cloud.tencent.com/developer/article/2169959](https://cloud.tencent.com/developer/article/2169959)