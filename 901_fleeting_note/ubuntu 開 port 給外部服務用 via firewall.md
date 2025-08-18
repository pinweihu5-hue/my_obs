- [[ubuntu]]


---


- docker 開的 port 會無視 ubuntu fw… XD 因此不用處理這塊 -> see [无视系统防火墙的 docker - 博客 - binsite](https://www.binss.me/blog/docker-pass-through-system-firewall/)

```bash title:防火牆設定
# 看 all fw rule
sudo firewall-cmd --list-all
sudo ufw status
sudo iptables -L -n | grep 8080

# firewall allow 61208 port, 如果需要臨時開放端口(不永久生效),可以省略 --permanent 參數
sudo firewall-cmd --permanent --add-port=61208/tcp

# reload -> acer 會斷掉網路
sudo firewall-cmd --reload

# 要透過開 ufw 來重啟 <-- 需要去 acer 電腦控制
sudo ufw enable

# 另外 ufw 也開放這個 port
sudo ufw allow 61208/tcp

# 其他
sudo systemctl start firewalld
sudo systemctl enable firewalld
sudo systemctl status firewalld

sudo ufw status
sudo ufw allow 61208/tcp
sudo ufw delete allow 61208/tcp

# if disable
sudo ufw enable

# 加入規則 allow tcp 61208
sudo iptables -A INPUT -p tcp --dport 61208 -j ACCEPT
# -A INPUT: 表示要新增一條輸入規則。INPUT 鏈是指處理進入本機的網路封包。
# -p tcp: 指定網路封包的協定類型為 TCP。
# --dport 61208: 表示目標埠號為 61208。這個埠號是被設定要允許通過防火牆。
# -j ACCEPT: 最後一步是指定對於符合上述條件的網路封包,要採取「接受」的動作,也就是允許通過防火牆。
# 允許所有目的地埠號為 61208,且使用 TCP 協定的輸入封包通過防火牆
# 這通常是為了讓某些應用程式或服務能夠正常對外提供服務

# 加入規則 allow 只允許新建立的 HTTP 連接以及已建立的連接使用 61208 port
sudo iptables -A INPUT -p tcp --dport 61208 -m state --state NEW,ESTABLISHED -j ACCEPT

# see all
sudo iptables -L -n

# remove rule
sudo iptables -D <chain> <rule_number>
<chain> 是規則所在的鏈,例如 INPUT、OUTPUT 或 FORWARD。
<rule_number> 是要移除的規則序號。

例如,如果要移除 INPUT 鏈中序號為 5 的規則:
sudo iptables -D INPUT 5

sudo iptables -D INPUT 9

# 確認? 可以先在 acer 這邊開一個簡易server, 指定 port
# 下面這個只能適用 linux
wget -qO port-checker https://github.com/qdm12/port-checker/releases/download/v0.3.0/port-checker_0.3.0_linux_amd64

# or arm64 for mac
wget -qO port-checker https://github.com/qdm12/port-checker/releases/download/v0.3.0/port-checker_0.3.0_darwin_arm64


chmod +x port-checker
./port-checker -port 8080

or 
python -m http.server 8080

# 另一台機器可以用 curl, use browser or nc 來 check
瀏覽器 開 <http://100.87.63.33:8080>
curl <http://100.87.63.33:8080>
nc -z 100.87.63.33 8080

```