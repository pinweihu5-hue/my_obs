```yaml fold title:yaml hl:1,3-4
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
    volumes:
      - ./config:/config # 設定檔儲存目錄
      - ./downloads:/downloads # 下載目錄
    ports:
      - 8085:8085 # 映射容器內部的8085 WebUI通訊埠
      - 6881:6881
      - 6881:6881/udp
    restart: unless-stopped
```

ref: [Obsidian 插件：Code Styler](https://pkmer.cn/Pkmer-Docs/10-obsidian/obsidian%E7%A4%BE%E5%8C%BA%E6%8F%92%E4%BB%B6/code-styler/)
