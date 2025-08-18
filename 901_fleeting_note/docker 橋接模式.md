[[docker_idx]]
[[docker 網路模式]]


---


### 什麼是虛擬橋接網絡？

虛擬橋接網絡是一個軟件層面的網絡設置，類似於物理網絡中的交換機（Switch）。
它能夠在同一主機的多個容器之間進行網絡通信，並且提供host與容器之間的通信橋梁。


在 Docker 中，當你創建或啟動一個容器而沒有明確指定網絡模式時，容器會被自動分配到預設的虛擬橋接網絡。
通常情況下，Docker 會創建一個名為 `docker0` 的虛擬橋接器，並將容器連接到這個橋接器上。

### 工作原理

1. **橋接器（Bridge）**：
- `docker0` 是一個虛擬網橋，它創建了一個隔離的網絡環境，使多個容器可以通過它進行通信。橋接器負責在連接到它的各個網絡接口之間轉發數據包，就像物理網絡中的交換機一樣。
2. **虛擬以太網設備對（veth pairs）**：
- 每當你創建一個 Docker 容器時，Docker 會創建一對虛擬以太網設備（veth pairs），其中一端連接到容器內部，另一端連接到 `docker0` 網橋。這使得容器內部的網絡通信可以通過 `docker0` 網橋發送或接收數據。
3. **IP 地址分配**：
- Docker 通常會自動為每個容器分配一個 IP 地址，這些地址來自於由 `docker0` 使用的子網（通常是172.17.0.0/16，但可以根據需要配置）。

### 範例說明

假設你有兩個容器 `container1` 和 `container2`，它們都連接到預設的 `docker0` 網橋上：

1. 啟動兩個容器：
```sh

docker run -d --name container1 alpine sleep 1000
docker run -d --name container2 alpine sleep 1000

```

2. 檢查網絡設置：
- 使用 `docker network inspect bridge` 來檢查預設網絡（bridge）的設置：
- 這個命令會顯示所有連接到 `docker0` 網橋的容器及其分配的 IP 地址。你會看到類似以下的輸出：
```json

[
    {
        Name: 'bridge',
        Id: 'e1c9b...',
        Created: '2023-10-04T10:20:30.123456789Z',
        Scope: 'local',
        Driver: 'bridge',
        EnableIPv6: false,
        IPAM: {
            Driver: 'default',
            Options: null,
            Config: [
                {
                    Subnet: '172.17.0.0/16',
                    Gateway: '172.17.0.1'
                }
            ]
        },
        Containers: {
            a1b2c3d4e5f6: {
                Name: 'container1',
                EndpointID: '1234567890abcdef',
                MacAddress: '02:42:ac:11:00:02',
                IPv4Address: '172.17.0.2/16',
                IPv6Address: ''
            },

            fedcba0987654321: {
                Name: 'container2',
                EndpointID: 'abcdef1234567890',
                MacAddress: '02:42:ac:11:00:03',
                IPv4Address: '172.17.0.3/16',
                IPv6Address: ''
            }
        },
        Options: {
            'com.docker.network.bridge.default_bridge': 'true',
            'com.docker.network.bridge.enable_icc': 'true',
            'com.docker.network.bridge.enable_ip_masquerade': 'true',
            'com.docker.network.bridge.host_binding_ipv4': '0.0.0.0',
            'com.docker.network.bridge.name': 'docker0',
            'com.docker.network.driver.mtu': '1500'
        }
    }
]

```

3. 容器之間的通信：
- 現在，這兩個容器通過 `docker0` 網橋可以相互通信。你可以進入容器並使用 ping 命令進行測試：
```sh

docker exec -it container1 sh
ping 172.17.0.3 # 這是 container2 的 IP 地址

```

### 小結
虛擬橋接網絡是 Docker 的預設網絡環境，通過 `docker0` 虛擬橋接器實現容器之間的隔離與通信。
每個容器都有獨立的網絡接口和 IP 地址，共享同一個虛擬網橋。這種設定使得容器之間的網絡管理更加靈活和安全，且使用起來方便直觀。