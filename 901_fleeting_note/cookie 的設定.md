
- Secure → https 才準帶cookie
- HttpOnly → 中了 xss 後，也拿不到 cookie, 開發者是無法透過程式去拿到 cookie內的資料 , 只有瀏覽器可以拿到
- same-site
	- 跨網域請求偽造 CSRF 流程
		- 跨網域下，same-site is none 的話，瀏覽器會帶上已經有的 cookie
		- 類似我先去x銀行, x銀行 set-cookie給我，裡面有我的身分，存在瀏覽器
		- 我被騙到攻擊者網站，攻擊者網站看起來像是銀行網站（iframe), 這個請求雖然在壞人網站跨網域發x銀行的post請求，會帶上x銀行的 cookie, x銀行轉帳到我的錢出去
			- 或是就是偷我的身分做其他事情..
	- 所以，我們要設定  same-site
		- strcit → 禁止所有的跨 domain的 cookie傳送
		- lex → 跳轉登入（redirect) 的那種 get 的請求才可以跨域帶上 (很多大網站, fb, google, 都有用)
		- nona → 等於沒設定
		- same-site 也可以是瀏覽器設定，未必透過 server 設定，兩邊都要過才會帶上 cookie
		- same-site: lex已經是瀏覽器預設,
- domain
	- 另外 cookie 裡面如果有設定 domain
	- 那就決定了那個cookie只能把資料帶過去那個指定的 domain
	- 反過來說，如果你去其他 domain, 那個 cookie info 就不會帶過去
- path
	- path 也是一樣，有設定的話，對應的path才准許帶過去
- expire / max-age
	- 另外可以設定一個讓browser 在一個時間限制下自己去砍掉cookie
	- 一個是一個到期日 expire
	- 一個是多久 max-age
	- 如果有設定expire / max-age，這種算是 persistance cookie, 關掉瀏覽器， cookie 不會砍掉
	- 反過來，如果沒設，叫做 session cookie, 瀏覽器關掉，cookie會被砍掉




例子
![[IMG-cookie 的設定-20250810200233090.png]]



ref:
- [Day14 - 守護餅乾大作戰（一）](https://ithelp.ithome.com.tw/articles/10274463)
- [Day15 - 守護餅乾大作戰（二）](https://ithelp.ithome.com.tw/articles/10275178)
- [Day16 - 守護餅乾大作戰（三）](https://ithelp.ithome.com.tw/articles/10275651)