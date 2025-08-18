[[k3d]]

k3d: k3s in docker




[Day 2 本機環境安裝 K3D & kubectl - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10319629)

```bash


# k3d install
wget -q -O - https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash # or curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash

# k3d create cluster
k3d cluster create


k cluster-info

# kubctrl to get nodes
kubectl get nodes
k get pods


# 建立方法
k run nginx-pod --image=nginx
# or
k apply -f <filename>


# list objects
kubectl get services # list all services in the current namespace (defined in config file or default)
kubectl get pods -A # list all pods in all namespace, -A = --all-namespaces
kubectl get pods -o wide # list all pods in the current namespace, with more details
kubectl get nodes -o wide # list all nodes with more details

# describe - view the details
kubectl describe pods my-pod
kubectl describe service mysql-service -n app # view the details of mysql-service service in app namespace

# delete
kubectl delete pod,service baz foo # delete pods and services with same names "baz" and "foo"

# view logs of pod
kubectl logs <pod name>


```