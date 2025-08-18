[[network_idx]]
[[Nginx]]

## install
```bash fold


sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https

# get gpg key
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list

sudo apt update

sudo apt install caddy

```

[] 
## cf設定
![[IMG-caddy-20250811203623605.png|600]]

## 檔案設定
```bash fold

sudo vi /etc/caddy/Caddyfile

# 內容如下
static0.re4388.buzz {
        root * /var/www/html
        file_server
}

reverse0.re4388.buzz {
    reverse_proxy 127.0.0.1:9200   #x-ui or other internal svc
    encode gzip
}

redirect.re4388.buzz {
    redir https://naiyous.com/4457.html
}



# static site content
cat /var/www/html/index.html
<!DOCTYPE html>
<html>
<body>
<h1>HELLO</h1>
<p>THIS IS BEN HU</p>
</body>
</html>



```



result:
- http://static0.re4388.buzz
- http://reverse0.re4388.buzz
- http://redirect.re4388.buzz
- http://cpolar0.re4388.buzz

ref: 
- https://naiyous.com/4457.html
- [(448) Caddy2的安装与使用，轻松实现反向代理和重定向，How to Make a Simple Caddy 2 Website (with automatic HTTPS)#一瓶奶油 - YouTube](https://www.youtube.com/watch?v=SHxR0yFntmU&t=65s)

