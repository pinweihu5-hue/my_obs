[[k3d]]

---


[Day 7 Resource - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10324682)


control plane 的 scheduler 會負責將 Pod 安排到合適的 Node 上。

Scheduler 考量的點有以下幾個：
- Resource Requirements and Limits
- Taints and Tolerations
- Node Selectors/Affinity

# Resource Requests and Limits for Containers in Pod

其中一個考慮的因素是這個 Pod 需要多少資源 、Node 還有沒有足夠的資源能運行這個 Pod。
如果沒有任何一個 Node 有足夠資源能運行這個 Pod，這個 Pod 就會處於 Pending 的狀態。

那 Pod 需要多少資源？可以在 spec 中的 `resource` 定義：

```yaml hl:9-15
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
    - name: app
      image: images.my-company.example/app:v4
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m"
    - name: log-aggregator
      image: images.my-company.example/log-aggregator:v6
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m"
```

在這邊的資源是指什麼呢？在這裡最常指定的資源是 CPU & Memory
Resource 的使用是以 Container 為單位。

其中 Resources 又分成 Requests & Limits：

- Request 是 container 運行時要求的**最少** cpu 或 memory
- Limit 則是 container **最多**可以使用的 cpu 或 memory。
- 而一個 container 是有可能會使用超過它一開始要求的資源，但不能超過 resource limits。

**CPU 單位**  
Kubernetes 中 1 cpu 等於實體機器的 1 CPU 核心，或是 虛擬機器的 1 核心 (virtual core, vCPU)。
例如 1 AWS vCPU、1 GCP Core。可以定義小於 1 的數字，例如 `0.5`，就會是 `1` CPU 的 0.5，或是可以使用 `500m` 來表示。
m 在這邊是 milliCPU，但設置上不能小於 `1m` （千方之一的 cpu）。

- **1 CPU** = 1000 milliCPU
- **0.1 CPU** = 100 milliCPU
- **0.5 CPU** = 500 milliCPU
- **0.05 CPU** = 50 milliCPU


**Memory 單位**  
Memory 單位為 `bytes` 。可以用十進位制跟二進位制的方式表示。

|1Gi|1 Gibibyte|1,073,741,824 bytes|
|---|---|---|
|1Mi|1 Mebibyte|1,048,576 bytes|
|1Ki|1 Kibibyte|1,024 bytes|
|1G|1 Gigabyte|1,000,000,000 bytes|
|1M|1 Megabyte|1,000,000 bytes|
|1k|1 Kilobyte|1,000 bytes|

