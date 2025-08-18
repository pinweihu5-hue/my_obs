- [[linux_idx]]
- [[linux 自架服務 還沒裝，可以考慮有機會裝]]


---

## service

### portainer

```yaml fold
sudo docker pull portainer/portainer-ce

sudo docker run -d -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```

### qbittorrent

[qbittorrent - LinuxServer.io](https://docs.linuxserver.io/images/docker-qbittorrent/#application-setup)
```yaml fold
version: '3'
services:
 qbittorrent:
    image: lscr.io/linuxserver/qbittorrent:latest
    container_name: qbittorrent
    environment:
      - PUID=1000 # 使用一般使用者跑服務，通常UID是1000
      - PGID=1000
      - TZ=Asia/Taipei # 設定時區
      - WEBUI_PORT=8085 # WebUI通訊埠
      - QBT_USERNAME=admin # Set your desired username
      - QBT_PASSWORD=admin # Set your desired password
    volumes:
      - ./config:/config # 設定檔儲存目錄
      - ./downloads:/downloads # 下載目錄
    ports:
      - 8085:8085 # 映射容器內部的8085 WebUI通訊埠
      - 6881:6881
      - 6881:6881/udp
    restart: unless-stopped
```


	- user: admin
	- pass ⇒ docker logs <id> to get the pass


### jellyfish

需要把 外部硬碟 mount 上去才可以被 jelly fish 識別 -> [[auto mount external disk into linux]]
docker compose file -> /home/re4388/jellyfin

- [Jellyfin：用 Docker 自架可調畫質 + 硬體解碼的影片音樂串流伺服器・Ivon 的部落格](https://ivonblog.com/posts/self-hosting-jellyfin/)
- [http://100.87.63.33:8096/web/index.html#!/home.html](http://100.87.63.33:8096/web/index.html#!/home.html)



user and pass
        re4388
        2aoxjgju
    
    
- [ ] 目前還沒有處理yt字幕問題 → not rush, 因為 yt 只是試水溫 → yt 我還是會直接看 XD    
- [ ] 目前還沒有確認不開硬體加速linux是否夠用
- [x] **新增其他 Jellyfin 使用者**
        

### glances

- [https://github.com/nicolargo/glances](https://github.com/nicolargo/glances)

```bash fold title:安裝
# install
pip install glances
pip install --user 'glances[web]'
pip install --user 'glances[all]'

# upgrade
pip install --user --upgrade glances

# run up in web mode
glances -w &

# 因為沒用 docker 開port繞fw, 需要開 port, 看下面

# 註冊為服務
sudo nvim /etc/systemd/system/glances.service

內容為

[Unit]
Description=Glances monitoring tool
Wants=network-online.target

[Service]
ExecStart=/home/re4388/.local/bin/glances -w
User=re4388

[Install]
WantedBy=multi-user.target

sudo systemctl start glances.service
sudo systemctl status glances.service
```

```bash fold title:docker

docker run -d --restart="unless-stopped" -p 61208-61209:61208-61209 -e TZ="${TZ}" -e GLANCES_OPT="-w" -v /home/re4388/.config/glances/glances.conf:/glances/conf/glances.conf -v /var/run/docker.sock:/var/run/docker.sock:ro -v /run/user/1000/podman/podman.sock:/run/user/1000/podman/podman.sock:ro --pid host nicolargo/glances:latest-full

# 如何換 config -> docker 有點難搞 XD

eca2960f492d

# 一些說明
-e GLANCES_OPT="-w": 設定 Glances 啟動參數,在這裡啟用 Web 界面。
-v /home/re4388/.config/glances/glances.conf:/glances/conf/glances.conf: 將主機上的 Glances 配置文件掛載到容器內。
-v /var/run/docker.sock:/var/run/docker.sock:ro: 以唯讀方式將 Docker 套接字掛載到容器內,以便 Glances 能夠監控 Docker 容器。
-v /run/user/1000/podman/podman.sock:/run/user/1000/podman/podman.sock:ro: 以唯讀方式將 Podman 套接字掛載到容器內,以便 Glances 能夠監控 Podman 容器。
--pid host: 使用主機的 PID 命名空間,以便 Glances 能夠監控主機上的所有進程。
```

### monitorix
[Monitorix :: Debian/Ubuntu --- Monitorix :: Debian/Ubuntu](https://www.monitorix.org/doc-debian.html)
- 內部服務
    - no need to let other to see
    - only ppl/admin in the tailnet can see
    - 所以用 tailscale server 即可

```bash fold title:install

sudo apt install monitorix

# httpd port 我改為 8081
sudo nvim /etc/monitorix/monitorix.conf
sudo lsof -i :8081
service monitorix restart
service monitorix status
```
        
- debug, ssh local forward    
    - `ssh -NL localhost:8081:100.87.63.33:8081 [re4388@acer.tailbcab9.ts.net](<mailto:re4388@acer.tailbcab9.ts.net>) -N`
- 讓 mac 可以看到 can use tailscale server

```bash fold title: ts

sudo tailscale serve --bg --https 8081 <http://127.0.0.1:8081>

sudo lsof -i :8081
COMMAND       PID      USER   FD   TYPE    DEVICE SIZE/OFF NODE NAME
monitorix 2398271 monitorix    3u  IPv4  17118062      0t0  TCP *:tproxy (LISTEN)
tailscale 2446527      root   29u  IPv6 749655306      0t0  TCP re4388-Swift-SF515-51T:tproxy (LISTEN)



# to stop
sudo tailscale serve --https=8081 off
	
	# 如果用 --bg, 那就是 tailscaled 負責，因此以下可能沒啥用XDD
	# 不過還是可以用 sudo lsof -i:8081 確認有沒有東西在監聽
	# 砍掉這個 proc
	# 找 proc
	# 用 htop 或是 sudo lsof -i :8081, ps, pg找 pid and then kill -16 <pid> 

```

[介紹好用工具：Monitorix (詳細的監控你的 Linux 主機狀況) | The Will Will Web](https://blog.miniasp.com/post/2008/12/20/Useful-tool-Monitorix)

### redis72

- ssh local forward (need to inside tailscale vpn)
    - `ssh -NL localhost:6389:100.87.63.33:6379 [re4388@re4388-swift-sf515-51t.tailbcab9.ts.net](<mailto:re4388@re4388-swift-sf515-51t.tailbcab9.ts.net>) -N`
    - 6389 → 6379

### pg16

- ssh local forward (need to inside tailscale vpn)
    - `ssh -NL localhost:5460:100.87.63.33:5432 [re4388@re4388-swift-sf515-51t.tailbcab9.ts.net](<mailto:re4388@re4388-swift-sf515-51t.tailbcab9.ts.net>) -N`
    - 5432 → 5460

### lobechat

```yaml fold title:docker_compose


name: lobe_chat
services:
    lobe-chat:
        ports:
            - 3210:3210
        environment:
            - OPENAI_API_KEY=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
            - ANTHROPIC_API_KEY=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
            - OLLAMA_PROXY_URL=http://100.66.152.75:11434
        container_name: lobe-chat
        image: lobehub/lobe-chat

```

- to let other to use
	- ssh local forward
	    - `ssh -NL localhost:3210:100.87.63.33:3210 [re4388@re4388-swift-sf515-51t.tailbcab9.ts.net](<mailto:re4388@re4388-swift-sf515-51t.tailbcab9.ts.net>) -N`
	- tailscale tunnel
		- only avail in ts net
	    - [https://re4388-swift-sf515-51t.tailbcab9.ts.net/welcome](https://re4388-swift-sf515-51t.tailbcab9.ts.net/welcome)
	    - `bsZMq3fCnRusFNVqcsr85V3A7dAZtEnKdiEwkqaDySZdjCM2R4gTAhZPUiGzG2hMdQmq36VPgjLs3mxJFm2zHrXUi9oRwZjPzXE63w2wPbyV3L3jaQbeuzspNPRNQ6LX`
	    - 3210
- 如果要給外網用 -> tailscale serve

### my node

- 3002
- docker → cf tunnel
- bun
    - 3003 → 還在弄


### uptime kuma

- [https://github.com/louislam/uptime-kuma](https://github.com/louislam/uptime-kuma)
- docker → cf tunnel
- `docker run -d --restart=always -p 3001:3001 -v uptime-kuma:/app/data --name uptime-kuma louislam/uptime-kuma:1`
- todo
- 監控上面有沒有正常運作 XD
- ssh local 轉發
    - `ssh -NL localhost:3001:100.87.63.33:3001 [re4388@re4388-swift-sf515-51t.tailbcab9.ts.net](<mailto:re4388@re4388-swift-sf515-51t.tailbcab9.ts.net>) -N`

### mindmap

- 啟動方法
    - `docker run -d -p 8081:8080 shuiche/mind-map:latest`
    - 8081
- docker → cf tunnel
- 轉發可以用上面的 ssh local forward 或是用 tailscale tunnel

[[linux 自架服務 還沒裝，可以考慮有機會裝]]


[[ubuntu 開 port 給外部服務用 via firewall]]

### shorcut current setting

we use tweak to remap

1. ctrl lock → ctrl
    1. so it’s ez to copy paste and do a lot of stuff
2. still can’t use caplock as esc in vim




### Suwayomi + Tachidesk

[Suwayomi + Tachidesk教學，架設免費線上看漫畫伺服器，電腦版Tachiyomi · Ivon的部落格](https://ivonblog.com/posts/suwayomi-tachidesk/)

```yaml

version: '3.7'
services:
  suwayomi:
    image: ghcr.io/suwayomi/tachidesk
    environment:
      - TZ=Asia/Taipei # 修改時區
    volumes:
      - ./data:/home/suwayomi/.local/share/Tachidesk # 資料存放路徑
    ports:
      - "4567:4567"
    restart: on-failure:3
  flaresolverr:
    image: ghcr.io/flaresolverr/flaresolverr:latest
    container_name: flaresolverr
    environment:
      - TZ=Asia/Taipei # 修改時區
    ports:
      - "8191:8191"
    restart: unless-stopped


```