[[k3d]]




```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

![[IMG-k3d 建立 dashboard-20241003104933994.png]]

Dashboard 預設沒有權限是沒辦法看的，這邊我們需建立一個 sample user。

在 kubernetes 中 RBAC 的執行是透過 Service Account / Role / Role Binding / Cluster Role / Cluster Role Binding 來實現的。
- **Service Account** 類似於 User，就是一個 account，但 Service Account 是給機器使用的，可綁在 Pod 上，給 Pod 一個身份。
- **Role** 則是定義這個角色可以對哪些資源 (e.g. “nodes”, “pods”, “deployment”) 進行哪些操作 ( verb, e.g. “list”, “create” etc) 
	- 這邊的 Role 定義的資源是 Namespace level 的, 如果要對所有 Namespace, 整個集群的資源的 role  -> 要用 **Cluster Role** 
- 建立好角色後，再透過 **RoleBinding** or **ClusterRoleBinding** 將身份及角色綁定，就可以讓 User or Service Account 對 k8s resource 有操作的權限。
![[IMG-k3d 建立 dashboard-20241003104948899.png]]



首先建立 Service Account
名字是 admin-user
作用在 kubernetes-dashboard 的 ns
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```


建立 ClusterRoleBinding
這邊是綁 `cluster-admin` ClusterRole，通常這個 cluster role 在建立 cluster 時就會建立好
這邊綁定 `cluster-admin` 跟我們剛才建立的 Service Account `admin-user`
    
```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: admin-user
    namespace: kubernetes-dashboard
```




```sh

# 就 kubernetes-dashboard 這個 ns 下，建立 admin-user 的 token
k -n kubernetes-dashboard create token admin-user


-> will get below token


eyJhbGciOiJSUzI1NiIsImtpZCI6Ikp5N0J2c0UwbnhieGlEeVUyWXJ4bVpGTFhhWnpFd0VVNWkyeU1UVHlwNmsifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiLCJrM3MiXSwiZXhwIjoxNzE4NDI0MDk0LCJpYXQiOjE3MTg0MjA0OTQsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJhZG1pbi11c2VyIiwidWlkIjoiNGZiNTBkNTYtNjI2YS00MDM1LTliYWUtNzAwZTI4M2JiNTNkIn19LCJuYmYiOjE3MTg0MjA0OTQsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDphZG1pbi11c2VyIn0.Vk-e7K5KRrfN2B8Kgseco27hKVs1okRzCCdFIEzgFBAHikMKUzOcGhbDXPpYCxj3yINuXeGD7JZWds0AYRFCjAozGJ26hs8wNgBweXu8sNcwqVZ05ZZzcmH4nhf8WrHxawKoSzm2rFcrTA-oZPyrNVAwsQfMm7BOwt5N3g6tGFboh6fKoSjDTXOf3SPiIkJxpvo4ATx4f5FdlDhfYh5md1Gr_Z3tc9e_23Nsv-RliUiw1QxXBtRJ0dGwgnzV0Ktk-541v1c7Pr5G2XnmeunVdatUSrgbnuhlCbb9EvGaL4DPgj22rtji29S2BXY1mnXFl72ZDQ4GZFpYjwRup1acuA



# 啟動代理伺服器讓本機能夠 Access Kubernetes API, default is 8001 port
k proxy

Starting to serve on 127.0.0.1:8001



# mac use D port forward
ssh -D 3000 re4388_103_0406@gcpN2 -N
```

use firefox Foxyproxy


透過 3000 port sock5 轉發
![[IMG-k3d 建立 dashboard-20241003105008666.png]]

[my – FigJam](https://www.figma.com/board/4MD4T4IegKTKPRN3RdJGE5/my?node-id=14-311&t=3juoAs6JSyykVQuK-0)


![[IMG-k3d 建立 dashboard-20241003105034053.png]]
打一個可以動態轉發 vm 服務的ssh port 22 的通道 連到 mac 3000 port
可以使用 vm port 8001
然後透過 kproxy 連到 k3d  cluster 的服務



get
![[IMG-k3d 建立 dashboard-20241003105100715.png]]



這邊 port 預設是 8081

指令文件: [https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#proxy](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#proxy)

接下來就可以透過 http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
訪問 Dashboard

Done!
![[IMG-k3d 建立 dashboard-20241003105122543.png]]