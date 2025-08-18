
- db 要區分user權限
	- ready only, write, admin user (砍 table, 用db底層指令等)
- credential
	- 放cloud 的 secrent mgmt
	- or 自架的  [Hashicorp Vault](https://www.vaultproject.io/docs/what-is-vault)
	- 簡易方案可以用 **本地端**的密碼管理軟體  https://keepassxc.org/
	- 可以把整個密碼存成一個檔案然後用 masterKey來看
- sql injection
- 還是要做格式驗證
- 要用 prepare statement
    - 把語法和參數分開, 因此  `1' OR '1'='1' --` 傳進來，就只是參數 → 沒用了
- db 
	- 要有 access control
	- 不要用預設 port
	- 只吃特定ip(白名單設計)


ref:
- [Day23 - 你的資料安全嗎（一）](https://ithelp.ithome.com.tw/articles/10279199)
- [Day24 - 你的資料安全嗎（二）](https://ithelp.ithome.com.tw/articles/10279571)
- [Day25 - 你的資料安全嗎（三）](https://ithelp.ithome.com.tw/articles/10279965)