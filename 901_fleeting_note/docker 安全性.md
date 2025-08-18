docker

- [Day26 - 保護鯨魚人人有責（一）](https://ithelp.ithome.com.tw/articles/10280332)
- [Day27 - 保護鯨魚人人有責（二）](https://ithelp.ithome.com.tw/articles/10280755)
- [Day28 - 保護鯨魚人人有責（三）](https://ithelp.ithome.com.tw/articles/10281108)
- [Day29 - 保護鯨魚人人有責（四）](https://ithelp.ithome.com.tw/articles/10281450)


dockerFile

image 的環境版本要制定版本，不要用latest 等等
lib 的版本也要指定
裡面不可以放敏感資料
使用 `--env` 或是 `--env-file` 來帶入

只 copy需要的東西
```go
# Very BAD
COPY . .
```

- 省空間
- 避免不小心也 copy 敏感資料

multi-stage build

只需要 build 好的
nodejs 只需要 dist 和必要資料

go只需要編譯執行檔


定期scan
- docker 已經內建
- `docker scan app`



container 不要用 root user

如果 app 有漏洞被拿到 container 的 root 權限
又被escape container → 拿到主機的 root → GG


加上 user 屬性就不是root了(預設)

```go
docker run --user 3000 <image>
```

因為 container 就是 linux下的一個 proc

要可以限制其cap

[https://man7.org/linux/man-pages/man7/capabilities.7.html](https://man7.org/linux/man-pages/man7/capabilities.7.html)

like

```go
docker run --cap-drop ALL <image> # 關掉所有權限
docker run --cap-drop ALL --cap-add CAP_NET_ADMIN <image> # 只給網管權限
```

用ready only 跑 container, 因為基本上你也不會把資料寫道主機XD

```css
docker run --read-only <image>

```