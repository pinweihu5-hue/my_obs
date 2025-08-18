以下是一個基本的 Nginx 負載均衡配置示例，這個配置將請求分發到多個後端伺服器上。假設我們有三個後端伺服器，分別位於 `192.168.1.2`、`192.168.1.3` 和 `192.168.1.4`，並且我們希望將 HTTP 請求負載均衡到這些伺服器上。

### Nginx 配置示例

1. **安裝 Nginx**（如果尚未安裝）：
    
```bash
sudo apt update
sudo apt install nginx

```
    
2. **編輯 Nginx 配置檔案**： 打開 Nginx 配置檔案，通常位於 `/etc/nginx/nginx.conf` 或 `/etc/nginx/sites-available/default`。
    
    sudo nano /etc/nginx/nginx.conf
    
3. **添加負載均衡配置**： 在配置檔案中，您可以添加以下內容：
    
    ```nginx

				http {
						upstream backend {
							# 定義後端伺服器
							server 192.168.1.2;
							server 192.168.1.3;
							server 192.168.1.4;
					
							# 可以選擇使用不同的負載均衡算法
							# 例如，使用輪詢（預設）：
							# server 192.168.1.2 weight=3;  # 這台伺服器的權重較高
							# server 192.168.1.3;
							# server 192.168.1.4;
						}
					
						server {
							listen 80;  # 監聽 HTTP 請求
					
							location / {
								proxy_pass http://backend;  # 將請求轉發到後端伺服器
								proxy_set_header Host $host;
								proxy_set_header X-Real-IP $remote_addr;
								proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
								proxy_set_header X-Forwarded-Proto $scheme;
							}
						}
}



```
    

### 配置說明

- **upstream backend**：定義一組後端伺服器，Nginx 將根據配置的負載均衡算法將請求分發到這些伺服器。
- **server**：指定後端伺服器的 IP 地址。可以根據需要添加更多的伺服器。
- **proxy_pass**：將請求轉發到 `upstream` 定義的後端伺服器組。
- **proxy_set_header**：設置一些 HTTP 標頭，以便後端伺服器可以獲取原始請求的相關信息。

### 測試和重啟 Nginx

在編輯完配置文件後，您可以使用以下命令檢查配置是否正確：

sudo nginx -t

如果配置正確，您將看到類似於 `syntax is ok` 和 `test is successful` 的消息。接下來，重啟 Nginx 以應用更改：

sudo systemctl restart nginx

### 註解

- 您可以根據需要調整負載均衡算法，例如使用 `least_conn`（最少連接數）或 `ip_hash`（根據客戶端 IP 地址進行哈希）等。
- 如果需要 HTTPS 支持，您還需要配置 SSL 證書並在 `server` 區塊中添加 `listen 443 ssl;` 相關配置。

這是一個基本的 Nginx 負載均衡配置示例，您可以根據具體需求進行調整和擴展。