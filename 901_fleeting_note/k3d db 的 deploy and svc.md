
[[k3d]]



---


- [x] 砍掉 ns 重新建立
- [x] 這次只建立 app and secret

---


[Day 5 利用 Deployment 及 Service 部署服務 - 2 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10323111)


db 的 deploy
```yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: ironman
  name: db-deploy
  labels:
    name: db-deploy
    app: prac-node-login
spec:
  replicas: 1
  selector:
    matchLabels:
      name: db-pod
      app: prac-node-login
  template:
    metadata:
      name: db-pod
      labels:
        name: db-pod
        app: prac-node-login
    spec:
      nodeSelector:
        role: "agent"
      containers:
        - name: db
          image: mysql:8.1.0
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-secrets
                  key: MYSQL_ROOT_PASSWORD
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  name: db-secrets
                  key: MYSQL_INIT_DATABASE

---
# using --- to separate different spec, then they can be in the same yaml file
apiVersion: v1
kind: Secret
metadata:
  namespace: ironman
  name: db-secrets
  labels:
    name: db-secrets
    app: prac-node-login
type: Opaque
data:
  MYSQL_ROOT_PASSWORD: cm9vdHB3ZDEyMzQ=
  MYSQL_INIT_DATABASE: dGVzdA==


```

port 的部分 MySQL 預設會使用 3306，可以從 docker hub 上的 image tag 點進去看 dockerfile，就能知道當初 build image 時開哪個 port。

環境變數的部分這邊用另一種方式去 refrence secret，可以定義 env name，然後引用 Secret 裡面的 key-value pair。

因 MySQL 啟動一定要給 root password，不然會失敗。
這邊還定義了 MYSQL_DATABASE 這個環境變數，在啟動 MySQL 的時候就建這個 DB。

這些資訊都可以從 docker hub 上得到 → [https://hub.docker.com/_/mysql](https://hub.docker.com/_/mysql)

```sh

~/simple-login on  main! ⌚ 5:26:39
$ echo -n 'cm9vdHB3ZDEyMzQ=' | base64 --decode
rootpwd1234%

~/simple-login on  main! ⌚ 5:26:55
$ echo -n 'dGVzdA==' | base64 --decode
test%

```


svc
```yaml

apiVersion: v1
kind: Service
metadata:
  namespace: ironman
  name: db-service
  labels:
    name: db-service
    app: prac-node-login
spec:
  # type: ClusterIP
  selector: # map the pod label
    name: db-pod
    app: prac-node-login
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306


```


MySQL 接的 Service 就不是 Node Port 這種類型了。

**service 如果沒有寫 Type 預設會是 ClusterIP。**
**ClusterIP 這個 Service 在 Cluster 中會被分配一個 IP，可以被 Cluster 中的其他 Service / Pod 訪問，但沒有開放給集群外部。**


前面提過，Pod 的 IP 會變，所以 Cluster 中 Pod 跟 Pod 的溝通還是要透過 Service。

而 Cluster IP 就能在這時候派上用場。**以資料庫的應用場景來看，我們不會希望外界的人能夠訪問資料庫，所以 DB 不會開 Node Port ，而是讓其他應用在使用 DB 時透過 Cluster IP Service 連接**。


那 web server 是透過什麼資訊才能知道要去哪邊連 MySQL 的呢？  
web server 使用的 secret ，裡面設的 `MYSQL_HOST` 要跟 DB pod 掛的 service name 一樣！
用 base64 decode 會發現這兩邊是對應的，並且要在相同的 namespace 中。

~/simple-login on  main! ⌚ 5:31:04
$ echo -n 'ZGItc2VydmljZQ==' | base64 --decode
db-service%


![[IMG-k3d db 的 deploy and svc-20241003104933984.png|390]]

![[IMG-k3d db 的 deploy and svc-20241003104948889.png]]
app 那邊 svc 有設定好 nodePort, svc and 8000 的 app target port
然後 app 那邊 config 有對上 mssql svc 的 name
mysql svc 連到 mysql pod via cluster ip 


準備好了，可以 apply!
```

~/simple-login/k8s on  main! ⌚ 5:34:23
$ k apply -f db-deploy.yaml 
deployment.apps/db-deploy created
secret/db-secrets created

~/simple-login/k8s on  main! ⌚ 5:34:34
$ k apply -f db-service.yaml 
service/db-service created


$ kubectl get all -n ironman
NAME                                    READY   STATUS    RESTARTS   AGE
pod/login-app-deploy-7675c7dcf4-r8x7j   1/1     Running   0          20m
pod/login-app-deploy-7675c7dcf4-wbbh8   1/1     Running   0          20m
pod/db-deploy-869b6bd85f-gmfww          1/1     Running   0          22s

NAME                        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/login-app-service   NodePort    10.43.59.165   <none>        3000:30001/TCP   19m
service/db-service          ClusterIP   10.43.144.9    <none>        3306/TCP         15s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/login-app-deploy   2/2     2            2           20m
deployment.apps/db-deploy          1/1     1            1           22s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/login-app-deploy-7675c7dcf4   2         2         2       20m
replicaset.apps/db-deploy-869b6bd85f          1         1         1       22s




kubectl exec -it -n ironman pod/db-deploy-869b6bd85f-gmfww -- mysql -uroot -p


pass is rootpwd1234


mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test               |
+--------------------+


mysql> use test;
Database changed
mysql> CREATE TABLE user (
    ->   id INT AUTO_INCREMENT,
    ->   name VARCHAR(70) NOT NULL CHECK (name <> ''),
    ->   email VARCHAR(200) NOT NULL CHECK (email <> ''),
    ->   password VARCHAR(100) NOT NULL CHECK (password <> ''),
    ->   created TIMESTAMP DEFAULT NOW(),
    ->   PRIMARY KEY(id)
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql> desc user;
+----------+--------------+------+-----+-------------------+-------------------+
| Field    | Type         | Null | Key | Default           | Extra             |
+----------+--------------+------+-----+-------------------+-------------------+
| id       | int          | NO   | PRI | NULL              | auto_increment    |
| name     | varchar(70)  | NO   |     | NULL              |                   |
| email    | varchar(200) | NO   |     | NULL              |                   |
| password | varchar(100) | NO   |     | NULL              |                   |
| created  | timestamp    | YES  |     | CURRENT_TIMESTAMP | DEFAULT_GENERATED |
+----------+--------------+------+-----+-------------------+-------------------+
5 rows in set (0.00 sec)


```



singUp 看看
![[IMG-k3d db 的 deploy and svc-20241003105007958.png|454]]
pass:
W4n.3*3y3VJ5i6#




重建 token since it is short term
k -n kubernetes-dashboard create token admin-user