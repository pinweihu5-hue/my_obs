



- [[Oauth]]
- [[how base64 work in details]]
- [[how base64 work in short msg]]
- [[對稱加密]]
- [[非對稱加密]]
- [[ssl cert 實作]]



加解密 and encode/decode
- 雙向
- encode/decode
	- hex(1~9a~f) and base64(a-z, A-Z, 0-9, =+)
	- base64
		- base62 + 兩個特殊字符（+ 和 /），並且通常會使用 = 來填充。
		- HTTP 請求中傳遞圖像或文件，會把這些 binary 透過 base64 轉為文本格式
		- 壓縮率比 hex, binary高多了(64 vs 16 vs 2)
		- 通用於網路，不包括非法字元
			- 非法字元?
				- 控制字符：如 \n、\r、\t 等，這些字符可能會干擾網絡協議或是系統的正常運作
				- 特殊字符：如 <、>、& 等，這些字符可能會被解釋為 HTML 標籤或是其他特殊語法
				- 非 ASCII 字符：如中文、日文等，這些字符可能會因為編碼問題而導致傳輸錯誤
		- [[how base64 work in details]]
	- base62
		- 大小寫字母（A-Za-z), 數字（0-9），共62個字符
		- avoids special characters (like +, /, or = in Base64), 因此在 URL 中使用時不需要進行額外的編碼和escaping，更適合用於生成 shortURL
	- URL 編碼（Percent Encoding）
		- 將 URL 中的特殊字符轉換為可傳輸的格式。
		- 使用百分號（%）後跟兩位十六進制數字表示字符。例如，空格被編碼為 `%20`




加解密
- 加密就可以解密，也是雙向
- 常見的包括
- 對稱: like AES (fast, but need to transfer the secert)
	- [[對稱加密]]
- 非對稱: like RAS  (slow, but can trasnfer the public key)
	- [[非對稱加密]]




 hash
- 單向，因此系統/db可以存 hash 過後的 password, 且系統無法反過來知道 password
- 會加上 salt → 避免被查彩虹表破解
- 不要用ms5 → 有限時間可以被彩虹表破 → 至少用 sha256
- 如果 perf 沒有考慮，可以用 bycrpt, 可以 hash 疊加多次，這樣多一個維度，讓彩虹表更難破
-  [[cs61b Hashing]]
- [ notion - hash](https://www.notion.so/nture4388/hash-hashMap-32b2de7517d744a0928f328a4f458ac2?pvs=4)




- [[XSS]]
- [[CSRF]]
- [notion note on Burpsuite Academy](https://www.notion.so/nture4388/Burpsuite-Academy-c920a33e87f3467480c40044965bcce4?pvs=4)




---



[(592) one of the craziest exploits i've ever seen - YouTube](https://www.youtube.com/watch?v=89ysXVYH2Sk)
- a one pixel img contain below hacking scheme
- webp lib, the bug is abt no length check when create a huffman table (when it unpack it over and over again)
- so hacker is able to create a table in a way to have a bufferoverflow in the bss in the lib webp -> can lead to double free, a heap exploit and can conduct rce


[(592) secret backdoor found in open source software (xz situation breakdown) - YouTube](https://www.youtube.com/watch?v=jqjtNDtbDNI) & [(592) revealing the features of the XZ backdoor - YouTube](https://www.youtube.com/watch?v=vV_WdTBbww4)
- ref: [oss-security - backdoor in upstream xz/liblzma leading to ssh server compromise](https://openwall.com/lists/oss-security/2024/03/29/4)
- open source project but have backdoor in it
- how?
- it's a xz (compressed file) in test folder
	- so you can not easily scan code and find it, you need to go futhur step and check each binary file, zipped file in test folder!
	- that file, unzip it, you will find the code unzip another file 
	- in this file, is a script , this script is a hook to the build file(when build, this script will run sth)
	- and in it, there's .o file (.o is the middle step of C compliation process,    .c -> .o -> exectuable)
	- this .o will be binding to the program via dynamic linker in build exetuable process
	- and this .o is any code hacker can run when you run xz
	- 



[(592) malicious javascript injected into 100,000 websites - YouTube](https://www.youtube.com/watch?v=bbatLr98fEY&t=70s)
- polyfil, or polykill attach
- a chinese company buy a domain polyfill.io which serve a polyfill via cdn
- and over 100,000 website use this cdn for polyfill util
- when you visit this website, the this polyfill js run
- inside this minial js, there is a code will run a another js file, called googie.analysic script
- in this script, it's all js and obsiudcate
- this code shall be a payload, which at its extreme, can possible exploit v8 bug and escape from v8 sandbox and RCE on ur computer
- related link
	- [GitHub · Where software is built](https://github.com/polyfillpolyfill/polyfill-service/issues/2873)
	- [Exploiting V8 at openECSC Ʊ lyra's epic blog](https://lyra.horse/blog/2024/05/exploiting-v8-at-openecsc/)



[(592) new SSH exploit is absolutely wild - YouTube](https://www.youtube.com/watch?v=Rj3sTAMYNQk)
- [regreSSHion: Remote Unauthenticated Code Execution Vulnerability in OpenSSH server | Qualys Security Blog](https://blog.qualys.com/vulnerabilities-threat-research/2024/07/01/regresshion-remote-unauthenticated-code-execution-vulnerability-in-openssh-server)
- open SSH,  a recent ver just remove a header and this bug 又跑出來
- race condition
	- 特定時間去打 sig alarm
	- 會讓heap的某一個區間可以被 access，因為還沒有被 maclloc 掉
	- 透過 send different key 多次去建立讓這個heap區間可以注入code來達到 RCE
	- 32bit and aslr系統約 6-8 hr 的try
	- 64 bit可能要很多天 try
