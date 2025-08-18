[[k3d]]



`k3d cluster create` 有多個 options 可以下，可指定有多少 agent & server、映射 host port 至 container port

```bash
k3d cluster create demo -p "8080:80@loadbalancer" --agents 2
```

`-p` : 映射主機的 port 到 container port，這邊會是將 host 8080 port 映射到 loadbalancer 80 port
kubernetes cluster 中 pod 是運行在內部虛擬網路中，host 沒有辦法直接訪問，透過 host port mapping 可以利用 localhost:8080 來訪問在 k8s 上跑的 app  
`--agents` : agents number  
`--servers` : servers number  
更多 options 可參考官方文件: [https://k3d.io/v5.6.0/usage/commands/k3d_cluster_create/](https://k3d.io/v5.6.0/usage/commands/k3d_cluster_create/)




- `apiVersion`: k8s api ver
	- 定義是 kubernetes API 的哪個版本，以建立 kubernetes object，這邊 `v1` 是 Core API Group。
	- 不同的 object 會用不同的 API 建立，以另一種 object - Deployment 來說，使用的就是 `apps/v1` 才能建立，這代表 `apps` 這個 **API group** 的 `v1` **version**。
	- API groups & versioning 規則 → [https://kubernetes.io/docs/concepts/overview/kubernetes-api/#api-groups-and-versioning](https://kubernetes.io/docs/concepts/overview/kubernetes-api/#api-groups-and-versioning)  
	- 例子 → [https://kubernetes.io/docs/reference/using-api/api-concepts/#resource-uris](https://kubernetes.io/docs/reference/using-api/api-concepts/#resource-uris)  
	- 各個資源使用的 API Group & Version 以及 yaml config example → [https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.28/](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.28/)

- `kind` : k8s 資源類別的名稱
- `metadata` : 幫助辨識這個 object 的 data，包括 `name`, `labels` , `namespace`
	- `namespace`: 如果沒有指定就會是預設的 namespace，通常會是 `default`
- `spec` : 對這個 Object 的設定。
	- 以 Pod 來說 containers 為 required field
	- 一個 Pod 裡面一定會跑至少一個 container，需定義要用哪個 image 以及 container name。
	- 其他還可以定義這個 container 需要多少 resource (cpu & memory)、要 expose 哪個 containerPort、設 env …  等等等
	- 關於各個 Object spec 有什麼哪些是必要的可以看這邊 → [https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/)





```yaml

# pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-yaml
spec:
  containers:
  - name: nginx
    image: nginx

```



常用的指令可以看這篇 → https://kubernetes.io/docs/reference/kubectl/cheatsheet/
kubectl 介紹 → https://kubernetes.io/docs/reference/kubectl/
kubectl commands: 不曉得什麼指令有哪些 commands & 如何用就來這裡翻 → https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands