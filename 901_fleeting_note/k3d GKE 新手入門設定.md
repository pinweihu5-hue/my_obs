[[k3d]]

---
還沒學到怎麼自架 kubernetes，不過雲端服務商都有提供代管 Kubernetes 的服務，AWS 有 EKS、Google Cloud 有 GKE、Azure 有 AKS ……

今天就來做些基礎的設定然後在 GKE 上建立個 Cluster。

首先要使用 GKE 必須先有 Google 帳號，新用戶啟用 Google 會送 300 美金，期限是 90 天。有些服務有 free tier，有的則是看它的收費標準，使用上還是要注意，不然 300 鎂一下就沒了。  
![[IMG-k3d GKE 新手入門設定-20241003104933958.png]]

點下 Start free 後，會先請你選擇國家 / 地區，第二步則是要輸入信用卡資料。一開始啟用 Google Cloud 會先幫你建個專案。在 Google Cloud 中服務 (或是說資源) 是有分層級的。  
![[IMG-k3d GKE 新手入門設定-20241003104948869.png]]

但個人帳戶只會有 Project level。

剛進去的頁面是網頁介面的控制台 (Console)，左上方可以選擇專案 (Project) 或新增專案。

左側的選單點開則是一排 Google Cloud 的服務。
**如果我們想要使用 GKE，必須先到 API 和服務 (API & Services) 開啟 k8s 的 API 服務**。
下面介面也可以看到你有多少抵免額、目前用了多少，以及到期時間。  
![[IMG-k3d GKE 新手入門設定-20241003105006604.png]]

# Create GKE Cluster by UI

進到 API 和服務 的介面後，選擇 [啟用 API 和服務]  
![[IMG-k3d GKE 新手入門設定-20241003105028832.png]]

搜尋 [Kubernetes Engine API]  
![[IMG-k3d GKE 新手入門設定-20241003105053836.png]]

選擇 [啟用]  
![[IMG-k3d GKE 新手入門設定-20241003105121181.png]]

啟動後就可以到左側的選單選擇 [Kubernetes Engine] 來建立 Cluster  
![[IMG-k3d GKE 新手入門設定-20241003105133296.png]]

點選叢集的 [建立] 就能建立 Cluster，不過這時會跳到 [建立 Autopilot Cluster] 的介面。
看了一下應該是 Google 會幫忙你管理 Node，讓開發者減少管理 cluster 資源的成本。

不過新手還是先從 Standard 開始。選擇右上的 [切換為 STANDARD 叢集]，自己設 Node。  
![[IMG-k3d GKE 新手入門設定-20241003105142227.png]]

基本資訊的部分要填 cluster name 以及設置位置。  
![[IMG-k3d GKE 新手入門設定-20241003105149708.png]]

**這邊地區 (Region) asia-east1 是台灣，台灣下面有三個區域 (Zone)。**
**預設 node pool 會是一個區域/Zone會有 3 個 nodes，所以這個地區 (Region) 就會有 9 個 nodes。**

另外最下面還會問你 kubernetes **版本要固定還是讓 GKE 自動管理**。
![[IMG-k3d GKE 新手入門設定-20241003105155554.png]]

如果想要配置 node，點選左邊的 [default-pool] 可做配置。這邊可以增加不同的 Node Pool，Node Pool 中的 Node 設置會是相同的。
底下我們把數量改成 1，練習的話可以先開 1 個 node。  
![[IMG-k3d GKE 新手入門設定-20241003105200933.png]]

點選 [Nodes] 的部分可以選規格。
等開建立 Cluster 後可以觀察到**這些 Node 就是一個個 VM**，可以另外在 [Compute Engine] 的頁面看到。  
![[IMG-k3d GKE 新手入門設定-20241003105209041.png]]

再來按下最下方的 [Create] 就能建立 Cluster 了。體感大概會花個 5 分鐘。

不過這邊可以先不要點選，接下來我們來用指令開 Cluster。
順帶一提，建立 Project & Enable API service 也可以用指令達成。

# Create GKE Cluster by gcloud CLI

## Install gcloud

網頁介面右上角其實有個 Cloud Shell 可使用，但…… 在本機裝 gcloud CLI 來操作 Google Cloud 應該比較快。

