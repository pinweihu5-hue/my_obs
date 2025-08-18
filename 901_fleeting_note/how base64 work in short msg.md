好知識，現在解釋為什麼會這樣。

{" 是 ASCII 01111011, 00100010

 
Base64 編碼採用 3 位元組 x 8 位元 = 24 位，將這 24 位元序列分成四個部分，每部分 6 位，然後將每個部分轉換為 0-63 之間的數字。如果位數不足（我們只有 2 位元組 = 16 位，需要 18 位），則用 0 填入。當然，實際上最後兩位會取自 JSON 字串的第 3 個字符，而這個字符是可變的。

前 6 位是 011110，十進制為 30。

第二個 6 位是 110010，十進制為 50。

最後 4 位是 0010。用 00 填充，得到 001000，即 8。

使用編碼表（ [https://base64.guru/learn/base64-characters），30](https://base64.guru/learn/base64-characters) 是 e，50 是 y，8 是 I。這就是「ey」。


ref:
[Spotting base64 encoded JSON, certificates, and private keys | Hacker News](https://news.ycombinator.com/item?id=44802886#44803260)