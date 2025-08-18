[] [] [[k3d]]

[Day 3 First Pod & Kubernetes Dashboard - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10320337)


因 Pod 是在 k8s cluster 的內部網路，外部是無法訪問的。
如果要讓外面的流量可以到達 Pod 可以利用一些方式
這邊先使用 port-forward 的方式讓主機能夠訪問 Pod 。

PS: 但一般的作法會是建立 Service 這個 kubernetes object，Service 會是將流量導入 Pod 的接口

```sh

## 用動態 port forwarding 把 linux 上的 localhost 轉發到 firefox


# port forward pod port to vm localhost
kubectl port-forward nginx-pod-yaml 3000:80

# mac上動態轉發 gcp vm 上的 port to local via sock5
[] ]
[] 
```

用 firefox 的 套件 FoxyProxy
![[IMG-k3d port forward-20241003104933988.png]]
![[IMG-k3d port forward-20241003104948893.png]]