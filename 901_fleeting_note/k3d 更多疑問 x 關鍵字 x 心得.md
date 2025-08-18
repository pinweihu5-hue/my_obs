[[k3d]]

---
終於來到第 30 天，結束官網英翻繁中的日子。原本想說最後是不是要再找一個相關的應用來研究，不過最後一天還是來回顧一下前面文章留下的各種疑問以及待研究的問題 / 主題。

- 自己架 Cluster (by kubeadm)
    - 如何配置 Container runtime、Kubelet、Kube-proxy
    - 如何配置 Kube API server、ETCD、Kube Scheduler、Kube Controller Manager
- Pod 的更多配置，PriorityClass、Networking 相關 (hostname, hostAliases)、Security context …
- Rolling Update & Rollback、部署方式及災難復原
- Deployment revision history 存在哪 - 應該是 etcd 🤔 ?
- 競爭 Resource 的情況、OOM Killer、如果 OOK Killer 殺掉的 Process 不是 Container 中 `PID=1` 的 Process 會發生什麼事
- Pod Quality of Service Classes
- PodDisruptionBudget
- Multi-Container 的設計模式，例如 sidecar、adapter、ambassador …
- Secret - Opaque 以外的 Secret 類型，用在哪邊、怎麼使用
- Secret 管理，Helm Secrets、HashiCorp Vault
- HPA 的 algorithm & behavior 跟實測觀察到的 Pod 數量變化不一致 - 不確定是不是我理解錯誤
- Service & Kubernetes 網路
    - Endpoints & EndpointSlice - 目前建立 Service 還是會同時建立這兩個 Object，但根據官網 Endpoints 能存的資訊有限所以才推出 EndpointSlice，那這兩個為何不是互相取代的關係
    - Kubernetes 網路架構，底層基礎建設是怎麼建的
    - Kubernetes 內建網路是什麼
    - Network Policy
    - Container Network Interface (CNI) 是什麼
    - Gateway API
    - kube-proxy 如何實現網路代理 (IPVS vs iptables)
- 如何做到
    - 所有 Pod 都能在不使用 NAT 的情況與其他所有 Pod 溝通
    - 所有 Node 都能在沒有 NAT 的情況與所有 Pod 溝通
    - Pod 認為自己的 IP 與其他人認為的 IP 相同
- 如何實現
    - Pod 之中，Container & Container 之間的溝通
    - Pod 與 Pod 之間的溝通
    - Pod 與 Service 的溝通
    - 外部到 Service 的溝通
- Docker networking
- 網路相關的關鍵字
    - DNS
    - Network Namespaces
    - NAT、SNAT、DNAT
    - WAN
    - Routing Table
    - Switch
    - Router
    - DHCP Server
    - iptables/ipvs
    - Veth, Bridge
    - CIDR
    - Overlay
    - Bridge
- Storage Class provisioner, e.g. AWS EBS, GCE PD, NFS, Ceph …
- Ingress Controller 的使用 - Ingress-Nginx Controller、Traefik、HAProxy etc
- Kubernetes 中的 TLS certifiactes
- 更深入了解 Kubernetes Authorization & Authentication、權限控管、X.509 certs、OpenID
- Helm chart 怎麼寫
- Open Container Initiative (OCI) 是什麼
- Prometheus + Grafana 的監控方案
    - Exporter 怎麼設、要傳什麼 metrics
    - PromQL
    - 如何設定 Alert
- Custom Resource Definition (CRD)
- KEDA 接 Scaler 的應用 (例如接 AWS SQS、Redis 是什麼情境
- Loki、Fluent Bit
- ELK stack in Kubernetes Cluster
- Rancher Kubernetes Engine (RKE) 的使用
- 雲地混和的管理
- Velero
- Istio
- ArgoCD
- GitOps

以上，可以看到轉職仔在電腦科學的背景十分缺乏，很多 Networking 相關的名詞都不懂，繼續努力。

以下心得：

一開始看到鐵人賽報名的訊息，心裡想著要寫 30 天我要寫什麼內容、什麼都還不懂的我能寫嗎。不過看到某些文章篇幅也是滿短的，那當作自我學習的筆記應該還行吧。剛好 DevOps 的職位需要學習 Kubernetes，腦衝就報名了。第一天連大綱都沒排好，後續也是各種更動，還有不少主題分成兩篇因為下班太累寫不了太多，水了好幾天。但完賽就是開心 🥰 中文跟英文都爛導致寫文章都不知道在用哪邊的文法的我，竟然連續發文 30 天，真的是痛哭流涕。

這 30 篇內容算是從 Bootcamp 畢業後上工兩個月的學習總結，大部分文章只有介紹怎麼在本地端建立 Kubernete object 或實作些小應用，但原理、應用場景、可能會遇到什麼問題是滿缺乏的，希望未來能理解的更深入。很多篇近似將官網內容英翻繁中，不過我還是盡力在每個主題把我自己學習時可能卡住的細節寫清楚，如果有人看的話希望能幫助也是新手的你。

插播一下廣告。在 Appworks School 時很幸運地從導師小賴那學到一些 Docker 的基本概念，在學習 Kubernetes 時可更快進入狀況。推薦各位購買小賴的新書 - [Docker 實戰 6 堂課：56 個實驗動手做，掌握 Linux 容器核心技術](https://www.tenlong.com.tw/products/9786263335769)，這也是 2022 鐵人賽 DevOps 組的冠軍系列文！

最後感謝建議我參加鐵人賽的同學、鼓勵我的人、讀者，還有我自己！