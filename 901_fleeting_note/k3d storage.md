[[k3d]]

---



當 Pod (App) 在運行時是需要保存狀態的，就需要考慮資料保存的問題。例如資料庫要保存寫入的資料，總不能 Pod 關掉後這些資料就不見。另外有時可能會需要多個 Container 使用同一份檔案。這篇會提一些跟 Storage 相關的元件：Volume、Persistent Volume、Persistent Volume Claim & Storage Class。

# Volumes

直接從 Pod spec 來理解好了，前面幾篇文章應該也有用過

```yaml hl:14,11
apiVersion: v1
kind: Pod
metadata:
	name: random-number-generator
spec:
	containers:
	- image: alpine
		name: alpine
		command: ["/bin/sh","-c"]
		args: ["shuf -i 0-100 -n 1 >> /opt/number.out;"]
		volumeMounts:
		- mountPath: /opt # directory in the container
			name: data-volume # what volume to be mounted
	volumes:
	- name: data-volume # the name of the volume
		hostPath:
			path: /data
			type: Directory
```

volumes 可分為 ephemeral volumes & persistent volumes。如字面意思，一種是暫時性的儲存，一種是持久性的儲存。
當 Pod 消失的時候，ephemeral volumes 也會跟著被砍掉，但 persistent volumes 會留著，可以讓重啟後的 Pod 繼續使用。

上面的 spec 中，Pod level 可定義不同的 volumes，可以定義多個 volumes。
**在 Container level，可以將外邊定義好的 volumes 掛載進 Container 中。**
這邊就是定義了 `data-volume` 這個 volume，它的種類是 host directory，實際位置位於宿主機的 `/data` 位置。然後在 `volumeMounts` 這邊定義說要把 `data-volume` 這個 volume 掛載到 Container 中的 `/opt` 位置。





## Types of volumes

Kubernetes 支持多個種類的 volumes，不過要注意版本，有些 volume type 可能不被支援了。

下面列幾個目前我自己練習有使用過的 -

### configMap

ConfigMap 前面提到可以設定 key-value，作為設置檔、環境變數定義給 Pod 使用。
使用方式可以利用 Volume 將 ConfigMap 掛進 Container 中，變成 Container direcotry 中的一個檔案。

例如：

```yaml hl:10,13,15
apiVersion: v1
kind: Pod
metadata:
  name: configmap-pod
spec:
  containers:
    - name: test
      image: busybox:1.28
      command: ['sh', '-c', 'echo "The app is running!" && tail -f /dev/null']
      volumeMounts:
        - name: config-vol
          mountPath: /etc/config
  volumes:
    - name: config-vol
      configMap:
        name: log-config
        items:
          - key: log_level
            path: log_level
```

在 test container 中，進去 `/etc/config` 這個位置，可看到 `log_level` 這個檔案 (檔名或是說 path 來自於 `items.path` )
其中的內容會是 ConfigMap 中 key = log_level 對應的 value。

這邊要注意在使用上必須要先建立 ConfigMap，才能在 Volume 使用它。
以及 ConfigMap 掛進去會是 `readOnly` 。

