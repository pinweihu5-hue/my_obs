- related:
	- 可以找找看 jack 那個 文件



key idea:




## Alex

需求
- 一個系統可以發送到 sms, email and mobile push notifiation
- soft real-time, 就是不需要"馬上", 但要盡量馬上
- 可以 client and server side 都可以 trigger
- user can opt-out/in
- 10 million mobile push notifications, 1 million SMS messages, and 5 million emails



high level design

### notification 的種類

基本上目前都會使用第三方
APNS 就是 apple 的 mobile push notification 的服務
sms 我們家就是用 every8d
![[IMG-a notification system-20250810200231441.png]]




### user info 資料流

一開始註冊就會把資料 save into user 表 (email, phoneNumber)
![[IMG-a notification system-20250810200231875.png]]

if we want to support multiple device, schema:
![[IMG-a notification system-20250810200232678.png]]



### notification flow


我們會有很多 AP

另外有一個 microservice 處理 notification ( pod in k8s, kind: deployment, service and HPA)

利用 queue
- 每一個 queue handle 不同類型的 notification
- 當然如果有一些其他需求，類似我們有一些比較需要即時的訊息，那 pri 要高一點，不需要即時的，那就低一點，那就可以開多個 pq 處

queue 本身有 
- priority 機制
- retry 機制
	- at least one design (bull queue) -> 留意 idempotent
- rate limit
- more can refer to bull queue

有兩種rate limit
- 一個防止惡意攻擊，因此設定流量
- 一種是per user level, 可以防止對一個使用一天放太多訊息

queue 本身也可以有monitor 機制 setup and dashboard

![[IMG-a notification system-20250810200233559.png]]


如果需要讓使用者 opt-in/out, 可以設計一個 entity 來控制

| col_name | type    | 說明                              |
| -------- | ------- | ------------------------------- |
| user_id  | bitInt  |                                 |
| channel  | varchar | push notification, email or SMS |
| opt_in   | boolean | opt-in to receive notification  |


==
下面的圖加入:
- use level rate limit
- 只有 verified client 才會發送(避免惡意攻擊)
	- e.g. [App Check and Play Integrity to protect your apps and APIs](https://medium.com/firebase-developers/protecting-apps-and-apis-a-deep-dive-into-firebase-app-check-and-play-integrity-7364f96aa96d)
- 加入分析系統
	- send to bigQuery
- 加入 notificaiton template
![[IMG-a notification system-20250810200234585.png]]




## link
[1] Twilio SMS: https://www.twilio.com/sms
[2] Nexmo SMS: https://www.nexmo.com/products/sms
[3] Sendgrid: https://sendgrid.com/
[4] Mailchimp: https://mailchimp.com/
[5] You Cannot Have Exactly-Once Delivery: https://bravenewgeek.com/you-cannot-have-exactly-once-delivery/
[6] Security in Push Notifications: https://cloud.ibm.com/docs/services/mobilepush?topic=mobile-pushnotification-security-in-push-notifications
[7] RadditMQ: https://bit.ly/2sotIa6