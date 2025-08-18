[[k3d]]

---
前幾天我們曾使用 deployment 部署服務，今天我們來看比較詳細一點的 spec 以及 deployment 中 strategy 的設定。  
在這之前稍微介紹一下 Replication Controller 跟 Replica Set。

# ReplicationController & ReplicaSet

從字面就可以知道這兩種 controller 是用來控制數量的。
**控制對象就是 Pod，可以確保 Pod 運行的數量與 spec 中指定的相同。**

ReplicaSet 則是比 Replication Controller 多了 selector，在控制 Pod 的數量上更加彈性。

可使用 `matchLabels` 及 `matchExpressions` 來定義要控制的 Pod 是哪些。

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend-replicaset
  labels:
    app: guestbook
    tier: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
		matchExpressions:
	    - { key: environment, operator: NotIn, values: [dev, test] }
		# matchExpressions:
		#  - key: environment
		#    operator: NotIn
		#    values:
		#      - dev
		#      - test
  template:
    metadata:
      labels:
        tier: frontend
				environment: staging
    spec: 
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
```

上面的範例類似 Deployment，會寫 Pod template 是什麼、selector 是什麼。

`matchExpressions` 則是有不同的 operator 可以使用：
`In`, `NotIn`, `Exists`, and `DoesNotExist` 

如果是 `In` &`NotIn` 後面的 `values` 不得為空。`matchExpressions` 兩種寫法都行。

那上面我們定義了 `matchLabels` & `matchExpressions` ，它們在這裡就會是 AND 的關係。Pod 必須都符合選擇的條件才會套用這個 ReplicaSet。

現在官方建議使用 Deployment 來控制 Pod。
**Deployment 相對於 ReplicaSet 是更高一層的概念，除了控制 Pod 數量以外還有提供了別的功能。**

# Deployment

Deployment 有哪些應用場景呢？

- Rollout new version of used image
    Deployment 能夠讓 Pod template 被更新時透過指定的策略來更新它管理的 Pod。更準確一點的說是，新的 ReplicaSet 會產生，進而產生新的 Pods。例如 Image 版本更新時就**不需要砍掉先前的 Deployment，而是能夠透過更改 deployment 的方式來 rollout new version**。
    
- Rollback
    可以 rollout 那自然也可以 rollback， Deployment spec 中可指定 `revisionHistoryLimit` ，預設是 10。kubernetes (具體來說我還不曉得這個 history 會儲存在哪 🥲) 會儲存過去版本的 ReplicaSets，讓你能夠 rollback 回去之前的 revision。設成 0 就無法 rollback 啦。
    
- Pause when updating Pod template
    另外 Deployment 還有個 paused 的參數可以下，讓你更改 Pod template 時先不要啟動 rollout。
    

從上面敘述可以發現 Deployment 可以讓我們更方便地做應用程式的換版。
而更新 Pod 時的策略有兩種：`RollingUpdate` & `Recreate` ，下面就來看下兩種策略的 demo。

基本的 Deployment 寫法可以參考
- [Day 4 利用 Deployment 及 Service 部署服務 - 1](https://ithelp.ithome.com.tw/articles/10322186)
- [Day 5 利用 Deployment 及 Service 部署服務 - 2](https://ithelp.ithome.com.tw/articles/10323111)

## RollingUpdate

RollingUpdate 是預設的 deploy 策略，其中還可以定義 `maxSurge` 、`maxUnavailable` 的參數。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-rolling
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 2       
      maxUnavailable: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx-container
          image: nginx:1.14.2
```

上面的 spec 我們使用 RollingUpdate 並且設置了兩個參數：  
`maxSurge` 指的是在**更新的時候可以比 replicas 數量還要多多少個 Pod**。這邊的數字可以是百分比也可以是絕對數字。  
`maxUnavailable` 則是指更新的時候，可能不能用的 Pod 數量。上面的數字為 1，則更新時不能使用的 Pod 為 1。

因此上面的例子表示在更新的時候：

- 最多會有 (3 desired pods + 2 maxSurge pods) = 5 個 Pods Ready
- 最少會有 (3 desired pods - 1 maxUnavailable pod) = 2 個 Pods Ready

預設的話則是 `maxSurge` = 25%、`maxUnavailable` = 25%。即更新期間最多會有 125% * replicas 的 Pods 可用、最少會有 75% * replicas 的 Pods 可用。

下面來實際跑跑看。跑完第一版的 deployment 後使用 `k set image deployment/deployment-rolling nginx-container=nginx:1.17` 更改 image version。  
![[IMG-k3d Deployment & Deploy Strategy-20241003104933953.png]]

可以看到有 1 個舊版本的 Pod 被砍掉，至少 2 個 Pod ready；新版本的 Pod 一次建立 3 個，後續換版最多有一瞬間會有 5 個 Pod Ready。

### Revision history

關於 revision history 則是可以透過以下指令來看：

```bash
kubectl rollout history deployment deployment-rolling
```

其中前兩個版本 CHANGE-CAUSE 是空的，因下指令時沒有放 `--reocrd` 這個 flag 或是使用 annotation `kubernetes.io/change-cause`  
![[IMG-k3d Deployment & Deploy Strategy-20241003104948865.png]]

如果使用下指令的方式，更新時可利用 `--record` 這個指令，但這個指令已經被 deprecated  
![[IMG-k3d Deployment & Deploy Strategy-20241003105006457.png]]

較好管理的做法還是用 declarative 的作法，更改 annotation，再執行 `kubectl apply -f <yaml file>`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-rolling
  annotations:
    kubernetes.io/change-cause: "change version to nginx:1.24"
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 2       
      maxUnavailable: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx-container
          image: nginx:1.24
```

### Rollback

如果更新版本發現出錯，可使用 rollback 指令先讓服務回到之前的版本。如果沒有下 `--to-revision` 則預設會是上一個版本。
可搭配 `k rollout history deployment/deployment-rolling` 使用。

```yaml
kubectl rollout undo deployment/deployment-rolling --to-revision=4
```

## Recreate

Recreate 從字面上來看就是重新建 pods。使用這個策略的話所有正在跑的 Pods 都會被砍掉，然後新的 Pods 才會建立。通常不建議正式環境使用 Recreate 的方式更新版本。不然就是能接受 downtime 的服務才會使用這個策略。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-recreate
spec:
  replicas: 3
  strategy: Recreate
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
	      - name: nginx-container
	        image: nginx:1.14.2
	        ports:
		        - containerPort: 80
```

執行之後來更新 image 版本

```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.16.1
```

![[IMG-k3d Deployment & Deploy Strategy-20241003105028561.png]]

可以觀察到 pods 一次性的 terminated 然後換成新版的 pods。

---

🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠🫠

_Reference_  
[https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/)  
[https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)  
[https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)  
[https://kubernetes.io/docs/concepts/workloads/controllers/deployment/](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)  
[https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/deployment-v1/](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/deployment-v1/)  
[https://www.bluematador.com/blog/kubernetes-deployments-rolling-update-configuration](https://www.bluematador.com/blog/kubernetes-deployments-rolling-update-configuration)  
[https://www.baeldung.com/linux/deployment-rollout-kubernetes](https://www.baeldung.com/linux/deployment-rollout-kubernetes)

