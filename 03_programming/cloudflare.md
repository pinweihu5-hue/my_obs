
related: [[dns_idx]]

## tunnel can  expose my internal service on httpS

- 透過 tunnel expose 出去
- 再透過 DNS CNAME 別名 tunnel doman → http變https

```Bash
docker run -d cloudflare/cloudflared:latest tunnel --no-autoupdate run --token eyJhIjoiNmU3OTI3NTkzOGNhYWFkMWJhZjU5YjIxYWFmYjI2MjUiLCJ0IjoiY2M3NmMxMTQtYjE1ZS00OTQ4LTgxYjgtNmZiNzk1NDYxNzVhIiwicyI6Ik9XVTJNV0kwTVRRdFptTmxZeTAwWVRoaExXSmpOREl0T1Rnd1pHTmpaakZqWm1SayJ9


# https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/get-started/create-local-tunnel/

sudo curl -L 'https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64' -o /usr/bin/cloudflared
sudo chmod +x /usr/bin/cloudflared
cloudflared tunnel login

# 建立通道
cloudflared tunnel create acer3
# cd98fa97-ca9e-4e27-9d17-df242fca38d6


# 建立路由 -> 等同 cname 建立如下圖
# 小心，下面會透過 cf proxy, 只能建立一級路由, like abc可以，但 abc.com 就無法透過 cf 幫你掛上tls了
cloudflared tunnel route dns acer3 mindmap3
```



```Bash


# https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/configure-tunnels/local-management/configuration-file/
vim ~/.cloudflared/config.yml

# validate
cloudflared tunnel ingress validate

# check your url hit which rule
cloudflared tunnel ingress rule https://mindmap3.benhuapp.store

# run up tunnel

# check tunnel
cloudflared tunnel list

# run up
cloudflared --loglevel debug --transport-loglevel warn --config ~/.cloudflared/config.yml tunnel run cd98fa97-ca9e-4e27-9d17-df242fca38d6

# 可以網頁 remote 設定，也可以 local 設定

# 發現有有被 talescale拿了 443
sudo netstat -antp | grep ':80\|:443\|:8443\|:2083\:9443\|:10443'
# tcp        0      0 100.87.63.33:8443       0.0.0.0:*               LISTEN      2317664/tailscaled
# 監聽100.87.63.33:8443, 吃任何遠端請求
```

這邊有bug, 有時候會matching 錯誤的..要自己調整

  

### 建立一個新的 service 的步驟

```Bash
# create new route/CNAME in the same tunnel
cloudflared tunnel route dns acer3 mindmap4


nvim ~/.cloudflared/config.yaml

ingress:
  - hostname: mindmap3.benhuapp.store
    service: http://localhost:3002
  # ingress 新增一組
  - hostname: mindmap4.benhuapp.store 
    service: http://localhost:3003 
    

sudo systemctl restart cloudflared
```

  

  

### cloudflared 變成服務

```Bash

sudo cloudflared --config /home/re4388/.cloudflared/config.yml service install

systemctl start cloudflared
systemctl status cloudflared
systemctl restart cloudflared

注意：创建系统服务后，配置文件会被拷贝到 /etc/cloudflared/config.yml，后续修改配置必须修改新文件
```

  


## too many redirect error
- 如果你遇到 too may redirect 的錯誤，可能是你的 caddy or nginx 跟 cf 的下面的設定打架
	- 你需要調整為彈性
	- ref: https://community.cloudflare.com/t/redirect-too-many-time-when-setting-ssl-flexible/615343
![[IMG-cloudflare-20250811204403378.png| 600]]



## links
- [使用 Cloudflare Tunnel 实现内网穿透，把服务器架在家里](https://bra.live/setup-home-server-with-cloudflare-tunnel/)
- [Cloudflare Tunnel内网穿透 - Google 搜尋](https://www.google.com/search?q=Cloudflare+Tunnel%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F&sca_esv=9b107820ee34c717&sca_upv=1&rlz=1C5CHFA_enTW1035TW1035&sxsrf=ACQVn08vvjdnYxYlDGv7CVRu_lR9Vo26IQ%3A1713311840057&ei=YBAfZvWNA-771e8PmpW74A8&ved=0ahUKEwj1vfb898eFAxXuffUHHZrKDvwQ4dUDCBA&uact=5&oq=Cloudflare+Tunnel%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F&gs_lp=Egxnd3Mtd2l6LXNlcnAiHUNsb3VkZmxhcmUgVHVubmVs5YaF572R56m_6YCPSIcfUNoCWMcccAF4AZABAJgBWqAB_gSqAQIxMrgBA8gBAPgBAZgCB6ACjgPCAgoQABhHGNYEGLADwgIIEAAYgAQYogTCAgUQIRigAZgDAIgGAZAGCpIHATegB74S&sclient=gws-wiz-serp)
-  [記錄 | benhuapp.store | Re4388@gmail.com's Account | Cloudflare](https://dash.cloudflare.com/6e79275938caaad1baf59b21aafb2625/benhuapp.store/dns/records)
- [Tunnels - Cloudflare One](https://one.dash.cloudflare.com/6e79275938caaad1baf59b21aafb2625/networks/tunnels?search=)
- [zhuima/awesome-cloudflare: ⛅️ 精选的 Cloudflare 工具、开源项目、指南、博客和其他资源列表。](https://github.com/zhuima/awesome-cloudflare?tab=readme-ov-file)
- [利用 telegra.ph + cf 的免費圖床](https://www.notion.so/telegra-ph-cf-d6729c51dccb42b194581999f0a9fdf3?pvs=21)
- [自架臨時郵箱服務](https://www.notion.so/39845195dae64a1faa057ab95cf887c9?pvs=21)
- 這邊有很多短網址實作 → [https://github.com/zhuima/awesome-cloudflare?tab=readme-ov-file#短链](https://github.com/zhuima/awesome-cloudflare?tab=readme-ov-file#%E7%9F%AD%E9%93%BE)

  