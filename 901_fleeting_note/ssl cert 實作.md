
related:
 [[Nginx]]
 

# cloudflare

- 啟動 proxy 就幫你 handle

下面是我刻意關掉，這樣才可以 try Let's Encrypt
![[IMG-ssl 實作-20241003104851556.png]]


#  Let's Encrypt

[https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal](https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal)

沒有自動 re-new -> 要另外處理

```sh

sudo su

apt-get update sudo 
apt-get install python3-certbot-nginx
apt install certbot -y


# ensure nginx is running
sudo systemctl start nginx
sudo systemctl status nginx


sudo certbot certonly --nginx

# 會叫你輸入 email, 用 re4388@gmail.co 即可
# 同意 term
# 輸入 domain, 最後會給出 gen 檔案的位置


- Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/benhu.store/fullchain.pem
   
   Your key file has been saved at:
   /etc/letsencrypt/live/benhu.store/privkey.pem
   
   Your cert will expire on 2024-08-24. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"



```


在 Nginx 配置文件中,找到您的伺服器區塊(server block)並添加以下內容:

假設目前啟動 ssl 的就是 default domain 
so go here -> 
`sudo vim /etc/nginx/sites-available/default`


改成如下：
```Bash
server {
    listen 80;
    listen 443 ssl;
    server_name benhu.store;

    ssl_certificate /etc/letsencrypt/live/benhu.store/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/benhu.store/privkey.pem;

    # 其他配置
}
```


check and reload
```Bash
sudo nginx -t  
sudo systemctl reload nginx
```


如果你要讓其他 domain 也有 ssl, 你要另外申請


# acme

- acme 申請證書的過程
- 這個好像會自動 re-new 但是下面這個腳本常常會失敗，需要retry 很多次喔
- 有空可以去找新的自動化腳本
```bash fold

# 安装 acme：
sudo su
curl https://get.acme.sh | sh

# 安装 socat (a utility that establishes two bidirectional byte streams and transfers data between them)
apt install socat

# 添加软链接 to /usr/local/bin
ln -s /root/.acme.sh/acme.sh /usr/local/bin/acme.sh

# 注册账号： 
acme.sh --register-account -m re4388@gmail.com

# 开放 80 端口：
ufw allow 80

# 申請前要先關掉 80 port 的服務，類似正在跑的 nginx
# 申请证书： 
acme.sh  --issue -d 你的域名  --standalone -k ec-256

# 安装证书： 
acme.sh --installcert -d 你的域名 --ecc  --key-file   /root/XXX/server.key   --fullchain-file /root/XXX/server.crt 

# 如果默认 CA 无法颁发，则可以切换下列 CA：
切换 Let’s Encrypt：acme.sh --set-default-ca --server letsencrypt
切换 Buypass：acme.sh --set-default-ca --server buypass
切换 ZeroSSL：acme.sh --set-default-ca --server zerossl

# 自签证书：
生成私钥：openssl ecparam -genkey -name prime256v1 -out ca.key
生成证书：openssl req -new -x509 -days 36500 -key ca.key -out ca.crt  -subj "/CN=bing.com"
```
        



# 自簽 cert

不建議
但是下面個 bash 有教育意義

[https://www.liaoxuefeng.com/article/990311924891552](https://www.liaoxuefeng.com/article/990311924891552)

conf/ssl/gencert.sh

```Bash
#!/bin/sh

#!/bin/sh
#
# 來源：https://www.liaoxuefeng.com/article/990311924891552

# create self-signed server certificate:

read -p "Enter your domain [www.example.com]: " DOMAIN

# 1. 建立key
echo "Create server key..."
openssl genrsa -des3 -out $DOMAIN.key 2048

# 2. 建立签名请求
echo "Create server certificate signing request..."
SUBJECT="/C=US/ST=Mars/L=iTranswarp/O=iTranswarp/OU=iTranswarp/CN=$DOMAIN"
openssl req -new -subj $SUBJECT -key $DOMAIN.key -out $DOMAIN.csr


# 3. 将 Key 的口令移除
echo "Remove password..."
mv $DOMAIN.key $DOMAIN.origin.key
openssl rsa -in $DOMAIN.origin.key -out $DOMAIN.key

# 4. 用 Key 签名证书
echo "Sign SSL certificate..."

openssl x509 -req -days 3650 -in $DOMAIN.csr -signkey $DOMAIN.key -out $DOMAIN.crt

echo "TODO:"
echo "Copy $DOMAIN.crt to /etc/nginx/ssl/$DOMAIN.crt"
echo "Copy $DOMAIN.key to /etc/nginx/ssl/$DOMAIN.key"
echo "Add configuration in nginx:"
echo "server {"
echo "    ..."
echo "    listen 443 ssl;"
echo "    ssl_certificate     /etc/nginx/ssl/$DOMAIN.crt;"
echo "    ssl_certificate_key /etc/nginx/ssl/$DOMAIN.key;"
echo "}"

```

```Plain
server {
    ...
    ssl on;
    ssl_certificate     /etc/nginx/ssl/www.test.com.crt;
    ssl_certificate_key /etc/nginx/ssl/www.test.com.key;
}
```


另一個 self-signed script

ref: [https://hub.docker.com/r/enwaiax/x-ui](https://hub.docker.com/r/enwaiax/x-ui)

```bash
sudo apt update
sudo apt install snapd nginx
sudo snap install core
sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
certbot --nginx --agree-tos --no-eff-email --email re4388@gmail.com
sudo certbot renew --dry-run
```



[GitHub - FiloSottile/mkcert: A simple zero-config tool to make locally trusted development certificates with any names you'd like.](https://github.com/FiloSottile/mkcert)
- [FiloSottile/mkcert: 无需配置，执行一条命令让本地的开发环境实现 HTTPS 的工具 - HelloGitHub](https://hellogithub.com/repository/6a128cd5cb324a9faec84058b1ef9016)
