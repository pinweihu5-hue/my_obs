



Most common are:
- Content Security Policy 限制特定資源只能在那個domain load (like JS 只能在benhu.com)
- HTTP Strict Transport Security  一定時間內一定要走 https, 包括子網頁也要
- X-Content-Type-Options: nosniff 不要brower去偵測，一切找 content-type的定義走
- X-Frame-Options  我的網頁不要讓人內嵌



```jsx

res.cookie('cookie_name', 'jack', {
    httpOnly: true, // not allow js to read document.cookie
    secure: true    // only allow cookie to transfer under https
})

res.setHeader('X-XSS-Protection', '0') // disable xss filter

// enable xss filter and browser will remove suspecious part
res.setHeader('X-XSS-Protection', '1') 

// enable xss filter and browser will not render if it dectect

res.setHeader('X-XSS-Protection', '1; mode=block')
// enable xss filter and will also report to specified uri 
res.setHeader('X-XSS-Protection', '1;report=https://www.example.com')

res.setHeader(
	'Content-Security-Policy', 
	"default-src 'self';  // only default to allow read our own site

	// only allow script src to our own site and *.google.com
	script-src 'self' *.google.com 'unsafe-eval'; 

  // only allow img-src to our own site and *.amazonaws.com
	img-src 'self' *.amazonaws.com data:")

// 不允許被任何網站用 iframe 的形式嵌入的
res.setHeader('X-Frame-Options', 'DENY')

// 允許同源底下的網站，用 iframe 方式嵌入
res.setHeader('X-Frame-Options', 'SAMEORIGIN')

// 設定白名單的 list
res.setHeader('X-Frame-Options', 'ALLOW-FROM https://example.com')

// 防止 Content-Type 被竄改, 'nosniff' is the only value to set
// to tells a web browser to block a webpage from loading 
// if the browser detects that the webpage is not being served 
// with the correct MIME type.
res.setHeader('X-Content-Type-Options', 'nosniff')

// forece to use https
// even user use http, it will forward to use https 
res.setHeader(
	'Strict-Transport-Security', 'max-age=16070400; includeSubDomains')

// add pricivy

// won't record your last visit site/ which site you come from
res.setHeader('Referrer-Policy', 'no-referrer')

// it's okay to record all
res.setHeader('Referrer-Policy', 'unsafe-url')

// other options:
no-referrer
不允許被記錄下來

origin
只有紀錄 origin，例如在 https://example.com/a.html 底下，
只會傳送 https://example.com

strict-origin
只有在 HTTPS->HTTPS 之間才會被記錄下來

no-referrer-when-downgrade (default)
跟 strict-origin 一樣

origin-when-cross-origin
只有在 CORS 的時候，referrer 才會被送出，但只有 origin

same-origin
CORS 的時候，referrer 不會被記錄，同源的時候會有 origin

strict-origin-when-cross-origin
只有在同源的時候才會送出 referrer，而且還是要 HTTPS -> HTTPS

unsafe-url
不管怎樣都送就對拉

// prevent mimt
// trust to a specfict cert
// after create your own cert, replace below XXX
res.setHeader(
'Public-Key-Pins', 'pin-sha256="XXX"; 
max-age=2592000; includeSubDomains')
```




ref: 
- [Day11 - 記得要戴安全帽（一）](https://ithelp.ithome.com.tw/articles/10272394)
- [Day12 - 記得要戴安全帽（二）](https://ithelp.ithome.com.tw/articles/10273029)
- [Day13 - 記得要戴安全帽（三）](https://ithelp.ithome.com.tw/articles/10273734)
- [Scan results for https://yu-jack.github.io/2021/07/19/interview/](https://securityheaders.com/?q=https%3A%2F%2Fyu-jack.github.io%2F2021%2F07%2F19%2Finterview%2F&followRedirects=on)
- [ref](https://yu-jack.github.io/2017/10/20/secure-header/)