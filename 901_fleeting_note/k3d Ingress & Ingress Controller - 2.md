[[k3d]]

---

Ben: 這篇我這就沒實作看看了，感覺作者這邊也沒有弄的很完整 XD


雖然 Ingress 只支援將 HTTP (S) request 路由到內部 Service，但 Ingres-Nginx 也可以接受外部的 TCP/UDP 請求並轉給內部 Service。今天就來實作看看。

主要設定檔參考官網：
https://kubernetes.github.io/ingress-nginx/user-guide/exposing-tcp-udp-services

今天用的環境一樣是 Ubuntu 22.04。

# DB configuration

以下是這篇建 MySQL 的設定檔，包含 secret、statefulset、headless service。
一樣為了方便就先建在 default namespace。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-h
spec:
  ports:
    - port: 3306
  selector:
    app: mysql
  clusterIP: None
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
data:
  MYSQL_ROOT_PASSWORD: eW91cnB3ZA==
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: mysql-h
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:8.1
          resources:
            requests:
              memory: 512Mi
              cpu: 500m
            limits:
              memory: 1Gi
              cpu: 1000m
          ports:
            - containerPort: 3306
          envFrom:
            - secretRef:
              name: mysql-secret
          volumeMounts:
            - name: mysql-vol
              mountPath: /var/lib/mysql
  volumeClaimTemplates:
    - metadata:
        name: mysql-vol
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi
```

Secret 的部分建議不要放到 code repository (github, gitlab …)。
但在部署的時候要怎麼處理這個 Secret 是另一個問題。恰好看到一篇 - [https://ithelp.ithome.com.tw/articles/10250620](https://ithelp.ithome.com.tw/articles/10250620) ，有興趣的人可以研究看看。~~怎麼用 Hashicorp Vault 處理 secret 的這個主題... 完賽前不曉得能不能研究到。~~

# Exposing TCP (UDP) Services

Ingress-Nginx Controller 如果要接收 TCP or UDP 的請求，安裝時需更改一些設定。

我們會需要建立一個 ConfigMap，其中要定義好使用哪個 port 以及對應的 service name & service 開的 port。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: tcp-services
  namespace: ingress-nginx
data:
  3306: "default/mysql-h:3306"
```

這邊是使用 port 3306 來暴露 default namespace 中的 mysql-h service (also listening to port 3306)。

接下來把 ingress-nginx 設定檔載下來 ([https://kubernetes.github.io/ingress-nginx/deploy/](https://kubernetes.github.io/ingress-nginx/deploy/))

```bash
curl -o ingress-nginx-deploy.yaml https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.2/deploy/static/provider/cloud/deploy.yaml
```

如果要使用 TCP/UDP，ingress-nginx controller 中的 LoadBalancer service 必須要暴露我們想用的 port。這邊在剛才建立的 `ingress-nginx-deploy.yaml` 之中，找到 LoadBalancer Service -

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app.kubernetes.io/component: controller
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
    app.kubernetes.io/version: 1.8.2
  name: ingress-nginx-controller
  namespace: ingress-nginx
spec:
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
	# ADD BELOW PORT
  - name: proxied-tcp-3306
	port: 3306
	protocol: TCP
	targetPort: 3306
  selector:
    app.kubernetes.io/component: controller
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/name: ingress-nginx
  type: LoadBalancer
```

這邊加上 `proxied-tcp-3306` 這個 port。

最後在跑 ingress-nginx deployment 時，args 需加上 `--tcp-services-configmap` 這個 flag。
在 `ingress-nginx-deploy.yaml` 之中，找 name: ingress-nginx-controller 的 Deployment -  
![[IMG-k3d Ingress & Ingress Controller - 2-20241003104933966.png]]

因內容太長了，只擷取 args 的部分

```yaml
# ..............
		spec:
      containers:
        - args:
            - /nginx-ingress-controller
						# --tcp-services-configmap=<namespace>/<configmap name>
						- --tcp-services-configmap=ingress-nginx/tcp-services
            - --publish-service=$(POD_NAMESPACE)/ingress-nginx-controller
            - --election-id=ingress-nginx-leader
            - --controller-class=k8s.io/ingress-nginx
            - --ingress-class=nginx
            - --configmap=$(POD_NAMESPACE)/ingress-nginx-controller
            - --validating-webhook=:8443
            - --validating-webhook-certificate=/usr/local/certificates/cert
            - --validating-webhook-key=/usr/local/certificates/key
# ..............
```

在跑 containers 需要跑的 argument 加上 `- --tcp-services-configmap=ingress-nginx/tcp-services` ，其中 `ingress-nginx` 是 configmap 所在的 namespace、`tcp-services` 是 configmap name。

最後記得加上 Ingress，將 request 導向前面建立的 mysql-h service

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mysql-ingress
  labels:
    name: mysql-ingress
spec:
	ingressClassName: nginx
  rules:
    - host: mysql.example.com
      http:
        paths:
          - path: /
            pathType: Exact
            backend:
              service:
                name: mysql-h
                port:
                  number: 3306
```

檔案都準備好了，就可以 apply 這些 yaml 啦。但 `tcp-configmap.yaml` 執行順序要在 `ingress-nginx-deploy.yaml` 後面，因在這之前沒有對應的 namespace。  
![[IMG-k3d Ingress & Ingress Controller - 2-20241003104948875.png]]

可以看到 LoadBalancer 開的 Port 比之前多了 3306 port。

再檢查一下 app & ingress 是否都順利被建立  
![[IMG-k3d Ingress & Ingress Controller - 2-20241003105007157.png]]

接下來我們要怎麼測試能不能連得上 mysql，而且使用我們前面設好的 root password 呢？

可以在本機安裝 MySQL，或是，我們跑一個 MySQL 的 Pod (or Docker) 來試試 - 原本是這樣想的但因為種種原因… 我只能先跑個 MySQL Pod 透過 LoadBalancer External IP 來連線 -  

k exec -it mysql-test --mysql -h 172.22.0.3 -uroot -p
![[IMG-k3d Ingress & Ingress Controller - 2-20241003105029709.png]]

這邊輸入的密碼是前面 secret 設定的密碼，成功進去 DB 內！

另外嘗試用 telent 的方式連線，在本機連接得到，但就… 無法驗證密碼  
![[IMG-k3d Ingress & Ingress Controller - 2-20241003105030087.png]]

---

有點鳥的結束，推測 MySQL image 的環境應該預設是無法進去 container 中編輯檔案。另一條路則是直接本地安裝 MySQL 也是能使用 MySQL command，但機器規格開太小……

但至少知道除了 HTTP (S) 外還是能透過 Ingress-Nginx 去接走 TCP protocol 的 services。這邊應該還可以測試怎麼真的用另一台主機連線，一樣待補 QQ。

_Reference_  
[https://kubernetes.github.io/ingress-nginx/user-guide/exposing-tcp-udp-services/](https://kubernetes.github.io/ingress-nginx/user-guide/exposing-tcp-udp-services/)