(不知道為什麼 1KB 是 1k … 請見 [https://kubernetes.io/docs/reference/kubernetes-api/common-definitions/quantity](https://kubernetes.io/docs/reference/kubernetes-api/common-definitions/quantity))

**不過要注意這邊單位是 `byte`** 
如果寫 `400m` 會變成是 `0.4` bytes， `400M` or `400Mi` 才是 400 MB (MiB)。

利用 `kubectl top pod <pod name>` 可以看到現在 pod 資源使用的情況  
![[IMG-k3d Resource-20241103201701117.png]]

**如果沒有設置 requests，但有設置 limits 會怎麼樣呢？**  
→ CPU & Memory 的 requests 就會等於 limits

**如果沒有設置 limits 會怎麼樣呢？**

- CPU: Container 在使用上就沒有限制，Container 能夠使用 Node 上所有的 CPU 資源。
- **Memory: Container 能使用 Node 的所有記憶體。這時有可能會發生 OOM - Out of Memory，會啟動 OOM Killer 殺掉 Process 來釋放空間，沒有資源使用上限的 Container 會有較高的機會被殺掉。**

註：Container 有較高的機會被殺掉但不一定會殺掉這個 Container，這與 OOM Killer - Linux kernel 的機制相關，會有某種規則來算 OOM score。查資料時也看到了個有趣的問題：如果 OOK Killer 殺掉的 Process 不是 Container 中 `PID=1` 的 Process? 這樣 Container 還活著嗎？

通常 limits 會設置大於 requests 的數字，這代表有需要的時候 Pod 可使用較多的 CPU / Memory，例如某段時間有較大的流量。

**Pod Scheduling**  
CPU/Memory 的 Requests & Limits 是 Container level，對 Pod 來說這些資源的 Requests & Limits 就是所有 Container 的 Requests & Limits **的加總**。

**而 Scheduler 在分配 Pod 到 Node 上的時候是看 Requests 的值，如果 Node 上的資源足夠提供 Pod 需要的 CPU & Memory，Pod 才會被調度到這個 Node 運行。**

不過每次寫 deployment、寫 pod spec 都要寫 resource limits & requests，是不是有點煩呢？Kubernetes 提供了另一種 object - LimitRange，可以讓建立的 Pod 都套用預先定義好的 requests & limits。

# LimitRange

LimitRange 是 Namespaced level object。
可以對 Namespace 中的 Pod 或是 PersistentVolumeClaim 做資源限制。

- 限制 Pod 或 Container 可以用的最小及最大的計算資源 (CPU & Memory)
- 限制 Request & Limit 的比例
- 設置預設的計算資源給 Container
- 限制 PersistentVolumeClaim 最小及最大的 storage request

PersistentVolumeClaim 是跟 Storage 相關，後面會再介紹。

這邊來看 LimitRange 的 spec -

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: limit-test
spec:
  limits:
    - type: Container
      max:
        cpu: 2000m
        memory: 1024Mi
      min:
        cpu: 10m
        memory: 128Mi
      maxLimitRequestRatio:
        cpu: 5
        memory: 5
      default:
        cpu: 1000m
        memory: 512Mi
      defaultRequest:
        cpu: 500m
        memory: 256Mi
```

- type: Container 表示對 Pod 中的 Container 做限制
- max - Pod 中 Container 的 Limits 上限 → limit 如果大於 max 會無法建立
- min - Pod 中 Container 的 Requests 下限 → request 如果小於 min 會無法建立
- default - Pod 中 Container 沒有指定 Limits 時，limits = default
- defaultRequest - Pod 中 Container 沒有指定 Requests 時，requests = defaultRequest
- maxLimitRequestRatio - 規定 Pod 中的容器設置 Limits & Requests 的比例的值不能超過 maxLimitRequestRatio 的值 ⇒ `Limits / Requests ≤ maxLimitRequestRatio`；例如 maxLimitRequestRatio = 2，表示 Limits 最多只能是 Requests 的兩倍

前四個參數的大小關係應該類似下面的關係：  
min — defaultRequest — default — max

官網上有個有趣的例子，request value 介於 max & min 之間是被允許的，但要注意 request 還是不能大於 limit。

例如定義 LimitRange 如下 -

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-resource-constraint
spec:
  limits:
    - default: # this section defines default limits
        cpu: 500m
      defaultRequest: # this section defines default requests
        cpu: 500m
      max: # max and min define the limit range
        cpu: "1"
      min:
        cpu: 100m
      type: Container
```

然後建一個 Pod，只寫說 cpu request = 700m

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-conflict-with-limitrange-cpu
spec:
  containers:
    - name: demo
      image: registry.k8s.io/pause:2.0
      resources:
        requests:
          cpu: 700m
```

request 700m 比 max 1 cpu 還小，但還是會得到錯誤訊息 :

```bash
Pod "example-conflict-with-limitrange-cpu" is invalid: spec.containers[0].resources.requests: Invalid value: "700m": must be less than or equal to cpu limit
```

因這時 limit 預設是 500m，request 必須小於或等於 cpu limit

這個情況下必須也定義好 limit 至少等於 request 才能成功建立這個 Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-no-conflict-with-limitrange-cpu
spec:
  containers:
    - name: demo
      image: registry.k8s.io/pause:2.0
      resources:
        requests:
          cpu: 700m
        limits:
          cpu: 700m
```

# ResourceQuota

ResourceQuota 這個 object 則是對整個 Namespace 的資源做限制。
Cluster administrator 可以利用 ResourceQuota 來管理各 Namespace 中資源的配額。
這邊的資源除了 compute resource，也可以限制儲存空間 (storage request)、Object 數量 (e.g. deployments 數量、secrets 數量)。

例如：

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: mem-cpu-demo
spec:
  hard:
    requests.cpu: "1" # 對這個 namespace 的 Pod, total request 的 cpu 不能超過 1
    requests.memory: 1Gi
    limits.cpu: "2" # 對這個 namespace 的 Pod, total limit cpu 不能超過 2
    limits.memory: 2Gi
```

上面的 ResourceQuota 指的是整個 Namespace 中運行的 Pod，加總的 requests memory 不能超過 1Gi；加總的 limits memory 不能超過 2Gi。

**要注意的是，如果一個 Namespace 中利用 ResourceQuota 定義了 cpu request，則後續建立的 Pod (Container) 就必須要寫明 cpu request 是多少，不然會無法建立**。

同樣的 `requests.memory` 、 `limits.cpu`、`limits.memory` 也是。
這時就可以搭配 `LimitRange` 使用。



假設 Pod 沒有定義好 ResourceQuota 限制的項目，錯誤訊息會長這樣 -

```bash
Error from server (Forbidden): error when creating "podafterrq.yaml": pods "rq-pod-2" is forbidden: failed quota: mem-cpu-demo: must specify limits.cpu for: nginx
```

list ResourceQuota 可以看到現在這個 namespace 中 resource 的使用狀況

![[IMG-k3d Resource-20241103201701197.png]]

---

以上單純介紹 Resource 相關的配置。
但沒提到 Kubernetes 中怎麼做調度。例如容器其實是有分等級的，有些容器在資源不夠的時候會比較容易被殺掉。

**最後記得在寫 spec 時把 resource requests & limits 定義好，才不會讓 container 無限制的使用 Node 資源。**

如果有任何錯誤或問題也麻煩指教！

_Reference_  
[https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)  
[https://kubernetes.io/docs/tasks/configure-pod-container/assign-cpu-resource/](https://kubernetes.io/docs/tasks/configure-pod-container/assign-cpu-resource/)  
[https://kubernetes.io/docs/tasks/configure-pod-container/assign-memory-resource/](https://kubernetes.io/docs/tasks/configure-pod-container/assign-memory-resource/)  
[https://kubernetes.io/docs/concepts/policy/limit-range/](https://kubernetes.io/docs/concepts/policy/limit-range/)  
[https://kubernetes.io/docs/concepts/policy/resource-quotas/](https://kubernetes.io/docs/concepts/policy/resource-quotas/)  
[https://kubernetes.io/docs/tasks/administer-cluster/quota-api-object/](https://kubernetes.io/docs/tasks/administer-cluster/quota-api-object/)  
[http://www.mydlq.club/article/36/](http://www.mydlq.club/article/36/)

