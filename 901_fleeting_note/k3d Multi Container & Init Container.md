[[k3d]]

---
[Day 8 Multi Container & Init Container - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10325484)


通常一個 Pod 內只會有一個應用程式在運行，這也比較有利管理及規模的控制。

不過有的應用程式較為複雜，例如可能需要先做些前置作業，或是有輔助的程式，這時能在 Pod 中使用多個 container，以 container 為單位來建構出這個應用，但又都放在同一個 Pod 中。

Kubernetes 提供了 init container 及 multi container 的功能讓一個 Pod 中能運行多個 container。

# Multi-Container

Pod 中可運行多個 container，在 spec `containers` 這邊設置。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world-pod
spec:
  containers:
    - name: container1
      image: chihhuiw/nodejs-helloworld:1.0.0
      imagePullPolicy: Always # 預設是 IfNotPresented
      command: ["node", "/app/index.js"]
      args: ["3001"]
      ports:
        - containerPort: 3001
      volumeMounts:
        - mountPath: /demo1
          name: demo-volume
    - name: container2
      image: chihhuiw/nodejs-helloworld:1.0.0
      imagePullPolicy: Always
      command: ["node", "/app/index.js"]
      args: ["3002"]
      ports:
        - containerPort: 3002
      volumeMounts:
        - mountPath: /demo2
          name: demo-volume
  volumes:
    - name: demo-volume
      emptyDir: {}
```

這邊我設置了 arguments，可以在啟動 node js 程式時指定要跑的 port。所以這邊我跑了兩個 container 但使用不同的 port，因同一個 port 只能被一個 container 使用。

## Pod 內的 container 該如何溝通呢？

同一個 Pod 中的兩個 containers 可直接用 `localhost: port` 連接。

來測試看看：

exec via container name

```yaml
kubectl exec -it hello-world-pod -c container1 -- /bin/sh
```

`-c` : 指定 container 名稱，如果一個 Pod 裡面只有一個 Container 可忽略

container 1  
![[IMG-k3d Multi Container & Init Container-20241003104933979.png]]

container 2  
![[IMG-k3d Multi Container & Init Container-20241003104948886.png]]

## 如果一個 Pod 裡面兩個 Containers 使用了同樣的 Port

因 Pod 建立時會被 assign 一組 IP，同一個 Pod 內的 container，不能監聽同樣的 port。這種情況下只會有一個容器綁定到那個 port，第二個容器則無法成功啟動。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    name: nginx-pod
spec:
  containers:
    - name: nginx-1
      image: nginx
      ports:
        - containerPort: 80
    - name: nginx-2
      image: nginx
      ports:
        - containerPort: 80
```

![[IMG-k3d Multi Container & Init Container-20241003105007679.png]]

`kubectl describe pod nginx-pod` →  
![[IMG-k3d Multi Container & Init Container-20241003105007807.png]]

## 共用 Volume

另外上面的 yaml 我們還有定義了 `volume` 這個 field，這是用來設置 pod 存取檔案的資訊。可以發現 volume 的使用是 Pod level 的，也就是同一個 Pod 只會有一個 volume。

在這邊我們使用 `emptyDir` 的方式掛載 volume。
當 Pod 被分配到節點上，一個 emptyDir volume 就會被建立起來，並持續到 Pod 死掉。而這個 directory 一開始會是空的，可以被掛到多個 container 中。應用場景會是一些暫時性的使用，例如快取。

而在每個 container 中可設定這個 volume 要掛載進 container 中的哪個位置。
這兩個 container 能互相對同一個目錄下的檔案做操作。

一樣來觀察看看 -  
![[IMG-k3d Multi Container & Init Container-20241003105031404.png]]

上圖我執行了以下指令

- 進去 container 1
- 檢查是否在根目錄底下有 `demo1` folder → 如 yaml 所設置
- 建立 `test.txt` ，其中包含 `test` 的內容
- 退出後進去 container 2
- 檢查是否在根目錄底下有 `demo2` folder → 如 yaml 所設置
- 檢查 `demo2` 底下是否有剛才建立的 `test.txt`
- 刪除 `test.txt`
- 退出後再進去 container 1， `/demo1`
- 使用 `ls` 發現原本的 `text.txt` 不見了

# Init container

在啟動 pod 的時候，其中 containers 的啟動是不分順序的。但如果我們有些工作希望能在主要 container 啟動之前執行，就可以使用 initial container，例如先從 repo 拉下最新的程式碼 etc。

