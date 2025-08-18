[[k3d]]


## deployment

[Day 4 利用 Deployment 及 Service 部署服務 - 1 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10322186)



假設今天服務的流量很大，我們會需要一些方式來讓 Pod 數量增加以支撐住流量，或是如果有的 Pod 不健康要有機制移除。Kubernetes 有提供各式控制器讓開發者使用。今天就先用 Deployment 來部署個登入註冊服務，然後接上 Service 讓我們能在本機用瀏覽器訪問。


[GitHub - re4388/simple-login](https://github.com/re4388/simple-login)
用這個 code (nodeJS and mysql) as app
這用 Deployment 來建置，並使用 Service 讓 Pod 之間可以溝通、外界可以訪問


Deployment 是什麼呢？  
在 Deployment 中，會定義好我們想要的狀態，包含要跑幾個 Pod、這些 Pod 裡面跑的 Container 用什麼 Image、Label 是什麼、更新 Pod 的策略。  當我們建了 Deployment，它會建立 ReplicaSet，然後再看需要多少 replicas 去建立相對應數量的 Pods。


先來看 web server deployment

```yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: ironman
  name: login-app-deploy
  labels:
    name: login-app-deploy
    app: prac-node-login
spec:
  replicas: 2
  selector:
    matchLabels:
      name: login-app-pod
      app: prac-node-login
  template:
    metadata:
      name: login-app-pod
      labels:
        name: login-app-pod
        app: prac-node-login
    spec:
      containers:
        - name: login-app
          image: chihhuiw/node-simple-login:1.0.5
          ports:
            - containerPort: 8000
          envFrom:
            - secretRef:
               name: login-app-secrets
      nodeSelector:
        role: "agent"


```


apiVersion : 建立 Deployment 使用的 apiVersion 是 apps/v1 ，跟 Pod 不一樣，之後建立不同的 object 時都要注意是用哪個 api。

kind : Deployment

metadata : 這邊有幫 Deployment 加上 label，可作為識別的用途。
指定 Namespace 為 ironman，在 Cluster 中可以利用 Namespace 來做資源區隔，Deployment 就是個 Namespace level object。那當然也有跨 Namespace，Cluster level object。

spec :
replicas : 指定這個 Deployment 希望能有多少個 Pod，預設會是 1。
(但如果還有使用 HorizontalPodAutoscaler 搭配 Deployment 使用，則不要設置 replicas。)

selector : 重要的部分之一，這邊利用 selector 來去定向說要產生的 Pod 是什麼。這邊會利用 label 定義的值去做篩選。

template : Pod template，要定義的東西跟 Pod 一模一樣除了 apiVersion & kind。如果數量小於 replicas number 的話，這邊的資訊會被 Deployment 用來建立新的 Pod。因此在 .spec.template.metadata.labels 的地方，labels 要跟 selector 符合才會作用。另外這裡的 Pod 重啟策略只會是 Always ，因為使用 Deployment 就是希望能夠自動控管 Pod 數量如我們預期，所以 Pod 如果死掉會重啟直到數量 = replicas。

image : 要使用的 Image。這是前述的 web server image，用 Dockerfile build 成 Image 後上傳到 docker hub

containerPort : 在我的程式中是讓 server 使用 8000 port，可以從 image layers 中找到是 Expose 哪個 port
(選擇 image tags 點進去就能看到當初怎麼 build 出來的 - https://hub.docker.com/layers/chihhuiw/node-simple-login/1.0.5/images/sha256-1b6e55f8f841fa05f4da9eeeb377a3b8c37dc09f57975db4f860fdcba0c922ec?context=explore)

envFrom : 跟 Docker 相同，可以掛環境變數進去給 Container 使用。在 Kubernetes 的世界中通常會用 Secret & ConfigMap 兩種 object 來掛環境變數。那如同字面意思，Secret 是給敏感資訊用的。之後我們會再建立 Secret 給這個 Container 使用。

secretRef 即是說從 Secret 中設定 env variables，底下的 name 會需要跟 Secret name 一致。

nodeSelector : 這邊我使用了 nodeSelector 來指定 Pod 要 schedule 到哪個 node 上，而我的 node 也有加上 role=agent 的 label 來讓 scheduler 知道要選哪個。


## Secret

在建立 deployment 之前先來建個 Secret。
Pod 所需要用到的環境變數如果包含敏感資料，可以利用 Secret 這個類型的 Object 傳進去。

template
app-secret.yaml.template
```yaml

apiVersion: v1
kind: Secret
metadata:
  name: env-secrets
type: Opaque
data:
  MYSQL_HOST: <db service name>
  MYSQL_DATABASE: 
  MYSQL_USER: 
  MYSQL_PASSWORD: 
  MYSQL_ROOT_PASSWORD: 
  SESSION_SECRET: 

```





```yaml


apiVersion: v1
kind: Secret
metadata:
  namespace: ironman
  name: login-app-secrets
	labels:
		name: login-app-secrets
    app: prac-node-login
type: Opaque
data:
  MYSQL_HOST: ZGItc2VydmljZQ==
  MYSQL_DATABASE: dGVzdA==
  MYSQL_USER: cm9vdA==
  MYSQL_PASSWORD: cm9vdHB3ZDEyMzQ=
  SESSION_SECRET: S3pqd21CNVFsNFkxR3lzRnIxMk9BRXc5Um5zcm5RTUI=

```

Secret spec 中有 `type` 的 field。預設其實就是 Opaque
但 Secret 還有很多種類型。Opaque 可以讓使用者定義要的 data 是什麼。  

`data` 是內容的部分。這邊都是以 key/value pair 的方式來訂變數。
如果使用 `data` ，則 value 的部分必須是 base64-encoded 字串，因 Kubernetes 在使用時會幫你做 base64 decode。
如果想要用明碼的話則是要用 `stringData` 。

base64 轉換可以用以下指令：

```bash
echo -n 'test' | base64
echo -n 'dGVzdA==' | base64 --decode
```

要注意的一點是這邊只做了編碼，並非加密喔！

這邊定義的 MySQL 連線相關資訊會跟另一個 MySQL deployment 對應，到時這兩個服務才能接起來。
SESSION_SECRET 則是因我的 web server 使用 Cookie-Session 的方式來做驗證，產生 session 所使用。




## Service

在 Cluster 中每個 Pod 其實都能跟別的 Pod 溝通，每個 Pod 都會有自己的 IP。但是！如果 Pod 死掉後重開一個，IP 就會改變。So..???

So we need service

在 Kubernetes 中，Service 是一個虛擬的概念，使用 Service 可以讓外界的流量傳給 Pod，或是做 Pod 之間的通信。

而 Service 要將流量傳給誰呢？在 configuration file 中會設定 selector 去指向特定的 Pods，也會設定要**如何**訪問 these pods。

```yaml


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

來看看 Service 的 spec 怎麼寫。

`type` : 可以是 `ClusterIP` 、`NodePort` 、`LoadBalancer` 、`ExternalName` 。 

NodePort 類型會在 所有 Node 上分配一個 Port 接收外界的流量，再傳給 Service，然後再傳給 Pods。


畫個圖示意 nodePort、port、targetPort 的關係：

![[IMG-k3d-nodejs app-20241003104934005.png]]


`selector` : 與 Deployment 的 selector 概念一樣，這個 Service 接收到的流量要傳給誰？利用 label 來篩選對象。這邊的 selector 就會跟 Deployment 中 template 定義的 metadata.labels 是一致的。

`ports` : 定義要用哪種 protocol、要開哪個 service port、targetPort。

`protocol` 預設是 TCP，但也支援 SCTP & UDP。

`port` 是 required field，要寫開哪個 service port 出去。

`targetPort` 是要連接的那個 Pod，Container 開的 Port。如果沒有寫的話預設會跟 `port` 一樣。那這邊因 web server 是使用 8000 port，所以這邊要定義好。

`nodePort` 是有範圍的，只接受 30000-32767，如果不指定的話就是由 control plane 從這個範圍隨機分配一個。Cluster 中每一個 Node 都會開這個 port 出來。




先建立 ns
```

kubectl create ns ironman


kubectl apply -f app-secret.yaml

kubectl apply -f app-deploy.yaml
kubectl apply -f app-service.yaml
```