see notion: [session and cookie](https://www.notion.so/nture4388/session-and-cookie-1249df69750f80689ef9c4403d7a0da7?pvs=4)
[[第一方 cookie vs 第三方 cookie]]



what is session? 一段時間的狀態

因為 http 是無狀態, 我們要透過 cookie + session 來讓 server and client 可以建立起狀態
cookie, 是一個存在瀏覽器的資料, 共可以放 4kb

![[IMG-session and cookie-20250810200242886.png]]

![[IMG-session and cookie-20250810200244348.png]]



原則，data 存在 server 端
![[IMG-session and cookie-20250810200245647.png]]
PS: 通常 sid 會用 data store/redis 那邊用亂碼的方式產生




整個機制:
1. benHu login
2. server save session_id into db for benHu
3. server 會 `set-cookie: session_id=42` to Browser client
4. 瀏覽器會自動把cookie存起來
5. Browser go to /orders with `cookie:session_id=42`
6. server check db and know it’s benHu 然後就可以進行客製化benHu的行為
![[IMG-session and cookie-20250810200247058.png]]