[[k3d]]



fail
```sh



# need to use to see logs
kubectl get events --sort-by=.metadata.creationTimestamp

這邊可以看到 schelder 找不到 selector 

大概是因為我的 yaml 下面有標明這些檔案需要選 agent node, 但目前我的 mycluster 沒有



```
![[IMG-k3d 建立 deployment and service 失敗的處理-20241003104933996.png]]


要整理一下 cluster

先不把 mycluster remove
但我想要先把 dashboard 移到demo cluster


建立 demo cluster if not created yet
```sh
k3d cluster create demo -p "8080:80@loadbalancer" --agents 2
```


- [x] then let's remove all dashboard stuff in mycluster

k get all,secrets 
but this command maynot see all stuff


need to go here to find all `kind`
[recommended.yaml](https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml)

直接砍掉 ns 就會一起把 ns 底下的東西一起砍掉了!
很方便！！但要小心使用 XDD
因為如果你一開始設計針對 ns 劃分資訊就要很小心, 不然就會砍掉ns 一起砍掉你可能不想要砍掉的資源，然後你就 gg 了

目前我用 k9s 砍
然後利用
kctx and kns 來幫我切 ctx and ns

- [x] 建立 dashboard at demo cluster

- [x] try again nodeapp and mysql
```sh

~/simple-login/k8s on  main! ⌚ 3:17:23
$ k create ns ironman
namespace/ironman created

~/simple-login/k8s on  main! ⌚ 3:17:47
$ ./deploy.sh
secret/login-app-secrets created
deployment.apps/db-deploy created
service/db-service created
deployment.apps/login-app-deploy created
service/login-app-service created
done

```

db and app 兩個 deploy yaml 都 拿掉 node selector 那段 


err log
```

6s          Warning   Failed              pod/db-deploy-57f8649857-ngsmq           
Error: couldn't find key MYSQL_ROOT_PASSWORD in Secret ironman/login-app-secrets

```

然後把漏掉的 MYSQL_ROOT_PASSWORD 設定補上

可以跑起來囉!
```sh

$ k get all,secrets
NAME                                    READY   STATUS    RESTARTS   AGE
pod/db-deploy-57f8649857-rk5jx          1/1     Running   0          2m32s
pod/login-app-deploy-86f668d866-xqdhb   1/1     Running   0          2m32s

NAME                        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/db-service          ClusterIP   10.43.94.208   <none>        3306/TCP         2m32s
service/login-app-service   NodePort    10.43.32.150   <none>        3000:30001/TCP   2m32s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/db-deploy          1/1     1            1           2m32s
deployment.apps/login-app-deploy   1/1     1            1           2m32s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/db-deploy-57f8649857          1         1         1       2m32s
replicaset.apps/login-app-deploy-86f668d866   1         1         1       2m32s

NAME                       TYPE     DATA   AGE
secret/login-app-secrets   Opaque   6      2m33s

```



可以發現有 Deployment 也有 ReplicaSet，會列出 Desired & Current & Ready Pod Number。
而 ReplicaSet 及 Pod 名稱後面都有一串隨機字串。

**為什麼要有 Deployment 又有 ReplicaSet 呢？**
**Deployment 有比 ReplicaSet 更多的功能，在進行版本更新時能夠使用不同的策略來維持服務。**

之後會更詳細比較不同的 Deployment 部署策略。



---


因為我們使用了 Node Port 的方式來讓 Pod 接收外界流量，這邊我們就要把本機的 port 映射到 Node 30001 port，才能接上。

