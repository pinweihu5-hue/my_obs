[[k3d]]

---

今天來看 StatefulSet 以及跟其搭配的 Headless Service。

# StatefulSet

StatefulSet 可以用來管理有狀態的應用程式。
StatefulSet 類似於 Deployment，可以指定要多少個 Pod (replicas)，先直接跑個 StatefulSet 來觀察 -

```yaml hl:2
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql-statefulset
spec:
  volumeClaimTemplates:
    - metadata:
        name: mysql-vol
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi
  replicas: 3
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
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: your-root-password
          volumeMounts:
            - name: mysql-vol
              mountPath: /var/lib/mysql
  podManagementPolicy: OrderedReady  
  serviceName: mysql-h
```

volume 的部分後面再提，先用 `kubectl get` 看看  

```sh



$ k get all
NAME                              READY   STATUS    RESTARTS       AGE
pod/php-apache-598b474864-4r2bk   1/1     Running   1 (162m ago)   15h
pod/php-apache-598b474864-tlc67   1/1     Running   1 (162m ago)   15h
pod/nginx-simple-pod              1/1     Running   0              144m
pod/mysql-statefulset-0           1/1     Running   0              77s  // <-- statefulset 的 name 帶有 idx
pod/mysql-statefulset-1           1/1     Running   0              59s
pod/mysql-statefulset-2           1/1     Running   0              47s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   24h
service/php-apache   ClusterIP   10.43.19.8   <none>        80/TCP    15h

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/php-apache   2/2     2            2           15h

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/php-apache-598b474864   2         2         2       15h

NAME                                 READY   AGE
statefulset.apps/mysql-statefulset   3/3     77s

NAME                                                  REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/hpa-utilization   Deployment/php-apache   0%/60%    2         10        2          15h


```

上圖同時還有 deployment 可以互相對照。Deployment 控制的 replicaset & pod，可以發現它們的名字是帶有隨機字串的。這邊比較下來可以觀察到 StatefulSet 的特性 -

- StatefulSet 控制的 Pod，名字是有 index 在的
- Pods 建立是照順序的，當第一個 Pod 建立成功，才會建第二個 Pod；終止也會是照順序的，從數字大的開始關

順序的部分可以看 `podManagementPolicy` 的配置，預設會是 `OrderedReady` ，也可以設置 `Parallel` ，這樣啟動跟終止都會是所有 Pod 一起。

更新策略的部分也類似 Deployment，設置在 `.spec.updateStrategy.type` 這邊 -

- 預設是 `RollingUpdate` ，如果更新的話，StatefulSet controller 會砍掉一個 Pod 然後一次更新一個 Pod，順序會與 Termination 順序相同。更細部的設定這邊先略過，可看官網 [https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#rolling-updates](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#rolling-updates)
- 另一種是 `OnDelete` ，如果更新 StatefulSet，系統不會自動更新 Pods，使用者必須手動刪除 Pods，系統才會建立新的 Pods

`volumeClaimTemplates` 
則是讓 Kubernetes 為每個 Pod 建立 PersistentVolumeClaim，這些 PVC 名字會從這個 StatefulSet derived，並加上 unique number。如果沒有設置 Storage Class 則套用 default 的，以本地 k3d 來說，default Storage Class 就會為這個 PVC 建立對應的 PV。

使用 `kubectl get pv,pvc,sc` 觀察 -  

![[IMG-k3d StatefulSet & Headless Service-20241003104933990.png]]

可以看到有相對應的 PVC 被建立，名稱是 `<given volumeClaimTemplates name>-<statefulset name>-<index>` ，如果 Pod 被刪掉重啟，還是會掛上同一個 Pod。

先觀察 `mysql-vol-mysql-statefulset-0`  
![[IMG-k3d StatefulSet & Headless Service-20241003104948895.png]]

刪掉其中一個 Pod  
![[IMG-k3d StatefulSet & Headless Service-20241003105008380.png]]

再看一下重新被建立的 Pod 是不是 binding 到同樣的 PVC  
![[IMG-k3d StatefulSet & Headless Service-20241003105008509.png]]

(Describe 的訊息有刪掉部分)

可以看到 Volumes 還是使用同一個 PVC

# Headless Service

如果將 Service spec 中的 `clusterIP` 設成 None，則這個 Service 就是 Headless Service。

Headless Service 的特性：
- 沒有 Cluster IP
- 不需負載平衡
- 搭配 Selector，接上 Headless Service 的 StatefulSet 的 Pods  會各自有一個 DNS record
	- DNS record foramt: `<pod_name>.<headless-service_name>.<namespace>.svc.<cluster-domain>` 
    - 使用 StatefulSet 會自動將 Pod Name 設置成 host name，Headless Service Name 設置成 subdomain name，所以在 Statefulset spec section 需定義好 serviceName (headless service name) 才會有這些 DNS records
	- 前面提過如果使用 StatefulSet 建立的 Pod 它的名字並不會因為重啟而改變，這時利用這個 DNS record 就能正確地與特定 Pod 連接，不用擔心名字改變、IP 改變
	- 對這個 Service Domain Name 的請求 (`<headless-service name>.<namespace>.svc.<cluster-domain>` ) 還是會發給對應的後端 Pods
    - 目前不確定轉發的機制，是否平均分配 (round-robin)



接續前面段落的例子，建立一個 headless service 給 mysql app 使用
```yaml hl:2,10
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
```



當我們建立好 `mysql-h` headless service 後，也在 StatefulSet 中定義要接的 serviceName，架構圖可以畫成 -  
![[IMG-k3d StatefulSet & Headless Service-20241003105033831.png]]

Client Pod 可以透過 headless-service 得到 backend Pod 的位置，Client Pod 也可以直接透過 DNS name 去跟特定的 Pod 連接。

這邊也建一個 busybox 的 Pod

```bash
kubectl run busybox --image=busybox --command -- /bin/sh -c "while true; do sleep 3600; done;"
```

進去裡面跑 nslookup 看看 -  
![[IMG-k3d StatefulSet & Headless Service-20241003105034686.png]]

可以看到向 `mysql-h.default.svc.cluster.local` 會得到所有 pods 的 IP

最後，到底什麼時候會用到呢？  
放上另一張示意圖：  
![[IMG-k3d StatefulSet & Headless Service-20241003105100881.png]]  
* 綠色三角形跟藍色三角形都是 Service

假設使用 DB 時設置三台，並假設是讀取量 > 寫入量的情況，配置 1 台 Master 2 台 Slaves。

- 讀取可透過一般的 service 讀取 (三台 DB 都能提供讀取功能)
- 寫入只對 Master 做寫入
- Slaves 可以透過 Pod name & Headless Service name 知道 Master 在哪邊，不用擔心 Master IP 改變

不過具體來說 DB cluster 要怎麼設置還沒研究，概念上的話則是可以很好地體現為什麼需要 StatefulSet + Headless Service 這樣的元件來建構 DB

---

最後的示意圖，不曉得讀取是不是也透過 headless service 讀取就好，目前實測只會連到同一個 Pod ……

_Reference_  
[https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)  
[https://kubernetes.io/docs/concepts/services-networking/service/#headless-services](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services)  
[https://stackoverflow.com/questions/52707840/what-is-a-headless-service-what-does-it-do-accomplish-and-what-are-some-legiti](https://stackoverflow.com/questions/52707840/what-is-a-headless-service-what-does-it-do-accomplish-and-what-are-some-legiti)  
圖片來自 - [https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/)