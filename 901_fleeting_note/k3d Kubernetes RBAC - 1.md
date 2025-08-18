[[k3d]]


---
類似雲端服務，Kubernetes 的資源也會限制存取對象。不過在本機上練習 kubernetes 會沒什麼感覺，因為你就是 admin。

回顧一下非常前面提到的 Cluster structure，Cluster 中會有 API server，使用者 (我) 利用 `kubectl` 可以列出有哪些 Pods、可以建立新的資源、更改或刪除資源；或是透過 API 做這些操作。但要做這些操作是需要權限的。而在確定這個請求是可以被執行之前，首先要先知道你是誰 (Authentication)。

在 Kubernetes 中進行 Authentication 的形式有很多種，例如 static token files、X.509 certificates、OpenID、Service Account。不過這篇只會介紹 Service Account - 先從 Kubernetes Object 開始理解。

# Service Account

在 Day3 建 Dashboard 的時候有提到，Service Account 也是 Kubernetes Object 的一種。概念上這個 Account 與 User 類似，只是 Service Account 是給機器 (或某個應用程式) 用的。在什麼情況下會需要讓應用程式去對 Pods、Deployment etc 做操作？例如 Prometheus 會透過 Service Account 跟 Kubernetes API 拉 metrics、自動化工具 (Jenkins, GitLab) 使用 Service Account 來部署應用程式到 Kubernetes Cluster 中。

在每個 Namespace 被建立時，都會有一個預設的 Service Account 被建立。可以透過 `kubectl get sa -n <namespace>` 觀察到 -  
![[IMG-k3d Kubernetes RBAC - 1-20241003104933970.png]]

如果要建立 Service Account 可以用以下指令 -

```bash
# kubectl create serviceaccount <name>
kubectl create serviceaccount test-sa
kubectl create sa test-sa
```

或是 YAML

```yaml hl:2
apiVersion: v1
kind: ServiceAccount
metadata:
  name: test-sa
	namesapce: test-ns
```

從 spec 中可以得知 Service Account 是 Namespaced Object。那這個 Service Account 要怎麼用？建立完 Service Account 後，會再透過 Authorization 的機制給予權限，然後建立 Pod 的時候指定 Service Account。如果沒有指定的話新建的 Pod 會自動 Assign `default` Service Account。  
![[IMG-k3d Kubernetes RBAC - 1-20241003104948879.png]]

如果要指定，可以在 Pod spec 加上 `serviceAccount`

```yaml hl:10
apiVersion: v1
kind: Pod
metadata:
	name: my-pod
	namesapce: test-ns
spec:
	containers:
		- name: my-pod
			image: my-image
	serviceAccountName: test-sa
```

那具體來說，到底 API server 是怎麼認得這個 Service Account 的呢？

當 Service Account 被 Assign 給 Pod，Kubernetes 預設會將 Service Account Token 分給這個 Pod，Token 會被 mount 進 Pod 內部的 `/var/run/secrets/kubernetes.io/serviceaccount` 之中 -  
![[IMG-k3d Kubernetes RBAC - 1-20241003105007372.png]]

也可以從 describe 資訊中看到  
![[IMG-k3d Kubernetes RBAC - 1-20241003105030421.png]]

當使用 Service Account 的 Pod 向 API server 發送請求的時候，會帶有 `Authorization` Header，其中會有 Bearer token 的值，讓 API server 辨識。

如果不希望 token 自動被 mount 進 Pod 中，在 spec 中可以將 `automountServiceAccountToken` 設為 false

```yaml hl:11
apiVersion: v1
kind: Pod
metadata:
	name: my-pod
	namesapce: test-ns
spec:
	containers:
		- name: my-pod
			image: my-image
	serviceAccountName: test-sa
	automountServiceAccountToken: false
```

後面會實作這個 Token 是怎麼使用。

# Authorization

Kubernetes authorization 的模式有幾種 -

- Node
- ABAC
- RBAC
- Webhook

這篇主要講 RBAC。如果要使用 RBAC 的模式需要啟用 `rbac.authorization.k8s.io` API group。

# Role & RoleBinding

Role 這個 Object 會定義這個角色可以做什麼事，例如下面的例子 - 
developer 這個 Role 可以看到有哪些 Pods、可以建立 / 更新 / 刪除 Pods，可以建立 ConfigMap。
而沒有提到的資源、沒有定義到的動作，這個 Role 就沒有權限執行。

```yaml hl:2
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
	name: developer
rules:
	- apiGroups: [""] 
		resources: ["pods"]
		# resourceNames: ["blue", "orange"]
		verbs: ["list", "get", "create", "update", "delete"]
	- apiGroups: [""] 
		resources: ["ConfigMap"]
		verbs: ["create"]
```

Spec 中 rules 的部分可以分別列不同種的資源，其中

`apiGroups` : 要寫說是哪個 API Group 的對象，如果是 Core group 可以留白 (例如 Pods)

`resources` : 可以操作的資源，如果只想限定某些特定的資源，例如名字是 blue & ornage 的 Pod，可以用 `resourceNames` 限定

