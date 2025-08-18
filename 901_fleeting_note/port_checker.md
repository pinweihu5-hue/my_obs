[[mac_idx]]
[[linux_idx]]


```sh

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