安裝參考官網文件 - [https://cloud.google.com/sdk/docs/install](https://cloud.google.com/sdk/docs/install)

使用 windows 的話建議就用 WSL 照 Linux 的方法安裝，不然 就是 會覺得痛苦 -.-

1. 找個位置下載 archive file
    
    ```bash
    # Linux 64-bit (x86_64)
    curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-447.0.0-linux-x86_64.tar.gz
    ```
    
2. 解壓縮
    
    ```bash
    tar -xf google-cloud-cli-447.0.0-linux-x86_64.tar.gz
    ```
    
3. 執行 Script
    
    ```bash
    ./google-cloud-sdk/install.sh
    ```
    

接下來執行 `gcloud init` 來 configure，會看到以下畫面  
![[IMG-k3d GKE 新手入門設定-20241003105213351.png]]

這時會需要認證身分。把顯示出來的網址貼到瀏覽器上，會得到一組 authorization code。輸入後會顯示 **[You are logged in as: [you-email]]**。

接下來會有一些互動式問題，問你要設置哪個專案 (如果只有一個好像就會是那一個)、default Compute Engine zone、default Compute Engine region。

總結下需要 configure 的部分有這些 -

```bash
gcloud init
gcloud config set project ${PROJECT_ID}
gcloud config set compute/zone <zone name>
gcloud config set compute/region <region name>
```

## Create cluster by command

再來就可以建立 cluster 了。

輸入以下指令表示要在 asia-east1-b 建立 cluster-1 這個 cluster，nodes number = 1

```bash
gcloud container clusters create cluster-1 --num-nodes=1 \
	--machine-type=e2-standard-2 --zone=asia-east1-b
```

開始建立  
![[IMG-k3d GKE 新手入門設定-20241003105216841.png]]

建立完成  
![[IMG-k3d GKE 新手入門設定-20241003105221421.png]]

不過這邊請再原諒我忘記順序，可能在執行前述指令時會有以下 output -

> CRITICAL: ACTION REQUIRED: gke-gcloud-auth-plugin, which is needed for continued use of kubectl, was not found or is not executable. Install gke-gcloud-auth-plugin for use with kubectl by following [https://cloud.google.com/blog/products/containers-kubernetes/kubectl-auth-changes-in-gke](https://cloud.google.com/blog/products/containers-kubernetes/kubectl-auth-changes-in-gke)

使用 Bard 幫忙產生 summary -

> 在 v1.26 版本中，Kubernetes 的開源版本將移除所有提供者特定的認證程式碼。這表示 GKE 用戶需要使用單獨的認證外掛程式來生成 GKE 特定的令牌。
> 
> Google 提供了一個新的 kubectl 外掛程式「gke-gcloud-auth-plugin」來支援此變更。這個外掛程式使用 Kubernetes Client-go 憑證外掛程式機制來擴展 kubectl 的認證以支援 GKE。
> 
> 因此，GKE 用戶需要安裝「gke-gcloud-auth-plugin」外掛程式，以便在 v1.26 版本中繼續使用 kubectl 對 GKE 叢集進行認證。
> 
> 具體來說，安裝「gke-gcloud-auth-plugin」外掛程式可以幫助您：
> 
> - 在 v1.26 版本中繼續使用 kubectl 對 GKE 叢集進行認證。
> - 避免在 v1.26 中進行變更時出現任何中斷。
> - 獲得一種簡單方便的方式對 GKE 叢集進行認證。

OK 所以就是為了使用 GKE，需要這個 extension 讓我們能用 kubectl 對 GKE cluster 進行認證。

這時請跟隨著指示，下載 `gke-gcloud-auth-plugin` -

```bash
gcloud components install gke-gcloud-auth-plugin
```

安裝完之後，**要怎麼讓我們能夠透過本機 kubectl 去得到 GKE cluster 的資訊呢？這時需更新 local kubeconfig 檔去授權**。

```bash
# gcloud container clusters get-credentials <cluster name> --zone <zone name>
gcloud container clusters get-credentials cluster-1 --zone asia-east1-b
```

執行完後就能使用 kubectl  
![[IMG-k3d GKE 新手入門設定-20241003105225292.png]]

如果想知道有哪些 clusters，可執行

```bash
gloud container clusters list
```

建立 pod 看看  
![[IMG-k3d GKE 新手入門設定-20241003105225395.png]]

可以**在網頁 Console 點選 Workloads，就能看到剛才建立的 Pod**  
![[IMG-k3d GKE 新手入門設定-20241003105228943.png]]

## 切換 context

因本機也有我們練習建的 cluster，如果我們想要操作本機的 k8s 該怎麼做呢？

可以使用以下指令：

```bash
# list contexts
kubectl config get-contexts
# change contexts
kubectl config use-context k3d-demo
```

列出有哪些 contexts，再選擇要切去哪一個  
![[IMG-k3d GKE 新手入門設定-20241003105232655.png]]

# THE MOST IMPORTANT THING

刪掉 cluster！！！！！！使用服務的時間都是要算錢的

```bash
gcloud container clusters delete cluster-1
```

**Cloud Shell 補充**

如果想要用 cloud shell 操作 GKE，直接用 apt 下載 kubectl

```bash
sudo apt install kubectl
```

然後一樣要 grant access

```bash
gcloud container clusters get-credentials <cluster name> --zone <zone>
```

---

_Reference_  
[https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy)  
[https://cloud.google.com/sdk/docs/install](https://cloud.google.com/sdk/docs/install)  
[https://cloud.google.com/kubernetes-engine/docs/deploy-app-cluster](https://cloud.google.com/kubernetes-engine/docs/deploy-app-cluster)