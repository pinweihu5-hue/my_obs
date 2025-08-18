[[k3d]]

---


今天講 Pod 為什麼能用 Service Name 就跟 Service 連接上，然後補一下 Namespace 的 Overview。

在 Kubernetes 中，Namespace 是個虛擬的概念，用來隔離同個 Cluster 中的資源。

以使用情境來看，多個團隊或是不同的專案都可能使用同一個 Cluster，但避免不同的資源互相干擾或是做權限的控管就可使用 Namespace 來隔絕資源。

在同一個 Namespace 中，同一個種類的資源名字必須要是獨特的，不同 Namespace 中可以同名。

在 Cluster 建立的時候會有幾個 Initial Namespaces:

- default
    - 預設的 namespace，如果建立 objects 時沒有指定 namespace，會自動算在這個 namespace
- kube-node-lease
    - 在這個空間會存放 `Lease` objects，每個 node 會有對應的 lease object 在這個 namespace 中。Node 上的 kubelet 會定期向 API server 發送 hearbeats，同時也更新對應的 node lease object，讓 control plane 可以確認 Node 的存活狀況
    - Control Plane 透過監控 kube-node-lease 中的 lease object，來確認說預定的時間內有沒有被更新 (有沒有發 heartbeats)，沒有可能代表 Node 運行出問題
- kube-public
    - 在這個 namespace 的資源可以被所有使用者讀取，不需要 authenticate，並不是必要的 namespace
- kube-system
    - kubernetes 運行所需要的內部資源會放在這邊，例如 CoreDNS、API server、Controller Manager etc。不建議在這個 namespace 跑應用程式

Namespace 相關的指令 -

```bash
# ns: namespace
kubectl create ns new-ns
kubectl get ns
kubectl delete ns
```

**要注意刪除 namespace，其中的所有資源都會一起被刪掉。**

使用 yaml 來建立 -

```yaml
apiVersion: v1
kind: Namespace
metadata:
	name: new-ns
```

# Namespaces and DNS

在同一個 namespace 的 Pod 可以透過 service 的名字互相連接，而不需要知道 service IP & port。

**當 service 被建立時，Kubernetes 會為這個 service 建立一個 DNS record**
這個 DNS record 格式會是 `<service_name>.<namespace>.svc.<domain_name>` 

例如在 `default` namespacce 中建立了一個名為 `db-service` 的 service，那這個 DNS record 就會是 
`db-service.default.svc.cluster.local`

現在來對應一下：  
`<service_name>` : =`db-service`, service 名稱  
`<namespace>` : =`default`, namespace 名稱  
`svc` : service 的意思  
`<domain_name>` : kubernetes cluster 的預設域名，通常會是 `cluster.local`


在 Kubernetes 中，會有 DNS server 來幫忙做 DNS 解析。
當 Pod 對 Service Name 做 DNS 查詢時，DNS server 就能透過 Service Name 去找出相關的 DNS record，解析出對應的 Service IP。Pod 就能連接到對應的 Service。

例如：
```bash
# psedo code
mysql.connect("db-service")
mysql.connect("db-service.default.svc.cluster.local")
```
這兩種寫法都能連接到 `default` namespace 中的 `db-service` service。


對同一個 Namespace 的 Pod 來說，它可以只使用 service name 就找到對應的服務，不用指定 namespace。
因為 Pod 在建立時，kubelet 會設置 Container 內部的 `/etc/resolv.conf` 檔案，添加 Pod 所在的 namespace 的網域名稱，例如：

```bash
# cat /etc/resolv.conf
search default.svc.cluster.local svc.cluster.local cluster.local
nameserver 10.43.0.10
options ndots:5
```

當 Pod 用 service name 進行 DNS 查詢的時候，就能帶上這邊定義的網域，找到正確的 Service。

先前也有提到 Service 是可以供所有 Cluster 中的 Pod 訪問的，只不過要訪問別的 Namespace 的 Service 的話，就必須帶上正確的域名，才能找到正確的 Service。

# 當前的 Namespace

預設的 namespace 為 default，所以當我們下 `kubectl get ...` 的指令，若沒有指定 namespace 則是會列出 default namespace 中的資源。如果想要看目前的 namespace 是什麼、更改當前 namespace，可以使用下面的指令：

