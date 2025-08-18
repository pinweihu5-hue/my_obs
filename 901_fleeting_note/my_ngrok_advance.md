---  
alias:  
creation_date: 2024-05-26 12:59  
tags: 
related: [[tunnel_idx]], [[Nginx]]
---  

- [Can You Grok It :: dade](https://0xda.de/blog/2024/04/can-you-grok-it/)
- [Can You Grok It – Hacking together my own dev tunnel service | Hacker News](https://news.ycombinator.com/item?id=40028494)


- 作者想要 tunnel 但有一些目標
	- 可以用自己的 domain name
	- 可以tls 結束在本機
	- 不想要邀請自動掃描
	- 可以自架
		- 作者有說，這邊有類似項目： awesome-tunneling


- ngrok還是可以看到你的東西，因為他們在抵達你的電腦前就已經終止了 tls
	- 另外 ngrok沒有自定domain unless u paid


- tailscale  tunnel 符合很多需求，但作者想要自架
	- 但是 tailscale 一樣會被公網看到且被掃描
	- ts 一樣是透過 let's encryt 來替你的主機拿到 cert, 一樣會發佈到 Certificate Transparency Logs (CTL), like [this](https://crt.sh/?q=%25.ts.net)
	- ts 也無法自定 domain


- vscode 的 port forward 一樣很安全也可用
	- 但是無法用自己的網域



- 一個很簡單的作法：[我自己的 ngrok – Vallard 的博客 --- my own ngrok – Vallard's Blog](https://benincosa.com/?p=3755)
	- 可以用自己的網域



作者要自己弄
nginx 的 server_name 可以用 wildcard
```sh

server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name ~^(.*)\.dev\.0xda\.de$;
    ssl_certificate /etc/letsencrypt/live/dev.0xda.de/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/dev.0xda.de/privkey.pem;
    location / {
        include proxy_params;
        proxy_pass http://localhost:7070;
        error_page 502 /no-tunnel.html;
    }
    location /no-tunnel.html {
        return 404 "<center>No such tunnel...</center>";
    }
}

```

使用 Lets Encrypt 建立通配符 TLS 憑證 - 您需要確保它可以自動續訂，這需要您做一些額外的工作。我們不會在這篇文章中討論自動續訂


作者說，這樣做的話，我們可以擁有任意隧道名稱，而不會將它們洩漏到憑證透明度日誌中。
因為let's encryp 沒有這樣做？但是大公司會這樣做(like ts, ngrok?)


然後作者用這種 wirdcard -> 可以讓 tcp socket 連到本機的 linux domain socket 動態轉發

但是 nginx 的權限是 www-data, 因此無法 acess domain socket, 需要開權限 
```sh
chmod o+rw /tmp/*.dev.0xda.de.socket 2>/dev/null
```


# related link

-  awesome-tunneling : https://github.com/anderspitman/awesome-tunneling