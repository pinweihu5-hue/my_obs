
related: 
- [[caddy]]
- [[Zoraxy]]
- [[nginx load balancing 的配置 example]]
- [[nginx 要如何設定才可以預防潛在的 DDOS 攻擊]]
- [[SSL Termination in nginx]]



# Nginx 功能大全
[Can Nginx still play like this? ](https://www.youtube.com/watch?v=VjJmEf8muow&list=WL&index=13&t=1s)
- 常用操作
	- 靜態文件架站

![[IMG-Nginx-20250811203626427.png]]

![[IMG-Nginx-20250811203627423.png]]

- 反向代理 (還可以做緩存，ssl 加密，日誌記錄)
	- 更安全，前端也不會直接打到後面的 server
	- 也可以用來解決跨域問題，因為可以讓前端和後端的域名統一

![[IMG-Nginx-20250811203628491.png]]

- 負載均衡
[[nginx load balancing 的配置 example]]
![[IMG-Nginx-20250811203629745.png]]


- 改寫請求response
	- 控制瀏覽器緩存
	- 請求重定向 (常見場景如 http -> https, old url to new url)
	- URI重寫
![[IMG-Nginx-20250811203630925.png]]

URI重寫
後端可以不用關注 api/v1
這個在網站migration 和架構重組可以利用
![[IMG-Nginx-20250811203633379.png]]


進階
![[IMG-Nginx-20250811203634888.png|862]]


access log and error log

access control
![[IMG-Nginx-20250811203636186.png]]


rate limit
![[IMG-Nginx-20250811203637220.png]]


虛擬主機
see [[Nginx#virtual server for route to 不同的 subdomain]]
![[IMG-Nginx-20250811203640634.png]]


緩存配置
![[IMG-Nginx-20250811203642200.png]]



正向代理, 因此也可以作為跳板機，幫 client 發請求
![[IMG-Nginx-20250811203643459.png]]

也可以自定義錯誤頁面


protocol support
- http
- http2
- websocket



gzip 壓縮
![[IMG-Nginx-20250811203644949.png]]

也支持 process and connection 的配置
![[IMG-Nginx-20250811203645157.png]]


![[IMG-Nginx-20250811203646626.png]]







linux basic config path
```Markdown
/etc/nginx/nginx.conf
```


# install
```sh fold


sudo apt-get update && sudo apt-get install nginx -y


# 啟動
sudo systemctl start nginx 

# 讓他變成服務，下此重新開機也會自動啟動
sudo systemctl enable nginx

# check status
sudo systemctl status nginx



# shall see it worked
curl http://localhost

# 設定預設網頁根目錄
sudo vim /etc/nginx/sites-available/default

# 找到 root /var/www/html;  # <--可以改這一行來更改網頁根目錄路徑


# 這邊可以設定伺服器名稱, but /etc/nginx/conf.d 預設是空的
sudo vi /etc/nginx/conf.d/default.conf
# 找到 server_name 這一行並設定為您的網域名稱或 IP 位址。


# check syntax
sudo nginx -t


# reload
sudo systemctl reload nginx


# stop
sudo systemctl stop nginx

## 開機不會自動啟動
sudo systemctl disable nginx

## 重啟
sudo systemctl restart nginx

## 移除
sudo apt-get remove nginx

# PS: 使用 systemctl 命令是 Ubuntu 系統管理 Nginx 服務的推薦方式,因為它可以確保服務的狀態正確地被 systemd 管理

```


# virtual server for route to 不同的 subdomain

假設我去 DNS provider, like cloudflare, 那邊建立兩個 subdomain, support and api
[https://support.benhuapp.store/](https://support.benhuapp.store/)
[https://api.benhuapp.store/](https://api.benhuapp.store/)

I want [https://api.benhuapp.store/](https://api.benhuapp.store/) goes into my api server
I want [https://support.benhuapp.store/](https://support.benhuapp.store/) goes into my support web site

how does people usually to manage them?

for api 設定: 
````sh fold
cd /etc/nginx/sites-available/

sudo vi api.benhuapp.store

```
server {
    listen 80;
    server_name api.benhuapp.store;

    location / {
        proxy_pass http://localhost:3000; # 假設您的 API 伺服器運行在端口 3000
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

```

# soft link this file to /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/api.benhuapp.store /etc/nginx/sites-enabled/

# check syntax err
sudo nginx -t

# reload nginx
sudo systemctl reload nginx


# 建立一個nodejs server listen to port 3000 (or use Go or use Python..whatever)..
python -m http.server <port>


# 有時候會無法重啟，因為 ports bind 不上，可以用 procs 確認是否有 old proces, 如果有，那可以用
sudo killall -QUIT nginx
# 這會 kill gracefully, 然後就可以重開

````

default will fallback to www 開頭

  

針對 上面的作法 for [support.benhuapp.store](https://support.benhuapp.store/)
- another example to use lobe-chat
    docker up
    ```Markdown
    docker run -d -p 3210:3210 \
      -e OPENAI_API_KEY=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 \
      -e ACCESS_CODE=bsZMq3fCnRusFNVqcsr85V3A7dAZtEnKdiEwkqaDySZdjCM2R4gTAhZPUiGzG2hMdQmq36VPgjLs3mxJFm2zHrXUi9oRwZjPzXE63w2wPbyV3L3jaQbeuzspNPRNQ6LX \
      --name lobe-chat \
      lobehub/lobe-chat;
    ```
    
    ```Markdown
    
    lobe_chat.benhuapp.store
    
    1. add subdomain in cf
    ```
    
    ````sh
    cd /etc/nginx/sites-available/
    sudo vi lobe_chat.benhuapp.store
    
    
    ```
    server {
        listen 80;
        server_name lobe_chat.benhuapp.store;
    
        location / {
            proxy_pass http://localhost:3020;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
    
    ```
    
    # soft link this file to /etc/nginx/sites-enabled/
    sudo ln -s /etc/nginx/sites-available/lobe_chat.benhuapp.store /etc/nginx/sites-enabled/
    
    # check syntax err
    sudo nginx -t
    
    # reload nginx
    sudo systemctl reload nginx
    ````
    
    pass
    ```Markdown
    bsZMq3fCnRusFNVqcsr85V3A7dAZtEnKdiEwkqaDySZdjCM2R4gTAhZPUiGzG2hMdQmq36VPgjLs3mxJFm2zHrXUi9oRwZjPzXE63w2wPbyV3L3jaQbeuzspNPRNQ6LX
    ```
    



## rate limit 設定


- 可以 per api 來設定
用一個 lb 來處理， like nginx, 多 app 基本上就要這樣用才可以限流


nginx rate limit 坑

- `rate=100r/m` 
- 還滿直觀的，就每分鐘一百個請求。
- 但要注意的是 nginx 會幫你換算成多少毫秒可以接受一個新請求!
- 譬如說每分鐘一百個，他就會幫你換算成 600 毫秒一個
- **因此如果你在 600 毫秒內同時發了十個請求，那就只有第一個會被接受，其他九個都會直接被拒絕掉** 
- 如果不要的話，那要設定 `brust` and `nodelay` 來並行

- ``rate=100r/m, burst 300`
	- 表示1分鐘如果有400個打過來, 100個 drop
	- 然後這300個用 100r/m的速度消化

- `1r/m, burst 10`
- 如果打30個過來，20個 drop
- 10個進來
- 10個用一分鐘一個的速度吃
- 如果用 nodelay
- 那一分鐘可以並行跑10個


nginx 也可以設定 body size, 要設定，不然我給你上傳100g檔案的話…



ref:
- [Day06 - 流量限制（一）](https://ithelp.ithome.com.tw/articles/10268910)
- [Day07 - 流量限制（二）](https://ithelp.ithome.com.tw/articles/10269559)
- [Day08 - 流量限制（三）](https://ithelp.ithome.com.tw/articles/10270368)
- [Day09 - 流量限制（四）](https://ithelp.ithome.com.tw/articles/10270993)
- [Day10 - 流量限制（五）](https://ithelp.ithome.com.tw/articles/10271702)







# my own ngrok

參考 
- [my own ngrok – Vallard's Blog](https://benincosa.com/?p=3755)
- [Can You Grok It – Hacking together my own dev tunnel service | Hacker News](https://news.ycombinator.com/item?id=40028494)


前提：
搞一個 vm, 網域
網域那邊已經設定好 A record 指向 VM 靜態 ipv4 IP


nginx 設置
```sh


# file: sudo vim /etc/nginx/sites-available/default

server {
        listen 80;
        listen 443 ssl;
        server_name benhu.store;

        # SSL configuration
        ssl_certificate /etc/letsencrypt/live/benhu.store/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/benhu.store/privkey.pem;

        location / {
                proxy_pass http://localhost:7070;
                proxy_set_header Host $host;
                proxy_set_header X-Real-Ip $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
                error_page 502 /500.html;
        }
        location /500.html {
                return 500 "<center>Nothing to see here...</center>";
        }
}


#如果你是一般的 vm
# 本地可以透過下面這個指令, 把外部連到 nginx 7070 port tunnel into local port 3000 的 服務
# -N 是不需要 ssh 進去
# -R 7070:localhost:3000 -> remote 遠端 -> 連到 local 3000

ssh -R 7070:localhost:3000 -N <user_name>


#我用 gcp, 指令是, 就是把上面的東西塞到 --ssh-flag

gcloud compute ssh --zone "asia-east1-b" "instance-20240519-051959" --project "inner-topic-419501" --ssh-flag="-vvv" --ssh-flag="-R 7070:localhost:3000 -N"



# 本地快速開一個服務
# 下面這個只能適用 linux
wget -qO port-checker https://github.com/qdm12/port-checker/releases/download/v0.3.0/port-checker_0.3.0_linux_amd64

# or arm64 for mac
wget -qO port-checker https://github.com/qdm12/port-checker/releases/download/v0.3.0/port-checker_0.3.0_darwin_arm64


chmod +x port-checker
./port-checker -port 3000


```

done!
![[IMG-Nginx-20250811203647700.png]]



    
- FAIL 目標：看一個domain可以讓我用https 代理到內網 how to setup my own http(s) proxy (ngrok)
    [https://benincosa.com/?p=3755](https://benincosa.com/?p=3755)
    - nginx config
        
        這個好像是在 side enabled 裡面，再確認一下
        ````Markdown
        
        
        cd /etc/nginx/sites-available/
        sudo vi my_ngrok.benhuapp.store
        
        
        ```
        server {
            ... see below
        }
        
        ```
        
        # soft link this file to /etc/nginx/sites-enabled/
        sudo ln -s /etc/nginx/sites-available/my_ngrok.benhuapp.store /etc/nginx/sites-enabled/
        
        # check syntax err
        sudo nginx -t
        
        # reload nginx
        sudo systemctl reload nginx
        
        
        server {
            listen 80;
            server_name api.benhuapp.store;
        
            location / {
                proxy_pass http://localhost:3000;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
            }
        }
        
        
        server {
           server_name my_ngrok.benhuapp.store;
           
           location / {
              proxy_pass http://localhost:7070;
              proxy_set_header Host $host;
              proxy_set_header X-Real-Ip $remote_addr;
           }
        
        }
        
        
        ````
        
    - bash
        ```Bash
        
        
        
        ssh -R 7070:localhost:3000 -N re4388
        # 這條 SSH 命令 可將遠端機器的一個端口轉發到本地機器的一個端口。


gcloud compute ssh --zone "asia-east1-b" "instance-20240519-051959" --project "inner-topic-419501" --ssh-flag="-vvv" --ssh-flag="-R 7070:localhost:3000"



        or gcloud
        gcloud compute ssh --zone "asia-east1-b" "instance-20240406-011050" --project "inner-topic-419501" -- -NR 127.0.0.1:3001:34.80.127.242:7070
        
        # -R 表示遠端端口轉發。
        # 7070 是遠端機器上的端口，這意味著這個端口上的所有數據都會被轉發。
        # localhost:3000 指定了本地機器上的目標地址和端口。在這個例子中，它表示所有轉發到遠端 7070 端口的數據都將被轉發到本地機器上的 3000 端口。
        # -N：這個選項告訴 SSH 客戶端不執行遠端機器上的命令。通常與端口轉發一起使用，因為目的只是為了轉發端口，而不是在遠端機器上執行命令。
        # re4388：這是你要連接的遠端 SSH 服務器的用戶名或主機名。你需要將它替換為實際的用戶名或主機名，或者是一個在你的 SSH 配置文件中定義的主機別名。
        ```
        still not working, don’t know why
        - [x] try add `listen 80;`-> still can’t
    
    - 進階: 


# docker 安裝

```sh  fold



# Dockerfile file continue one line
FROM nginx:alpine


# start
docker build -t learn-nginx-docker .
docker run -it -p=7777:80 -d learn-nginx-docker

# or run like this
docker run -d -p 7777:80 --name nginx-web-server nginx

# html file:
ls /usr/share/nginx/html


# config
vi /etc/nginx/conf.d/default.conf



```
### ref
- [https://hi-founder.com/p/nginx-docker封裝/](https://hi-founder.com/p/nginx-docker%E5%B0%81%E8%A3%9D/)
- [https://medium.com/starbugs/web-server-nginx-2-bc41c6268646](https://medium.com/starbugs/web-server-nginx-2-bc41c6268646)






# Link
- [nginx rate limit 坑](https://www.notion.so/Web-API-2021-iThome-99adb55152494d2688c721c2013d032c?pvs=21)
- [mac nginx play](https://www.notion.so/mac-nginx-play-66aa821897aa464ea3262de6c59294c8?pvs=21)
- [https://hi-founder.com/p/nginx-教學/](https://hi-founder.com/p/nginx-%E6%95%99%E5%AD%B8/)
- [08.虚拟主机_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1mz4y1n7PQ?p=8&vd_source=c1191139db7aee736d31e75ebc6029d0)
- [Nginx Config 與常用指令教學: 快速入門網站架設 | 前端三分鐘 | 一起用三分鐘分享技術與知識](https://www.notion.so/Nginx-Config-208ef012ecf8482fa7cdf7fb20a7ddd0?pvs=21)
- [nginx playground](https://nginx-playground.wizardzines.com/)
- [New tool: an nginx playground](https://jvns.ca/blog/2021/09/24/new-tool--an-nginx-playground/)
- [https://github.com/dunwu/nginx-tutorial](https://github.com/dunwu/nginx-tutorial)
- [[基礎觀念系列] Web Server & Nginx — (1) | by 莫力全 Kyle Mo | Starbugs Weekly 星巴哥技術專欄 | Medium](https://medium.com/starbugs/web-server-nginx-1-cf5188459108)
    - 需要 web server like nginx 的反向代理功能，根據不同域名轉發到 Application Server 的不同 port 上去處理
    - web server like nginx 負載平衡就是負責 request 的分發，決定 request 要被分到哪一個 Application Server 處理
- [[基礎觀念系列] Web Server & Nginx — (2) | by 莫力全 Kyle Mo | Starbugs Weekly 星巴哥技術專欄 | Medium](https://medium.com/starbugs/web-server-nginx-2-bc41c6268646)
- nginx config tool → [https://nginx.viraptor.info/](https://nginx.viraptor.info/)