```bash

# 看目前的 namespace 是什麼
kubectl config view


# set ctx and ns
kubectl config set-context --current --namespace=test
```

還沒更改 namespace 前，`kubectl config view` 的 output，**沒有看到 namespace 且沒下 -n tag的話， 就是 default**  
![[IMG-k3d Namespace & 一點點 DNS-20241003104933985.png]]

接著建立一個 namespace `test` ，並運行一個 Pod；
更改 name space 後 kubectl 就不用再下 `-n` tag 就會列出 `test` namespace 中的資源  
![[IMG-k3d Namespace & 一點點 DNS-20241003104948891.png]]

# Cluster level objects

Kubernetes 的資源有些是定義在某一個 Namespace 中，
但有些資源則是不屬於任何 Namespace ，這些資源的建立及管理是在 Cluster level，not ns level, 因此可以在不同的 Namespace 中被使用。

常見的 pods、services、deployments … 都是需要在同個 Namespace 之中；
像 storage class、node、persistent volume 這類就不是 namespace level objects。

可使用以下指令來看有哪些 objects 是 mamespace level object -
```bash
# In a namespace
$ kubectl api-resources --namespaced=true
NAME                        SHORTNAMES   APIVERSION                     NAMESPACED   KIND
bindings                                 v1                             true         Binding
configmaps                  cm           v1                             true         ConfigMap
endpoints                   ep           v1                             true         Endpoints
events                      ev           v1                             true         Event
limitranges                 limits       v1                             true         LimitRange
persistentvolumeclaims      pvc          v1                             true         PersistentVolumeClaim
pods                        po           v1                             true         Pod
podtemplates                             v1                             true         PodTemplate
replicationcontrollers      rc           v1                             true         ReplicationController
resourcequotas              quota        v1                             true         ResourceQuota
secrets                                  v1                             true         Secret
serviceaccounts             sa           v1                             true         ServiceAccount
services                    svc          v1                             true         Service
controllerrevisions                      apps/v1                        true         ControllerRevision
daemonsets                  ds           apps/v1                        true         DaemonSet
deployments                 deploy       apps/v1                        true         Deployment
replicasets                 rs           apps/v1                        true         ReplicaSet
statefulsets                sts          apps/v1                        true         StatefulSet
localsubjectaccessreviews                authorization.k8s.io/v1        true         LocalSubjectAccessReview
horizontalpodautoscalers    hpa          autoscaling/v2                 true         HorizontalPodAutoscaler
cronjobs                    cj           batch/v1                       true         CronJob
jobs                                     batch/v1                       true         Job
leases                                   coordination.k8s.io/v1         true         Lease
endpointslices                           discovery.k8s.io/v1            true         EndpointSlice
events                      ev           events.k8s.io/v1               true         Event
helmchartconfigs                         helm.cattle.io/v1              true         HelmChartConfig
helmcharts                               helm.cattle.io/v1              true         HelmChart
addons                                   k3s.cattle.io/v1               true         Addon
pods                                     metrics.k8s.io/v1beta1         true         PodMetrics
ingresses                   ing          networking.k8s.io/v1           true         Ingress
networkpolicies             netpol       networking.k8s.io/v1           true         NetworkPolicy
poddisruptionbudgets        pdb          policy/v1                      true         PodDisruptionBudget
rolebindings                             rbac.authorization.k8s.io/v1   true         RoleBinding
roles                                    rbac.authorization.k8s.io/v1   true         Role
csistoragecapacities                     storage.k8s.io/v1              true         CSIStorageCapacity
ingressroutes                            traefik.containo.us/v1alpha1   true         IngressRoute
ingressroutetcps                         traefik.containo.us/v1alpha1   true         IngressRouteTCP
ingressrouteudps                         traefik.containo.us/v1alpha1   true         IngressRouteUDP
middlewares                              traefik.containo.us/v1alpha1   true         Middleware
middlewaretcps                           traefik.containo.us/v1alpha1   true         MiddlewareTCP
serverstransports                        traefik.containo.us/v1alpha1   true         ServersTransport
tlsoptions                               traefik.containo.us/v1alpha1   true         TLSOption
tlsstores                                traefik.containo.us/v1alpha1   true         TLSStore
traefikservices                          traefik.containo.us/v1alpha1   true         TraefikService
ingressroutes                            traefik.io/v1alpha1            true         IngressRoute
ingressroutetcps                         traefik.io/v1alpha1            true         IngressRouteTCP
ingressrouteudps                         traefik.io/v1alpha1            true         IngressRouteUDP
middlewares                              traefik.io/v1alpha1            true         Middleware
middlewaretcps                           traefik.io/v1alpha1            true         MiddlewareTCP
serverstransports                        traefik.io/v1alpha1            true         ServersTransport
serverstransporttcps                     traefik.io/v1alpha1            true         ServersTransportTCP
tlsoptions                               traefik.io/v1alpha1            true         TLSOption
tlsstores                                traefik.io/v1alpha1            true         TLSStore
traefikservices                          traefik.io/v1alpha1            true         TraefikService

# Not in a namespace
$ kubectl api-resources --namespaced=false
NAME                              SHORTNAMES   APIVERSION                             NAMESPACED   KIND
componentstatuses                 cs           v1                                     false        ComponentStatus
namespaces                        ns           v1                                     false        Namespace
nodes                             no           v1                                     false        Node
persistentvolumes                 pv           v1                                     false        PersistentVolume
mutatingwebhookconfigurations                  admissionregistration.k8s.io/v1        false        MutatingWebhookConfiguration
validatingwebhookconfigurations                admissionregistration.k8s.io/v1        false        ValidatingWebhookConfiguration
customresourcedefinitions         crd,crds     apiextensions.k8s.io/v1                false        CustomResourceDefinition
apiservices                                    apiregistration.k8s.io/v1              false        APIService
selfsubjectreviews                             authentication.k8s.io/v1               false        SelfSubjectReview
tokenreviews                                   authentication.k8s.io/v1               false        TokenReview
selfsubjectaccessreviews                       authorization.k8s.io/v1                false        SelfSubjectAccessReview
selfsubjectrulesreviews                        authorization.k8s.io/v1                false        SelfSubjectRulesReview
subjectaccessreviews                           authorization.k8s.io/v1                false        SubjectAccessReview
certificatesigningrequests        csr          certificates.k8s.io/v1                 false        CertificateSigningRequest
flowschemas                                    flowcontrol.apiserver.k8s.io/v1beta3   false        FlowSchema
prioritylevelconfigurations                    flowcontrol.apiserver.k8s.io/v1beta3   false        PriorityLevelConfiguration
etcdsnapshotfiles                              k3s.cattle.io/v1                       false        ETCDSnapshotFile
nodes                                          metrics.k8s.io/v1beta1                 false        NodeMetrics
ingressclasses                                 networking.k8s.io/v1                   false        IngressClass
runtimeclasses                                 node.k8s.io/v1                         false        RuntimeClass
clusterrolebindings                            rbac.authorization.k8s.io/v1           false        ClusterRoleBinding
clusterroles                                   rbac.authorization.k8s.io/v1           false        ClusterRole
priorityclasses                   pc           scheduling.k8s.io/v1                   false        PriorityClass
csidrivers                                     storage.k8s.io/v1                      false        CSIDriver
csinodes                                       storage.k8s.io/v1                      false        CSINode
storageclasses                    sc           storage.k8s.io/v1                      false        StorageClass
volumeattachments                              storage.k8s.io/v1                      false        VolumeAttachment
```

# Namespace vs Node

最後再釐清一下 Namespace & Node。
- Node 是實際的主機 (伺服器)，可以是實體或是虛擬的，Pod 運行在 Node 上。
- Namespace 是一種 Kubernetes 資源，是一個虛擬的隔離環境。
- 不同 Node 上面跑的 Pods 可屬於同一個 Namespace (ns level)， 也可屬於不同 Namespace (cluster level)
- 透過 Namespace 的方式可以做到資源隔離、資源管理，像是前面提到的 Resource Quota & LimitRange 就是 Namespace level objects。
- 另外在權限的控制上，可設置 Role 來操作特定 Namespace 中的特定資源。

---

_Reference_  
[https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)  
[https://kubernetes.io/docs/reference/kubectl/cheatsheet/](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)