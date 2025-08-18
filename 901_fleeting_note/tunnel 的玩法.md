
related [[tunnel_idx]], [[Nginx]]

- [anderspitman/awesome-tunneling: List of ngrok/Cloudflare Tunnel alternatives and other tunneling software and services. Focus on self-hosting.](https://github.com/anderspitman/awesome-tunneling?tab=readme-ov-file)


# 1. 讓外網可以連接到本地服務


![[IMG-tunnel 的玩法-20241003104934052.png]]


- 跳板的概念
- 本地服務和vm中間建立通道 via ssh port 22
- vm 有 pub lP
- [[Nginx#my own ngrok]]


```sh

ssh -R 0.0.0.0:9090:localhost:8080 johnliu@external-server

# run at local, worked
ssh -i /Users/re4388/.ssh/gcp_20240406-011050 -R 3000:localhost:3000 re4388_103_0406@34.80.222.226 -N

# ps: 開一個 local server at 3000 for test
port-checker -port 3000

# 測試直接在遠端 cli裡面呼叫
curl localhost:8080

# 網頁需要確認你的 nginx 有轉發好，類似: 就可以瀏覽器打到 http://re4388.buzz/

$ cat vim /etc/nginx/sites-available/default

server {
        listen 80;
        server_name benhu.store;

	    location / {
		proxy_pass http://localhost:3000; # 80 proxy to 3000
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
	    }
}


# this work on GCP? ssh -R 0.0.0.0:5460:localhost:5432 re4388@re4388-swift-sf515-51t.tailbcab9.ts.net -N


```



# 2. 連到內網服務

![[IMG-tunnel 的玩法-20241003104948934.png]]



- 中間建立 tunnel 可以穿過 fw
- 這種又叫做 remote ssh
- 因為你像是 ssh into 進去的反過來，所以又叫做 reverse shell
- [ ssh tunnel at wemo use case](https://www.notion.so/nture4388/ssh-tunnel-at-wemo-use-case-4e140f5220464b77846fe9fbd31dd86b)

```sh

# -L: local 指定了本地端口轉發
# -N：這個參數告訴SSH客戶端不執行遠端命令，只進行端口轉發
ssh -L localhost:<local_Port>:<remote_ip>:<remote_port> <ssh_ueserName>@<ssh_domain_mahcine> -N


# worked
# -i 後面接 pub key path
# -N means no ssh into
# -L is local tunnel
ssh -i /Users/re4388/.ssh/gcp_20240406-011050 -L localhost:3001:34.80.222.226:3000 re4388_103_0406@34.80.222.226 -N

# or (use ssh config version)
ssh -L localhost:8001:35.229.200.24:8001 re4388_103_0406@gcpN2 -N




# tailscale
ssh -NL localhost:5460:100.87.63.33:5432 re4388@re4388-swift-sf515-51t.tailbcab9.ts.net
# how to stop?
# use prs or procs can find the process and kill it




```

# 3. 動態轉發到不同的內網服務 (via SOCKS Proxy)

![[IMG-tunnel 的玩法-20241003105011030.png]]


- notion 下面連接有 example


1. 用 gcp vm 裡面開了兩個服務
    
    1. 靜態服務聽 3000 [https://www.npmjs.com/package/serve](https://www.npmjs.com/package/serve)
    2. http server 聽 8080 [https://www.npmjs.com/package/http-server](https://www.npmjs.com/package/http-server)


2. 本機主打這個指令, 建立 socket 連接, 透過本地的 3000
```sh


gcloud compute ssh --zone "asia-east1-b" "instance-20240406-011050" --project "inner-topic-419501" -- -ND localhost:3000



or

ssh -D 3000 re4388_103_0406@gcpN2 -N

```


firefox 用 ext(比較方便)來透過 socket 連
![[IMG-tunnel 的玩法-20241003105037134.png]]


result:
can see 3000, and 8080
![[IMG-tunnel 的玩法-20241003105037379.png]]

and
![[IMG-tunnel 的玩法-20241003105102639.png]]


notion 更多資料: https://www.notion.so/nture4388/Tunnel-ssh-socket-tun-nc-0e49c25b40b74d4fabd9af89761c15ea?pvs=4








