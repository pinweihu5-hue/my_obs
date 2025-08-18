[[k3d]]

---

主題 - 在 Kubernetes 中安裝 Prometheus 來監控 Cluster 的運作情形。

# 簡介 Prometheus

Prometheus 是一個開源的、基於時序資料的監控警報工具，起源是來自 SoundCloud 這間公司。
Prometheus 在 2016 年加入 CNCF (Cloud Native Computing Foundation，由 Linux Foundation 建立的專案，推動雲端原生技術的發展)。
Kubernetes 是第一個加入 CNCF 的專案，Prometheus 是第二個。

那所謂監控要怎麼監控，還有監控工具需要什麼？

我理解上是，例如要監控一台主機 (CPU、Memory、Disk usage…)，這個監控的 Server 會需要得到監控對象的資料。
那有兩種方式可以讓監控 Server 得到主機資料，一種是用 Push (推的)，一種是用 Pull (拉的)。
Push model 會需要在監控的對象 (e.g. 主機) 上另外跑一個程式，主動將監控的資料 Push 到監控 Server；
Pull model 則是由監控 Server 定期去 Pull 監控對象的資料。

得到資料後最重要的還是要讓開發者、維運人員知道目前數值多少、哪時候出現異常，
所以通常會需要可視化的工具將這些監控資料視覺化，然後能夠設定閾值接上警報系統，當異常值出現能告警。

底下是 Prometheus 的架構  
![[IMG-k3d  Monitor Cluster - 安裝 Prometheus-20241003104948857.png]]

可以分成幾個區塊 -

- 左下是監控對象 (Prometheus Targets)，要有 Exporters 蒐集 Metrics
- 左上 Short-lived jobs + Pushgateway
- 中間 Prometheus Server，包含 Retrieval、DB、Web Server
- 右上 Alertmanager
- 右下視覺化工具，包含 Prometheus 自己的 Web Interface、Grafana
- 中間上面是服務發現，用來動態的監視目標。在 Kubernetes 中的話會需要讓 Prometheus 與 Kubernetes API 能夠溝通，才能讓 Prometheus 自動發現有哪些資源被部署