如同 containers 的 section 一樣，一個 pod 可以有多個 initContainer，會照順序執行。要注意的是，要等前一個 initContainer 運行成功，才會跑下一個。init container 如果運行失敗，kubelet 會重啟這個 pod 直到 init container 啟動成功。但如果 `restartPolicy` **(Pod level**) 設置為 `Never` ，這個 Pod 就會被視為啟動失敗。

```yaml hl:12-24


apiVersion: v1
kind: Pod
metadata:
  name: init-containers-pod
spec:
  containers:
    - name: myapp-container
      image: busybox:1.28
      command: ["sh", "-c", "echo The app is running! && sleep 3600"]
  initContainers:
    - name: init-myservice
      image: busybox:1.28
      command:
        [
          "sh",
          "-c",
          "until nslookup myservice; do echo waiting for myservice; sleep 2; done;",
        ]
    - name: init-mydb
      image: busybox:1.28
      command:
        ["sh", "-c", "until nslookup mydb; do echo waiting for mydb; sleep 2; done;"]



```

上面的程式碼有主要的 container 一個，init containers 兩個。

`init-myservice` 執行的指令是，執行 `nslookup myservice` ，如果沒有找到就印出 `waiting for ...`，找到後等 2 秒結束。

而 `init-mydb` 則會等 `init-myservice` 執行成功後，才執行。一樣會執行 `nslookup mydb` 後，如果沒找到就印出字樣，找到後等 2 秒後結束。

init containers 執行完畢後，才會開始跑 `myapp-container` 。

但第一階段我們還沒有建立 `myservice` & `mydb` ，所以 init containers 應該是無法完成它的任務，整個 Pod 會是 Pending 的狀態。  
![[IMG-k3d Multi Container & Init Container-20241003105100196.png]]

利用 `kubectl describe pod myapp-pod` 觀察  
![[IMG-k3d Multi Container & Init Container-20241003105122271.png]]

可以看到 `init-service` State = Running，但後續的 containers 狀態都還是等待中。  
![[IMG-k3d Multi Container & Init Container-20241003105133714.png]]

利用 `k logs myapp-pod -c init-myservice` 檢查特定 container log 
發現 `init-myservice` 很努力的在找 myservice 不過找不到  
![[IMG-k3d Multi Container & Init Container-20241003105142586.png]]

後面的 containers 還沒跑起來所以看不到 log  
![[IMG-k3d Multi Container & Init Container-20241003105149927.png]]

再來我們建立 `myservice` & `mydb` service 給這兩個 container 連

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myservice
spec:
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
---
apiVersion: v1
kind: Service
metadata:
  name: mydb
spec:
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9377
```

這時再檢查 pod status，變成 Running 了  
![[IMG-k3d Multi Container & Init Container-20241003105155886.png]]

利用 `kubectl describe pod myapp-pod` 觀察  
![[IMG-k3d Multi Container & Init Container-20241003105156116.png]]

Init containers State = Terminated，主要的 Container State = Running  
![[IMG-k3d Multi Container & Init Container-20241003105201488.png]]

一樣用 `kubectl logs mypod -c <container name>` 看 log。

這張是 `kubectl logs myapp-pod -c init-myservice` 的一部分 output
可以看出有印出 waiting for myservice 的句子，最後找到 myservice 後則停止。  
![[IMG-k3d Multi Container & Init Container-20241003105201652.png]]

這張是 `kubectl logs myapp-pod -c init-mydb` 的 output，因我們同時建立了兩個 service，而 `init-mydb` 這個 container 又是接在 `init-service` 後面啟動，這時 nslookup 直接找到 mydb 這個 service 了，就沒有印出 `waiting for mydb`  
![[IMG-k3d Multi Container & Init Container-20241003105209436.png]]

最後看一下主要 container 的 log  
![[IMG-k3d Multi Container & Init Container-20241003105213640.png]]

等 init containers 執行成功後，主要的 container 也順利啟動了

---

`nslookup <service name>` 的部分、後面那串 `default.svc.cluster.local` 的介紹，Service 的部分會講到（應該，逃避中 QQ）。

_Reference_  
[https://kubernetes.io/docs/concepts/workloads/pods/](https://kubernetes.io/docs/concepts/workloads/pods/)  
[https://kubernetes.io/docs/tasks/access-application-cluster/communicate-containers-same-pod-shared-volume/](https://kubernetes.io/docs/tasks/access-application-cluster/communicate-containers-same-pod-shared-volume/)  
[https://kubernetes.io/docs/concepts/workloads/pods/init-containers/](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)

