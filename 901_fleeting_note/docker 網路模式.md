[[docker_idx]]



---
PS: [[[你知道 Docker 會讓 Linux 的 UFW 防火牆失效嗎？用 ufw-docker 解決此問題・Ivon 的部落格](https://ivonblog.com/posts/fix-ufw-docker/)]]


網絡模式可以用來控制容器如何與host以及其他container進行網絡通信。

Docker 提供了幾種不同的網絡模式，每種模式有其特定的用途和特性。



# Bridge 

橋接網路是部署容器時自動建立的預設網路。

橋接網路使用軟體橋接器，允許連接到同一橋接網路的容器進行通訊，在同一 **Docker 守護程式主機**上執行的容器上所使用的橋接網路。

橋接網路為主機建立一個私人內部隔離網絡，以便該網路上的容器可以進行通訊。


橋接網路提供網路隔離，為每個容器提供自己的 IP 位址和 DNS 配置。

連接到橋接網路的容器可以使用其 IP 位址或容器名稱進行通信，這要歸功於 Docker 的內建 DNS 伺服器。

此外，橋接網路允許容器透過 NAT 規則存取網際網路。

它通常用於本地容器通訊的開發和測試環境。

然而，對於更複雜的網路需求或跨多個主機的分散式系統，其他 Docker 網路模式可能是首選。
![[IMG-docker 網路模式-20241102140854624.png|594]]


- 可以通過以下命令啟動容器並使用預設的 Bridge 模式：
```sh

docker run mycontainer

```

- 或者顯式地指定：
```sh

docker run --network bridge mycontainer

```


also see [[docker 橋接模式]]

# Host


Docker 中的主機網路模式允許容器直接共用主機的網路堆疊，繞過 Docker 的網路隔離。
在這種模式下，容器使用主機的 IP 位址、網路介面和路由表，從而提高網路效能。
主機網路上的容器可以存取外部網路和服務，而無需網路位址轉換（NAT）或連接埠對映的開銷。

但這種模式缺乏容器之間的網路隔離，可能不適合需要嚴格網路隔離的場景。
主機網路通常用於需要最大網路效能或直接存取主機網路介面的應用程序，例如網路密集型服務或遺留應用程式。

```sh

docker run --network host mycontainer

```



# None

- **None 模式會禁用容器的網絡堆棧**。
- 容器將不會有任何網絡接口（除了 `lo` 回環接口）。
- 這種模式適用於不需要網絡功能的工作負載，或者需要手動配置網絡的情況。
- 啟動容器時使用 `--network none` 來指定 None 模式：
```sh

docker run --network none mycontainer

```


# Container

- Container 模式**允許一個容器共享另一個容器的網絡堆棧**。
- 這種模式非常適合需要緊密集成的多容器應用，例如需要與主應用容器共享網絡環境的側車（Sidecar）模式。
- 啟動容器時使用 `--network container:<container>` 來指定共享的容器：
```sh

docker run --network container:myothercontainer mycontainer

```


# Overlay 

如果節點 A 上的容器想要與節點 B 通信，則使用Overlay network，然後為了在它們之間進行通信，我們使用Overlay network。 

Overlay 網路使用 **VXLAN 創建 Overlay 網路**。這樣做的優點是可以在各種雲端和本地網路之間提供最大的可移植性。預設情況下，Overlay 網路使用 **AES 演算法**加密。


Docker 中的Overlay network，尤其是 Docker Swarm 中的Overlay network，用於促進 Swarm 叢集中不同 Docker 主機或節點之間的容器之間的通訊。當一個節點（假設節點 A）上的容器需要與另一個節點（節點 B）上的容器通訊時，覆蓋網路就會發揮作用。


Overlay network利用 VXLAN（虛擬可擴展 LAN）等技術來建立跨越 Swarm 叢集中所有節點的虛擬網路覆蓋範圍。這種Overlay network可以實現容器之間的無縫通信，無論其實體主機如何，為分散式應用程式提供統一的網路環境。
![[IMG-docker 網路模式-20241102140854769.png]]


- 創建一個 Overlay 網絡：
```sh

docker network create -d overlay myoverlay

```

- 啟動容器並使用這個 Overlay 網絡：
```sh

docker run --network myoverlay mycontainer

```




# Mac vlan

Macvlan 網路用於將應用程式直接連接到實體網路。

透過使用 macvlan 網路驅動程式為每個容器**分配 MAC 位址**，還允許擁有完整的 TCP/Ip 堆疊。

然後， **Docker 守護程式透過容器的 MAC 位址將流量路由**到容器。您可以使用不同的實體網路介面隔離 macvlan 網路。

這用於需要 MAC 位址的遺留應用程式。


**Docker 中的 Macvlan 網路是一種為每個容器提供自己的 MAC 位址的模式**，使它們在網路上顯示為單獨的實體裝置。

這使得容器能夠直接與外部網路通信，而無需透過 Docker 主機的網路堆疊。 

Macvlan 網路對於容器需要直接存取底層網路基礎架構的場景非常有用，例如執行需要公開特定連接埠的網路密集型應用程式或服務時。
與橋接網路相比，它提供了改進的網路效能、隔離性和靈活性，使其適合需要容器到網路通訊而無需網路位址轉換 (NAT) 開銷的應用程式。

![[IMG-docker 網路模式-20241102140854945.png|546]]


# ip vlan

![[IMG-docker 網路模式-20241102140855027.png]]

IPvlan 是 Docker 中的一種網路模式，它允許容器直接在物理網路上進行通信，而不需要通過 Docker 的橋接網路。IPvlan 網路模式主要用於需要高效能和低延遲的網路環境，特別是在大型微服務架構中。

### IPvlan 的特點：

1. **直接連接**：容器可以直接使用主機的網路接口進行通信，這意味著每個容器可以獲得一個獨立的 IP 地址，並且可以直接與外部網路進行通信。
2. **更好的性能**：由於容器不需要通過 Docker 的橋接網路進行通信，因此在某些情況下，IPvlan 可以提供更好的性能和更低的延遲。
3. **簡化的網路管理**：IPvlan 允許更簡單的網路管理，因為它可以利用現有的網路基礎設施，並且不需要額外的網路轉發。
4. **兩種模式**：IPvlan 有兩種模式：
    - **L2 模式**：在此模式下，容器在同一個二層網路上，並且可以直接與同一網段的其他設備通信。
    - **L3 模式**：在此模式下，容器在三層網路上運行，並且需要通過路由器進行通信。

### 使用 IPvlan 的情況：
- 當需要容器擁有獨立的 IP 地址以便於與其他服務或設備進行直接通信時。
- 當需要提高網路性能並降低延遲時。
- 在對網路隔離和安全性有較高要求的環境中。

### 如何在 Docker 中使用 IPvlan：
可以通過 Docker 命令行創建 IPvlan 網路，例如：

```bash

docker network create -d ipvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=eth0 ipvlan_net

```

這條命令創建了一個名為 `ipvlan_net` 的 IPvlan 網路，使用 `eth0` 作為父接口，並指定了子網和網關。
總之，IPvlan 是一種高效的網路模式，適合於需要直接網路訪問和高性能的 Docker 應用場景。




# 總結

Docker 提供了多種網絡模式以滿足不同類型的應用需求：
- **Host 模式**：直接使用host的網絡接口，適用於高性能應用，但缺乏隔離性。
- **Bridge 模式**：容器獲得獨立的網絡接口和 IP 地址，適合測試和簡單應用。
- **None 模式**：禁用網絡堆棧，適用於不需要網絡功能的情況。
- **Container 模式**：共享另一個容器的網絡堆棧，適用於需要緊密集成的多容器應用。
- **Overlay 模式**：當我們需要跨多個 host 主機的網路應用。
- 

根據應用需求選擇合適的網絡模式，可以有效提升應用運行的性能和安全性。