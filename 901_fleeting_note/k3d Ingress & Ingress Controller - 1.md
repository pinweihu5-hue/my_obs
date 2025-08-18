[[k3d]]

---

前面提到可以使用 Node Port Service 讓外部 Client 能連接到 Cluster 內，
但這會需要使用 Node IP & port，
而 Node Port 是有固定數量的。

另一種將 Cluster 內部應用程式暴露給外界的方式可以透過 Ingress 及 Ingress Controller，連接 Service 讓外部 Client 能透過 domain name 訪問。

**設置好 Ingress 後，還需要 Ingress Controller 才能讓 Ingress 能夠使用。**

- Ingress 是 Kubernetes object
- Ingress 其中會定義路由的規則，例如設置 host name or paths，來指定如何將外部流量轉給 Cluster 內部的 Services (aka Service objects 後面接著 Pods)。
- Ingress Controller 的角色與 reverse proxy 類似，當外部流量要進來 k8s cluster 時，會先打到 Ingress Controller，Ingress Controller 再根據 Ingress 定義的規則去路由這些流量。  
![[IMG-k3d Ingress & Ingress Controller - 1-20241003104933963.png]]  
Source: [https://github.com/GoogleCloudPlatform/community/blob/master/archived/nginx-ingress-gke/index.md](https://github.com/GoogleCloudPlatform/community/blob/master/archived/nginx-ingress-gke/index.md)



**Ingress Controller 並不是 Kubernetes 預設會有的，要自己裝。**

不過市面上有很多選擇，我自己聽過的有 Traefik (k3d cluster 預設會裝)、NGINX Ingress Controller、Ingress-Nginx Controller (沒錯這兩個有一點點不一樣，前者是 NGINX 版本，後者是社群版本)、HAProxy ……。


---



以下就來實作。

首先在建 k3d cluster 時，先不要裝預設的 ingress controller (traefik)，等一下改為自己裝 Ingress-Nginx Controller。

```bash
k3d cluster create demo-ingress --k3s-arg="--disable=traefik@server:0"
```

註 1: 其實是可以有多個 Ingress Controller 的，但這邊為了學習比較好理解，先用一個 Ingress-Nginx Controller (社群版本)

註 2: 寫到一半才 (再次) 發現用 windows WSL & docker desktop 有很大的機率無法在本地訪問自定義的 domain，mac 也可能有問題但不確定。建議用 linux OS，最後面我改用 AWS EC2 Ubuntu 22.04 來實作

# Ingress

以下的 Ingress 接了兩個 Services，這兩個 service 接的 Pods 分別是兩個簡單的 hello page。

這邊的 annotations 是給 Ingress-Nginx Controller 讀的，不同的 Ingress Controller 在這邊的設定也會不同。
這邊的 `rewrite-target` 是指將請求的路徑重新定向為 `/` 。
```yaml hl:2,
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: webapp-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
    - host: simple.hello.com
      http:
        paths:
          - path: /dotnet
            pathType: Prefix
            backend:
              service:
                name: hello-world-app-svc
                port:
                  number: 5000
          - path: /express
            pathType: Prefix
            backend:
              service:
                name: hello-express-app-svc
                port:
                  number: 5000
```

這邊來看一下 spec

`apiVersion` 是 networking.k8s.io/v1  
`spec` 的部分 -
- `ingressClassName` : 看是要套用哪個 Ingress Class，沒寫會套用預設的，不過建議還是都寫清楚。這邊可以使用 `kubectl get ingressclass` 來看有什麼 ingress class
- `rules` : 路由的規則，是個 array，可以定義多個不同的 host name or path
    - `host` : 如果沒有設定，那就是指發給 Ingress Controller External IP 的請求；這邊指定 `simple.hello.com` 的話就是指發給這個位置的請求
    - `http` : 沒有別種 value，使用 Ingress 就是發 http request
        - `paths` : 可以定義多個 sub-path
            - `path` : 不指定就是指向 host
            - `pathType` : 可以是 `Prefix` or `Exact` or `ImplementationSpecific` 。這邊用 `Prefix` 表示會以 “/” 進行分隔的 URL 前綴去做 match。例如這邊定義 `/dotnet` ，則 `/dotnet` 、`/dotnet/` 、`/dotnet/test` 都會指向同一個 Service
            - `backend` : 定義後端接的 Service，這邊的 port 是 Service 開的 port

Path Type 可參考官網表格: [https://kubernetes.io/docs/concepts/services-networking/ingress/#examples](https://kubernetes.io/docs/concepts/services-networking/ingress/#examples)

Service & Deployment 設定檔 -

**hello-world-app**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-world-app-svc
spec:
  selector:
    name: hello-world-app
  ports:
    - protocol: TCP
      port: 5000
      targetPort: 5000
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world-app-deploy
spec:
  replicas: 1
  selector:
    matchLabels:
      name: hello-world-app
  template:
    metadata:
      name: hello-world-app
      labels:
        name: hello-world-app
    spec:
      containers:
        - name: hello-world-app
          image: chihhuiw/dotnet-helloworld:v1.0.7
          resources:
            requests:
              memory: 512Mi
              cpu: 500m
            limits:
              memory: 1Gi
              cpu: 1000m
          ports:
            - containerPort: 5000
```

**hello-express-app**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-express-app-svc
spec:
  selector:
    name: hello-express-app
  ports:
    - protocol: TCP
      port: 5000
      targetPort: 3000
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-express-app-deploy
spec:
  replicas: 1
  selector:
    matchLabels:
      name: hello-express-app
  template:
    metadata:
      name: hello-express-app
      labels:
        name: hello-express-app
    spec:
      containers:
        - name: hello-express-app
          image: chihhuiw/nodejs-helloworld
          resources:
            requests:
              memory: 512Mi
              cpu: 500m
            limits:
              memory: 1Gi
              cpu: 1000m
          ports:
            - containerPort: 3000
```

# Ingress Controller

安裝 Ingress Controller 的部分，這邊參考官網 [https://kubernetes.github.io/ingress-nginx/deploy/](https://kubernetes.github.io/ingress-nginx/deploy/)

使用一般的 yaml 來安裝

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.2/deploy/static/provider/cloud/deploy.yaml
```

可以看到建了滿多東西，相關的元件都建在 ingress-nginx 這個 namespace 中  
![[IMG-k3d Ingress & Ingress Controller - 1-20241003104948873.png]]

看一下 ingress-nginx namespace 中有什麼  

```sh

$ k get pods,svc -n ingress-nginx
NAME                                            READY   STATUS      RESTARTS   AGE
pod/ingress-nginx-admission-create-pjvw5        0/1     Completed   0          95s
pod/ingress-nginx-admission-patch-wprbb         0/1     Completed   1          95s
pod/ingress-nginx-controller-68fb8cf9cc-56drc   1/1     Running     0          95s

NAME                                         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/ingress-nginx-controller-admission   ClusterIP      10.43.134.220   <none>        443/TCP                      95s
service/ingress-nginx-controller             LoadBalancer   10.43.108.124   172.19.0.2    80:30895/TCP,443:30129/TCP   95s

```
可看到有個 LoadBalancer Service，這個 External-IP 就是外界要跟 Cluster 內部溝通的對象。
also see [[k3d service]]

然後從 workloads 也可以發現，其實 Ingress Controller 就是另一個 Pod，會是實際路由流量的 App。

前面 Ingress & apps 都建立好後來看一下長什麼樣子  

```sh


$ k describe ingress webapp-ingress
Name:             webapp-ingress
Labels:           <none>
Namespace:        default
Address:          172.19.0.2  <----------- 這個 ingress 跟 上面的 ingress-nginx-controller 的 external IP 一樣
Ingress Class:    nginx
Default backend:  <default>
Rules:
  Host              Path  Backends
  ----              ----  --------
  simple.hello.com
                    /dotnet    hello-world-app-svc:5000 (10.42.0.6:5000)
                    /express   hello-express-app-svc:5000 (10.42.0.5:3000)
Annotations:        nginx.ingress.kubernetes.io/rewrite-target: /
Events:
  Type    Reason  Age                    From                      Message
  ----    ------  ----                   ----                      -------
  Normal  Sync    8m16s (x2 over 9m16s)  nginx-ingress-controller  Scheduled for sync

```


![[IMG-k3d Ingress & Ingress Controller - 1-20241003105006890.png]]

可以看到 ingress address 的 IP 就跟 Ingress Controller SVC 的 External IP 一樣。

Default backend 是指如果不符合規則的請求過來會接到哪邊，沒有設置的話通常就是看 Ingress Controller 怎麼設定。不過你也可以自己設一個自己定義的 service 作為 default backend。

下面的 Rules 就是前面 spec 設定的，host name 以及各自接上對應的 service IP & port。

接下來因為在本機進行模擬
domain name 也是自定義的，
如果要透過 **simple.hello.com** 模擬訪問服務我們需要改一下本機的 `/etc/hosts` 檔案。

```sh

$ sudo su
root@n2-2024-06-14-ben:/home/re4388_103_0406# vi /etc/hosts

加上

172.19.0.2 simple.hello.com

```



更改後測試看看能不能連上 Ingress Controller Service 的 External IP


對於後端 Pod 來說，`/` 是有定義過的 Endpoint ，就會回覆 200 啦。  
```bash

~ ⌚ 7:55:46
$ curl simple.hello.com/dotnet
Hello World! xxxx%

~ ⌚ 7:57:33
$ curl simple.hello.com/express
Hello, Express!%

~ ⌚ 7:57:43
$ curl simple.hello.com/dotnet123
Hello World! xxxx%


```
這裡也可以測試 pathType = Prefix 時，有哪些路徑會被導引到定義的 service、哪些不會。

---

明天來實作幫 Database 加上 Ingress。

_Reference_
[https://kubernetes.io/docs/concepts/services-networking/ingress/](https://kubernetes.io/docs/concepts/services-networking/ingress/)  
[https://kubernetes.github.io/ingress-nginx/](https://kubernetes.github.io/ingress-nginx/)  
[https://kubernetes.io/docs/reference/kubernetes-api/service-resources/ingress-v1/](https://kubernetes.io/docs/reference/kubernetes-api/service-resources/ingress-v1/)