這邊推薦看影片 - [https://youtu.be/h4Sl21AKiDg?si=v5eoq_txoh_MD_66](https://youtu.be/h4Sl21AKiDg?si=v5eoq_txoh_MD_66)



但今天安裝後我會看的東西只有其中幾項，所以我手動先把一些 componenets 蓋掉，比較好理解  
[[IMG-k3d  Monitor Cluster - 安裝 Prometheus-20241003104933942.png|Open: CleanShot 2024-06-16 at 20.31.27.png]]
![[IMG-k3d  Monitor Cluster - 安裝 Prometheus-20241003104933942.png]]


- Prometheus server 會包含 Retrieval，專門去拉取(pull)監控對象的資料。
	- 這些數據被稱作 metrics。
	- 左下角的 Targets 會搭配 Exporters 把監控對象的數據蒐集好並轉換成 Prometheus 使用的格式。
- Retrieval 拉到資料後，存到 TSDB (Time Series Database)。
- Metrics 舉幾個例子來理解，例如 CPU status, Memory Usage, Requests Count, Requests Duration …。
	- Prometheus Metrics Format 是 Human-readable text-based，有兩個屬性：HELP & TYPE。
	- HELP 是敘述 Metric 是什麼；TYPE 則是不同的數據概念，有 Counter, Gauge, Histogram 三種。
- Prometheus 也有提供 web UI 做 Data visualization & export，這邊 Prometheus 有個 HTTP Server 就會去 DB 拉資料。
	- 而視覺化工具 (Prometheus web UI & Grafana) 會透過 PromQL 這種查詢語法來向 HTTP Server 要資料，再呈現出來

接下來來安裝。

# Install Prometheus by Helm

Prometheus 支援多種安裝方式，我們可以用 Helm 來安裝已經包好的監控組合。

上 Artifact Hub 找 Prometheus，不意外會找到一堆 charts。這邊我拿 Prometheus Community 上傳的 charts 來安裝，可以點進去看這個 charts 包含哪些 dependencies。
以圖中兩個 charts 來說，上面那種有裝 grafana，下面那種沒有。今天就裝 kube-prometheus-stack 這個 chart。  
[kube-prometheus-stack 60.1.0 · prometheus/prometheus-community](https://artifacthub.io/packages/helm/prometheus-community/kube-prometheus-stack)
![[IMG-k3d  Monitor Cluster - 安裝 Prometheus-20241003105005906.png]]

執行以下指令

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm show values prometheus-community/kube-prometheus-stack > custom-prometheus-values.yaml
```

這邊我就改 Timezone，然後看一下預設的 grafana 登入密碼

```yaml
# custom-prometheus-values.yaml
grafana:
  enabled: true
  ## Timezone for the default dashboards
  ## Other options are: browser or a specific timezone, i.e. Europe/Luxembourg
  defaultDashboardsTimezone: Asia/Taipei
  adminPassword: prom-operator
```

安裝，一分鐘內會裝好

```bash
helm install prometheus prometheus-community/kube-prometheus-stack -f custom-prometheus-values.yaml -n monitoring --create-namespace
```

看一下裝了哪些東西 -  

k get all -n monitoring

![[IMG-k3d  Monitor Cluster - 安裝 Prometheus-20241003105027628.png]]

接下來可以用 `port-forward` 的方式來看一下 Web 介面

```bash
kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-prometheus 9090:9090 &
kubectl port-forward -n monitoring svc/prometheus-grafana 3000:80 &
```

開瀏覽器訪問 localhost:9090 可以看到 Prometheus Web UI  
![[IMG-k3d  Monitor Cluster - 安裝 Prometheus-20241003105052534.png]]

這邊可以下 PromQL 得到 metrics。

再來訪問一下 localhost:3000 進入 Grafana，帳號:pass
admin:prom-operator
![[IMG-k3d  Monitor Cluster - 安裝 Prometheus-20241003105120483.png]]

進去後選擇左邊的 Dashboards，點開 General Folder，可以看到底下已經有多個 sub folders，我先選擇 [Kubernetes / Compute Resources / Cluster] 這個  
![[IMG-k3d  Monitor Cluster - 安裝 Prometheus-20241003105132890.png]]

可以看到不同 Namespace 的資料，也可看 Namespace 下所有 Pods metrics、單一個 Pod 的 metrics。

Alert manager 的部分預設會啟用，如果想要設定可以 port-forward 9093 看 UI -

```bash
kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-alertmanager 9093:9093 &
```

可以測試啟幾個 Pods、對 Pods 發送請求，Prometheus 會自動抓到這些 Resources 顯示在 Grafana 上。

# Add MySQL Exporter

前面說要對監控的對象放 Exporter，Prometheus Server 才能蒐集 Metrics。
點選上方的 Status，可以選擇 Service Discovery 看目前有蒐集哪些資訊 -

前一天透過 Helm 裝 Bitnami 的 MySQL，Bitnami 也有提供 Prometheus exporter 的設置。

直接來改 values.yaml，增加 metrics 的部分

```yaml
global:
  imageRegistry: ""
  imagePullSecrets: []
  storageClass: ""

## @param architecture MySQL architecture (`standalone` or `replication`)
architecture: replication

## MySQL Authentication parameters
auth:
  rootPassword: "test1234"
  createDatabase: true
  database: "ironman"
  replicationUser: replicator
  replicationPassword: "replicationtest"

primary:
  name: primary
  persistence:
    enabled: true
    storageClass: ""

secondary:
  name: secondary
  replicaCount: 2
  persistence:
    enabled: true
    storageClass: ""

## 參考下面的設定，修改既有的設定
metrics:
  enabled: true
  serviceMonitor:
    enabled: true
    labels:
      release: prometheus
```

這邊需 enabled `metrics`，到時候會建一個 mysql-metrics service，port 開在 9104。

`serviceMonitor` 也要 enabled，這樣 Prometheus 才找得到這個 metrics。最重要的是，**labels 要加上 `release: prometheus`，不然 Prometheus 會找不到！**

來源參考 - [https://lyz-code.github.io/blue-book/devops/prometheus/prometheus_troubleshooting/](https://lyz-code.github.io/blue-book/devops/prometheus/prometheus_troubleshooting/)

我有嘗試過 upgrade release，但會出錯。所以就 Uninstall 後再重新 apply new values 安裝
also see: [[k3d Package Manager for Kubernetes - Helm & 用 Helm 安裝 MySQL Cluster]]

```bash
helm install mysql bitnami/mysql -f day24/custom-mysql-values.yaml -n db
```

底下的 Tips 會多出一段

```bash
To access the MySQL Prometheus metrics from outside the cluster execute the following commands:

    kubectl port-forward --namespace db svc/mysql-metrics 9104:9104 &
    curl http://127.0.0.1:9104/metrics
```

剛才提到的 Service Monitor 也是一個 Objects，但這是自定義的 (CustomResourceDefinition, CRD)。可以用來擴展 Kubernetes。
可以看到剛才加的 label `release: prometheus` 有出現在 mysql 這個 Service Monitor 中。  


```sh

$ k get servicemonitors.monitoring.coreos.com -A
NAMESPACE    NAME                                                 AGE
monitoring   prometheus-prometheus-node-exporter                  36m
monitoring   prometheus-kube-prometheus-coredns                   36m
monitoring   prometheus-kube-prometheus-operator                  36m
monitoring   prometheus-grafana                                   36m
monitoring   prometheus-kube-prometheus-alertmanager              36m
monitoring   prometheus-kube-prometheus-prometheus                36m
monitoring   prometheus-kube-prometheus-kubelet                   36m
monitoring   prometheus-kube-prometheus-kube-etcd                 36m
monitoring   prometheus-kube-prometheus-kube-controller-manager   36m
monitoring   prometheus-kube-prometheus-apiserver                 36m
monitoring   prometheus-kube-state-metrics                        36m
monitoring   prometheus-kube-prometheus-kube-proxy                36m
monitoring   prometheus-kube-prometheus-kube-scheduler            36m
db           mysql                                                80s  <----

```

```sh

$ k describe -n db servicemonitors mysql
Name:         mysql
Namespace:    db
Labels:       app.kubernetes.io/instance=mysql
              app.kubernetes.io/managed-by=Helm
              app.kubernetes.io/name=mysql
              app.kubernetes.io/version=8.4.0
              helm.sh/chart=mysql-11.1.2
              release=prometheus
Annotations:  meta.helm.sh/release-name: mysql
              meta.helm.sh/release-namespace: db
API Version:  monitoring.coreos.com/v1
Kind:         ServiceMonitor
Metadata:
  Creation Timestamp:  2024-06-16T13:20:39Z
  Generation:          1
  Resource Version:    9833
  UID:                 2d1f8966-9b08-46d0-86fe-9c732e09bdab
Spec:
  Endpoints:
    Interval:  30s
    Port:      metrics
  Job Label:
  Namespace Selector:
    Match Names:
      db
  Selector:
    Match Labels:
      app.kubernetes.io/component:  metrics
      app.kubernetes.io/instance:   mysql
      app.kubernetes.io/name:       mysql
Events:                             <none>


```


如果執行 mysql-metrics port-forward，可以在 `localhost:9104` 看到 mysql metrics  
![[IMG-k3d  Monitor Cluster - 安裝 Prometheus-20241003105141798.png]]

在 Prometheus 的 Targets 或是下 query 的地方也能看到 MySQL -  
![[IMG-k3d  Monitor Cluster - 安裝 Prometheus-20241003105149370.png]]

---

Grafana 中應該也要能看到 MySQL metrics

![[IMG-k3d  Monitor Cluster - 安裝 Prometheus-20241003105155277.png|1076]]


_Reference_  
[https://prometheus.io/docs/introduction/overview/](https://prometheus.io/docs/introduction/overview/)  
[https://artifacthub.io/packages/helm/prometheus-community/kube-prometheus-stack](https://artifacthub.io/packages/helm/prometheus-community/kube-prometheus-stack)  
[https://yunlzheng.gitbook.io/prometheus-book/part-iii-prometheus-shi-zhan/readmd/service-discovery-with-kubernetes](https://yunlzheng.gitbook.io/prometheus-book/part-iii-prometheus-shi-zhan/readmd/service-discovery-with-kubernetes)  
[https://www.modb.pro/db/209851](https://www.modb.pro/db/209851)  
[https://lyz-code.github.io/blue-book/devops/prometheus/prometheus_troubleshooting/](https://lyz-code.github.io/blue-book/devops/prometheus/prometheus_troubleshooting/)  
[https://www.cnblogs.com/liugp/p/16721252.html](https://www.cnblogs.com/liugp/p/16721252.html)