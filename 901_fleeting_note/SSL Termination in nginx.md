### 什麼是 SSL 終止（SSL Termination）？

SSL 終止指的是在網路中的特定點（通常是負載均衡器或反向代理）解密 SSL/TLS 流量的過程。
這樣，解密後的流量將以未加密的形式轉發給後端伺服器。


### why 
- 性能提升：通過將 SSL 處理卸載到專用伺服器或設備，後端伺服器可以專注於提供應用邏輯，從而提高整體性能。
- 簡化證書管理：
	- 集中管理安全 SSL/TLS 版本和加密套件，確保所有流量的一致性。
	- 在單一點（負載均衡器或代理）管理 SSL 證書，簡化了更新和續訂證書的過程。
- 減少後端伺服器的複雜性：後端伺服器可以使用普通的 HTTP 協議運作，這可能簡化其配置和管理。
    

### Nginx 可以做 SSL 終止嗎？
是的，Nginx 可以執行 SSL 終止。它通常用作反向代理和負載均衡器，可以處理 SSL/TLS 流量。


以下是如何配置 Nginx 進行 SSL 終止的步驟。

### Nginx SSL 終止的配置示例

1. **安裝 Nginx**（如果尚未安裝）：
    `sudo apt update`
    `sudo apt install nginx`
    
2. **獲取 SSL 證書**：您可以從 Let's Encrypt 獲取免費的 SSL 證書，或從證書授權機構（CA）購買一個。假設您已經擁有名為 `example.com.crt` 和 `example.com.key` 的證書和密鑰文件。
    
3. **編輯 Nginx 配置**：
    打開 Nginx 配置文件，通常位於 `/etc/nginx/sites-available/default` 或 `/etc/nginx/nginx.conf`。
    `sudo nano /etc/nginx/sites-available/default`
    
4. **添加 SSL 配置**：
    

以下是一個 Nginx 配置的示例，執行 SSL 終止：

- **HTTP 到 HTTPS 的重定向**：第一個 server 區塊監聽 80 端口（HTTP），並將所有流量重定向到 HTTPS（443 端口）。
- **SSL 配置**：第二個 server 區塊監聽 443 端口並處理 SSL 流量，指定 SSL 證書和密鑰文件的路徑。
- **代理轉發**：`proxy_pass` 指令將解密後的流量轉發到後端伺服器（將 `http://backend_server` 替換為實際的後端伺服器地址）。
- **標頭設置**：`proxy_set_header` 指令確保原始請求的信息被傳遞到後端
```nginx


server {
    listen 80;
    server_name example.com;

    # http 重定向 到 HTTPS
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /path/to/example.com.crt;  # SSL 證書的路徑
    ssl_certificate_key /path/to/example.com.key;  # SSL 證書密鑰的路徑

    # 可選：提高安全性，指定 SSL 協議和加密套件, # 1.3 也有對 SNI 加密
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers 'HIGH:!aNULL:!MD5';

    location / {
        proxy_pass http://backend_server;  # 將流量轉發到後端伺服器 use http 
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

```

    