參考 k3d 官網文件 -  
[https://k3d.io/v5.4.6/usage/exposing_services/#2-via-nodeport](https://k3d.io/v5.4.6/usage/exposing_services/#2-via-nodeport)  
[https://k3d.io/v5.4.6/usage/commands/k3d_cluster_edit/](https://k3d.io/v5.4.6/usage/commands/k3d_cluster_edit/)

必須幫這個 cluster 開個接口，把 host port map 到 node port


```bash
k3d cluster edit demo --port-add "8081:30001@agent:1"
```

在主機的端口 `8081` 上開放，將流量映射到 k3d 的第 `1` 個 agent 節點的 NodePort `30001` 上。
換句話說，當你訪問主機的 `8081` 端口時，該請求會被轉發到 k3d 集群中的第 `1` 個 agent 節點的 `30001` 端口。
這條命令的主要功能是通過端口映射，使外部請求可以通過本地主機的指定端口（8081），訪問 k3d 集群中指定 agent 節點（第 0 個）的服務端口（30001），從而實現有效的端口轉發和服務訪問。這在開發和測試中非常實用，特別是在需要測試和訪問 Kubernetes 集群內運行的服務時。

PS: login app 在那個 ndoe agent 上，可以透過 dashboard Node點進去看


這時候開啟 [http://localhost:8081](http://localhost:8081/) ，應該能看到畫面


因為我有打開動態轉發，因此mac 也可以看到
![[IMG-k3d 建立 deployment and service 失敗的處理-20241003104948901.png]]


也可以用 `kubectl describe` 來確認 Pod 被放到哪個 node 上
![[IMG-k3d 建立 deployment and service 失敗的處理-20241003105008797.png]]


重新修掉我的 node select

首先，確認你要調度的節點上的標籤。你可以使用以下命令檢視節點上的標籤：

```sh

kubectl get nodes --show-labels


NAME                STATUS   ROLES                  AGE    VERSION        LABELS
k3d-demo-agent-0    Ready    <none>                 140m   v1.28.8+k3s1   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=k3s,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=k3d-demo-agent-0,kubernetes.io/os=linux,node.kubernetes.io/instance-type=k3s
k3d-demo-agent-1    Ready    <none>                 140m   v1.28.8+k3s1   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=k3s,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=k3d-demo-agent-1,kubernetes.io/os=linux,node.kubernetes.io/instance-type=k3s
k3d-demo-server-0   Ready    control-plane,master   140m   v1.28.8+k3s1   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=k3s,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=k3d-demo-server-0,kubernetes.io/os=linux,node-role.kubernetes.io/control-plane=true,node-role.kubernetes.io/master=true,node.kubernetes.io/instance-type=k3s


```

如果你的目標節點還沒有標籤，可以使用以下命令添加標籤。例如，將標籤 `role=agent` 添加到節點 `node-name`：

```sh

~ ⌚ 4:18:54
$ kubectl label nodes k3d-demo-agent-0 role=agent
node/k3d-demo-agent-0 labeled

~ ⌚ 4:19:36
$ kubectl label nodes k3d-demo-agent-1 role=agent
node/k3d-demo-agent-1 labeled




```

這樣就加上 label了
![[IMG-k3d 建立 deployment and service 失敗的處理-20241003105008925.png]]

修改 yaml and re-apply
![[IMG-k3d 建立 deployment and service 失敗的處理-20241003105035360.png]]

done!
![[IMG-k3d 建立 deployment and service 失敗的處理-20241003105100944.png]]



目前如果打兩個 pod, 都會forward過去, 不是只有 agent1
~ ⌚ 4:30:03
$ k logs -n ironman login-app-deploy-7675c7dcf4-j5dfd
Server is running on port 8000...
ERROR req.path:  /config

~ ⌚ 4:30:19
$ k logs -n ironman login-app-deploy-7675c7dcf4-s9jlh
Server is running on port 8000...


用 `kubectl get node -o wide` 取得 node Internal IP

```sh


$ kubectl get node -o wide
NAME                STATUS   ROLES                  AGE    VERSION        INTERNAL-IP   EXTERNAL-IP   OS-IMAGE           KERNEL-VERSION   CONTAINER-RUNTIME
k3d-demo-server-0   Ready    control-plane,master   153m   v1.28.8+k3s1   172.18.0.2    <none>        K3s v1.28.8+k3s1   6.5.0-1022-gcp   containerd://1.7.11-k3s2
k3d-demo-agent-1    Ready    <none>                 153m   v1.28.8+k3s1   172.18.0.3    <none>        K3s v1.28.8+k3s1   6.5.0-1022-gcp   containerd://1.7.11-k3s2
k3d-demo-agent-0    Ready    <none>                 153m   v1.28.8+k3s1   172.18.0.4    <none>        K3s v1.28.8+k3s1   6.5.0-1022-gcp   containerd://1.7.11-k3s2


```


ssh 到 pod 裡面
kubectl exec -it -n ironman <pod_name> -- /bin/sh
kubectl exec -it -n ironman login-app-deploy-7675c7dcf4-j5dfd -- /bin/sh

打這個
wget -O - 172.18.0.3:30001

發現打給 server-0, agnet-0, agent-1，都有成功得到回應。
確認每個 Node 上都開了 30001 這個 port。