之前的文章：[Day 9 ConfigMap & Secret](https://ithelp.ithome.com.tw/articles/10326439)

### emptyDir

如同名字，這個類型的 volume 一開始會是空的
當 Pod 被放到某個 Node 上運行時 emptyDir volume 會被建立，當 Pod 還在這個 Node 上運行時會一直存在。但是 Pod 如果被刪除，emptyDir 中的資料也會被刪除。
不過如果只是 Container Crash，Pod 沒有被移除時 emptyDir 不會被影響。

```yaml hl:9,12,14
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: registry.k8s.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir:
      sizeLimit: 500Mi
```

之前的文章：[Day 8 Multi Container & Init Container](https://ithelp.ithome.com.tw/articles/10325484)

### hostPath

這種類型的 volume 是將 host node 的檔案或目錄掛進 Pod 之中，不過正式環境不太會這樣使用。

使用 hostPath 還能指定不同的 `type` ，例如:
- `Directory`
- `DirectoryOrCreate`
- `File`
- `FileOrCreate`

使用上會有一些事項要注意，像是不同的 Node 可能文件跟目錄跟想像中的不同，因 Pod 有可能被分配到不同節點上，有可能會導致不一致。
另外要寫入文件需要 root 權限，這時必須讓 Container 運行時使用 root 或是更改檔案的權限讓非 root 用戶能更改這個檔案。

```yaml hl:9,12,16
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: registry.k8s.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      path: /data       # directory location on host 
      type: Directory   # this field is optional
```

不過在本機上練習還是使用 hostPath 比較好觀察。

### persistentVolumeClaim

**這應該是目前主流使用的方式。**
Pod 指定使用哪一個 PersistentVolumeClaim。
PersistentVolumeClaim 會與 PersistentVolume 綁定
PersistentVolume 背後可能是 Google 的 Persistent Disk 或是別種儲存系統，Pod 這邊不需要知道細節

# CSI - Container Storage Interface

這邊只提一點點概念，檔案儲存有多種類型，Kubernetes 原生有支持一些類型。但這些設置是寫在 Kubernetes 核心程式碼之中的。也就是說假設有什麼新的儲存系統要加進去，會需要更新 Kubernetes 的 source code。要使用也必須更新 Kubernetes 版本。

Out-of-Tree (程式碼邏輯不在 Kubernetes source code 之中) 套件則可以讓第三方的 storage vendors 去發展他們自己的程式碼，不用跟 Kubernetes 綁在一起。但這邊需要有一個標準化的介面 - CSI，讓各式的儲存系統能夠讓容器編排系統 (Container Orchestration Systems, e.g. Kubernetes, Swam) 能夠使用。

# PersistentVolume

Persistent Volume 是 Cluster level 的 object
如名稱所表示，這是個持久性的 Volume。
可以想成這是一個先預先配置好的 Volume，等 Pod 需要使用的時候透過 PersistentVolumeClaim就能使用 

下面 PersistentVolume 會以 PV 簡稱、PVC 則是指 PersistentVolumeClaim。

PV 與 Pod 的生命週期是各自獨立的，如果 Pod 死掉，PV 還是會存在。

PV 的建立可以分成動態及靜態兩種。
靜態的，也就是 Cluster administrator 來建立一些 PV，讓 Cluster user 可以使用。

一樣使用 yaml -
```yaml hl:2
apiVersion: v1
kind: PersistentVolume
metadata:
	name: pv
spec:
	accessModes:
		- ReadWriteOnce
	capacity:
		storage: 1Gi
	persistentVolumeReclaimPolicy: Retain
	hostPath:
		path: /tmp/data
```

上面有些關鍵字，先來看 `accessModes`，這是定義這個 volume 在 host 中是以什麼樣的方式掛載，直接看可能的 value 比較好懂

- ReadOnlyMany
    - 這個 volume 是 read-only，並且能夠被多個 nodes 所使用
- ReadWriteOnce
    - 這個 volume 可以被單一個 node 進行讀取及寫入
    - 這個類型可以允許在同一個 node 上跑的多個 Pods 去存取這個 Volume
- ReadWriteMany
    - 這個 volume 可以被多個 node 進行讀取及寫入
- ReadWriteOncePod
    - 比較新的 feature，能夠在 CSI volumes 及 k8s 版本 1.22 以上使用。
    - 這個 volume 只能被單一個 pod 讀寫。如果你希望整個 cluster 中只有一個 Pod 能夠使用這個 volume 可以使用這個類型

*沒有驗證過，不確定多個 nodes 使用及多個 Pods 使用的情形

官網上有列出各式不同的 Plugin 支援的 Access Modes - [https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)

下一個比較特殊的關鍵字：`persistentVolumeReclaimPolicy`  
這是指 **與 PV binding 的 PVC 被刪除** 的時候，PV 中的資料該怎麼處理。

有可能有 3 種值 -
- Retain
    - 預設的 `persistentVolumeReclaimPolicy`
    - 如果 PVC 被刪掉，**PV 不會被刪掉但 not available，需要管理者手動處理這個 PV**
    - 因 PV 中還會留著之前的資料，所以看是要直接刪除這個 PV 或是手動清理之前的資料
    - 但如果想要重新使用，會需要用新的 PV
- Delete
    - 如果 PVC 被刪掉，PV 也會被刪掉
- Recycle
    - 已經被 deprecated，如果想要重新使用建議用動態配置的方式 (e.g. StorageClass)

整理一下不同的 Reclaim Policy，PV 使用本機的 direcotry，刪除 PVC 的話 -

| persistentVolumeReclaimPolicy | PV                  | mounted directory |
| ----------------------------- | ------------------- | ----------------- |
| Retain                        | 不會被刪                | 不變                |
| Delete                        | 跟著被刪                | 不變                |
| Recycle                       | 不會被刪，Status=Release | directory 內的檔案被刪掉 |

# PersistentVolumeClaim

PVC 用來跟 PV bind 的 Object。
這樣的設計可以提供更靈活、可管理和可擴展的儲存管理解決方案，也**讓儲存這塊與應用程式解藕**。

使用上會是
- 由管理者建立一堆 PV
- 使用者建立 PVC 來使用這些 PV
- PVC & PV 會是 1-1 binding

先來看 spec -

```yaml hl:2
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 8Gi
  storageClassName: slow
  selector:
    matchLabels:
      release: "stable"
    matchExpressions:
      - {key: environment, operator: In, values: [dev]}
```

如果是以上的 PVC，**在 Pod 使用 PVC 時，Kubernetes 會去找符合 spec 的 PV**，例如
- Sufficient Capacity
- Access Modes
- Storage Class
- Volume Modes
- Selector

**如果沒有配對到符合的 PV，PVC 會處在 Pending 的狀態**。
或是說，如果上層的 Storage Class 定義說 volume binding mode = WaitForFirstConsumer，那 PVC 會等到被某個 Pod 使用時才跟 PV binding。

另外補充一下如果 PVC 不指定 `storageClassName` ，在 k3d cluster 中會有預設的 storage class， `storageClassName = local-path` ，這會跟預設的 StorageClass object 的設置相關。([https://kubernetes.io/docs/concepts/storage/persistent-volumes/#class-1](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#class-1))

**Pod 如果要使用，PVC 必須跟 Pod 在同一個 Namespace** → PVC 是 namespace level objects。

```yaml hl:9,12,14
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
```

# Storage Class

**Storage Class 這個 object 可以用來動態配置 PersistentVolume**，就不需要手動配置了，在管理上會方便許多。

Storage Class 可以定義不同的後端儲存配置，例如不同的 Cloud provider、不同的儲存設備。  
![[IMG-k3d storage-20241003104948907.png]]  
(來源: [https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/))

使用上是

- 先建立好 StorageClass
- 建立 PVC 時需定義好 `storageClassName`
- Pod 使用 PVC
- 接下來就可以看到 PV 被 SC 建立好了

spec 如下 -

```yaml hl:2
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: google-storage
provisioner: kubernetes.io/gce-pd
parameters:
	type: pd-standard # [ pd-standard | pd-ssd ]
	replication-type: none # [ none | regional-pd ]
reclaimPolicy: Retain
volumeBindingMode: WaitForFirstConsumer
```

- `provisioner` : 定義說要用哪種 volume plugin，一定要填的 field
- `parameters` : 根據不同的 `provisioner` 會有不同的配置
- `reclaimPolicy` : 預設會是 Delete，另外一個可配置的值是 Retain，這邊對應到 PV 的 `persistentVolumeReclaimPolicy`
- `volumeBindingMode` : 這邊的 volume binding mode 設成 `WaitForFirstConsumer`，**Kubernetes 不會馬上為 PVC 建立 PV，而是等到 Pod 使用相對應的 PVC，才建立 PersistentVolume 並綁定 PVC & PV**。另一個可能的值為 `Immediate`

**底下是小疑問**  
如果 Storage Class provisioner 設為 local，不支援動態配置 PV。
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```

但是在本機 k3d 實驗

- 可以手動建立 PV，並指定 StorageClassName，例如 prac
- 手動建立 PVC，並指定 StorageClassName = prac
- 建立 Pod 使用前述的 PVC
- PV & PVC 能夠正確綁定，但在這些步驟前不需要自己建立 prac 這個 Storage Class，列出 sc 也不會看到 prac 這個 Storage Class 🤔

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: prac-pv
spec:
  storageClassName: prac
  accessModes:
    - ReadWriteOnce
  hostPath:
    path:
      "/tmp/k8s_storage_test"
    type: "DirectoryOrCreate"
  capacity:
    storage: 500Mi
  persistentVolumeReclaimPolicy: Retain
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: prac-pvc
  namespace: prac
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: prac
  resources:
    requests:
      storage: 100Mi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: prac
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      name: nginx-pod
  template:
    metadata:
      name: nginx-pod
      labels:
        name: nginx-pod
    spec:
      containers:
        - name: nginx-container
          image: nginx
          volumeMounts:
            - name: mount-prac-pv
              mountPath: /test
      volumes:
        - name: mount-prac-pv
          persistentVolumeClaim:
            claimName: prac-pvc
```

[[IMG-k3d storage-20241003104934002.png|Open: CleanShot 2024-06-16 at 10.39.30.png]]
![[IMG-k3d storage-20241003104934002.png]]

(須先建立 prac namespace)  
![[IMG-k3d storage-20241003105009209.png]]

最後總結一下 -

- PVC 需與 Pod 同一個 namespace 才能使用
- PV & StorageClass 則是 Cluster resource
    - End User 通常不會看到 PV & StorageClass 的設定
    - PVC 中需定義好要用哪個 StorageClass 以及設定 Resource Request → 使用者設定這個就好

---

_Reference_  
[https://kubernetes.io/docs/concepts/storage/volumes/](https://kubernetes.io/docs/concepts/storage/volumes/)  
[https://kubernetes.io/docs/concepts/storage/persistent-volumes/](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)  
[https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/)  
[https://kubernetes.io/docs/tasks/administer-cluster/change-pv-reclaim-policy/](https://kubernetes.io/docs/tasks/administer-cluster/change-pv-reclaim-policy/)  
[https://kubernetes.io/docs/concepts/storage/storage-classes/](https://kubernetes.io/docs/concepts/storage/storage-classes/)  
[https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)