`verbs` : 能夠對資源做什麼操作，有哪些動詞可以用可以看這篇 - 
[https://kubernetes.io/docs/reference/access-authn-authz/authorization/#determine-the-request-verb](https://kubernetes.io/docs/reference/access-authn-authz/authorization/#determine-the-request-verb)  
![[IMG-k3d Kubernetes RBAC - 1-20241003105054581.png]]

定義好角色之後，就要將 account 與 role 綁定起來，這時就會用到 RoleBinding 這個 Object -

```yaml hl:2
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
	name: developer-binding
subjects:
	- kind: ServiceAccount
		name: test-sa
		apiGroup: ""
		namespace: test-ns
roleRef:
		kind: Role
		name: developer
		apiGroup: rbac.authorization.k8s.io
```

`subjects` 對象，可以想成這邊就是指定 account
- `kind` : "User", "Group", or "ServiceAccount”
- `apiGroup` : 關聯的這個 subject 所屬的 API group
	- 如果是 ServiceAccount，屬於 Core API，值為 “”；
	- 如果是 User or Group，值會是 "rbac.authorization.k8s.io"
- `namespace` : 
	- Service Account 要填是在哪個 Namespace 的 SA
	- User & Group 不是 namespaced object，不用加；

這邊可以綁很多個 subjects 給同一個 Role

`roleRef` 則是要綁定的 Role
- `kind` : 可以放 Role 或是 ClusterRole
- `apiGroup` : "rbac.authorization.k8s.io"

---


以下來實作在 Pod 裡面 access kube API server，列出 default Namespace 之中的 Pods


`k create ns test-ns`


執行以下的 yaml -

```yaml hl:2,8,16,19,31
apiVersion: v1
kind: ServiceAccount
metadata:
  name: test-sa
  namespace: test-ns
---
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  namespace: test-ns
spec:
  containers:
    - name: nginx
      image: nginx
  serviceAccountName: test-sa
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["list", "get", "create", "update", "delete"]
  - apiGroups: [""]
    resources: ["ConfigMap"]
    verbs: ["create"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer-binding
subjects:
  - kind: ServiceAccount
    name: test-sa
    apiGroup: ""
    namespace: test-ns
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

- Role & RoleBinding 在 default namespace
- Role developer 可以對 pods 做 list, get, create, update, delete 的操作、可以對 ConfigMap 做 create 的操作
- Pod `my-pod` & Service Account `test-sa` 在 `test-ns` 這個 namespace
- 進去 Pod 之中對 API server 發 GET request，要能夠列出 default namespace 的 Pods

指令的部分參考 [https://kubernetes.io/docs/tasks/run-application/access-api-from-pod/](https://kubernetes.io/docs/tasks/run-application/access-api-from-pod/)

> The API server's in-cluster address is also published to a Service named `kubernetes` in the `default` namespace so that pods may reference `kubernetes.default.svc` as a DNS name for the local API server.

進去 Pod 之中，向 `kubernetes.default.svc` 發 Request，並帶上 token

go inside
```bash

k exec -it -n test-ns my-pod -- /bin/sh

```


```bash
# Point to the internal API server hostname
APISERVER=https://kubernetes.default.svc

# Path to ServiceAccount token
SERVICEACCOUNT=/var/run/secrets/kubernetes.io/serviceaccount

# Read the ServiceAccount bearer token
TOKEN=$(cat ${SERVICEACCOUNT}/token)

# Reference the internal certificate authority (CA)
CACERT=${SERVICEACCOUNT}/ca.crt

# Explore the API with TOKEN
curl --cacert ${CACERT} --header "Authorization: Bearer ${TOKEN}" -X GET ${APISERVER}/api



```




![[IMG-k3d Kubernetes RBAC - 1-20241003105121542.png]]  
得到回應，通常 API server 開的 port 是 6443，`172.21.0.4` 是 control-plane 的位置

![[IMG-k3d Kubernetes RBAC - 1-20241003105133437.png]]

再來測試兩個請求

- GET `${APISERVER}/api/v1/namespaces/default/pods`
- GET `${APISERVER}/api/v1/namespaces/default/services`

在外頭用 kubectl 操作可以得到以下結果，因為是 Admin 想看什麼都行  
![[IMG-k3d Kubernetes RBAC - 1-20241003105142350.png]]

在 Pod 中因為綁定的 Service Account 只能列出 default namespace 的 Pods，當發請求說要 GET services 時就會被禁止

(pods list 資訊太長只截部分，可以自己測試如果用 default SA 能不能得到 pods list)  
![[IMG-k3d Kubernetes RBAC - 1-20241003105149821.png]]

Services 的部分 = 403  
![[IMG-k3d Kubernetes RBAC - 1-20241003105155767.png]]

另外補充，透過 `kubectl proxy` 可以直接在 host access api server
```bash
kubectl proxy --port=8080 &
curl http://localhost:8080/api/
```

但這只是方便開發跟測試，不建議用在正式環境

---


_Reference_  
[https://kubernetes.io/docs/concepts/security/service-accounts/](https://kubernetes.io/docs/concepts/security/service-accounts/)  
[https://kubernetes.io/docs/reference/access-authn-authz/authorization/](https://kubernetes.io/docs/reference/access-authn-authz/authorization/)  
[https://kubernetes.io/docs/reference/access-authn-authz/authentication/](https://kubernetes.io/docs/reference/access-authn-authz/authentication/)  
[https://kubernetes.io/docs/tasks/run-application/access-api-from-pod/](https://kubernetes.io/docs/tasks/run-application/access-api-from-pod/)  
[https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)  
[https://stackoverflow.com/questions/52995962/kubernetes-namespace-default-service-account](https://stackoverflow.com/questions/52995962/kubernetes-namespace-default-service-account)  
[https://pwittrock.github.io/docs/tasks/administer-cluster/access-cluster-api/](https://pwittrock.github.io/docs/tasks/administer-cluster/access-cluster-api/)