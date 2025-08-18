[[wemo_note]]
[[BE - 阻擋與封鎖名單機制 - Plan and Prepare API doc]]



# main process
- login: 用idcard and devideID 換 sentry token
- phone:用 sentry token and phone number 可以發 totp 簡訊
- phone/verify: 用 top來確認是否是本手機
- exchange: 確認成功，拿 sntry token 換 access and refresh token



app check機制?
- login 就會帶上 X-Firebase-AppCheck 的token
- 驗證機制通過，會回傳 success
- [x] appCheck token 帶上來， BE 好像沒做事情吧?
	- BE does do sth!
		- be call firebase admin with this token to ask firebase to verify the device integrity
```ts

await firebaseAdmin.appCheck().verifyToken(firebaseAppCheckToken)

```



- after the phone api invoke:
	- we will aslo gen a token to pass to the client
	- client need to use this token and the totp code to verify against the BE
	- [x] why we need to gen a token to do this?
		- token is like a 號碼牌(number)
		- you can use this number to justify your identity
		- 妳給我手機，我會發給妳token, 但上也會給妳一個 號碼牌
		- 這個 number 是你"第一次"跟我要的時候我發給妳的
		- "下次" 妳用這個號碼牌，我就知道你是"那時候"我發的那個人
		- 本質上，是認 號碼牌, 不認 人
		- 然後 totp 本身是認手機，不認人
		- 因此 totp 是驗證手機是發出 sms 的那個手機
		- 而token 是驗證過去那個時間點申請這個驗證碼的那個身分  -> 比較像是一個以可以"跨時間"的概念上的驗證



# 登入流程
輸入 idcard 後
**POST /v23/users/login HTTP/1.1**
X-Firebase-AppCheck: eyJraWQiOiJNbjVDS1EiLCJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiIxOjcwMjY2MTY4OTk1Nzppb3M6NjA0YmZjMWIyZjYwZGZiMyIsImF1ZCI6WyJwcm9qZWN0c1wvNzA

{
  "idCard": "A182688454",
  "deviceId": "B3ACE54B-7D37-416B-8145-797EEBDEC990",
  "fcmToken": "c5VKBP8fNYBvc_hAfFkITZ2N-npf5nXjKniRPWsr2ERcY4kSUvsAjBfpiqZPZygK16RyociX"
}


{
  "result": "success",
  "data": {
    "authFlow": "register",
    "wemoSentryToken": "6yVnHQ6GcwIL1cEhNt0Lf8/MzQ09lqwAaVsVBYfLM30="
  }
}



--
輸入手機後打這個
## **POST /v23/users/me/phone HTTP/1.1**
Authorization: WeMoSentry 6yVnHQ6GcwIL1cEhNt0Lf8/MzQ09lqwAaVsVBYfLM30=

{"phone":"+886935533369"}


{
  "result": "success",
  "data": {
    "verifyToken": "3AOG/5//ylGGue5IqGdX1rAP6ReDktAnsKgxtqJL0Mg=",
    "maskedPhone": "+8869*****369"
  }
}


- 可以用 wemoSentryToken as redis key
- 放入 set
- 如果同一個 sentryToken下，set裡面有三組"以上"不同的 phoneNumber -> 拋錯


手機填完，會把到 angelia SMS 系統
然後使用者需要輸入 TOTP, verifyCode
輸入後，會打下面這個
## **POST /v23/users/me/phone/verify HTTP/1.1**
Authorization: WeMoSentry 6yVnHQ6GcwIL1cEhNt0Lf8/MzQ09lqwAaVsVBYfLM30=
{
	"verifyToken":"3AOG\/5\/\/ylGGue5IqGdX1rAP6ReDktAnsKgxtqJL0Mg=",
	"verifyCode":"666666"
}


{"result":"success"}


## **POST /v23/users/me/exchange HTTP/1.1**
Authorization: WeMoSentry 6yVnHQ6GcwIL1cEhNt0Lf8/MzQ09lqwAaVsVBYfLM30=
{}

{
  "result": "success",
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIyMDE0NzYiLCJ1c2VyX2lkIjoiMjAxNDc2IiwidmVyc2lvbiI6IjIuMCIsImlhdCI6MTcxODk3NTM5NSwiZXhwIjoxNzE5NDA3Mzk1fQ.ltGu1YYNwY2rx16Kp2R5cM5AJ6q9dkm_xoNUgh-r4GM",
    "refreshToken": "22EB6CE010E26F8DD032ED3B787B84037F8A50D86A1A34AF02DC2D3394BD217B"
  }
}
also refer to [[wemo users me exchange]]



# 如果輸入錯誤的驗證碼
如果你又輸入錯誤的又按 enter
## **POST /v23/users/me/phone/verify HTTP/1.1**
Authorization: WeMoSentry YpXM+dOR5q6VX6+lJVvNk145yStssuVRCwJw627x9eU=
{
	"verifyToken":"vJ+e1RIqp\/So40lWSaGmeyaZyfkWrBd8JCuJuYSfbMI=",
	"verifyCode":"333333"
}

{
  "result": "fail",
  "error": {
    "type": "VERIFY_SMS_FAILED"
  }
}



會打 POST /v23/users/me/phone/verify HTTP/1.1 -> 結果一樣回 VERIFY_SMS_FAILED


如果按 "重新傳送驗證碼", 會呼叫
## **POST /v23/users/me/phone HTTP/1.1**
Authorization: WeMoSentry YpXM+dOR5q6VX6+lJVvNk145yStssuVRCwJw627x9eU=
{"phone":"+886935521223"}

{
  "result": "success",
  "data": {
    "verifyToken": "Oc2nhhlz8MF1mVPRIMNkWMKDoN1WZzjn9AI1Ljoecdk=",
    "maskedPhone": "+8869*****223"
  }
}

所以重新傳送驗證碼是同一個手機
因此如果同一個手機發驗證碼，10分鐘，三次，就跳錯


---


# link
- [docs.google.com/document/d/1\_qRpvri3BCf4NBgF6CL6wRuurkIO81Rg0sZnOukQ\_b0/edit](https://docs.google.com/document/d/1_qRpvri3BCf4NBgF6CL6wRuurkIO81Rg0sZnOukQ_b0/edit)
- [Figma](https://www.figma.com/board/OpzZy9GGki5tsYXIaj5GOw/%E8%A8%BB%E5%86%8A%E6%B5%81%E7%A8%8B%E5%84%AA%E5%8C%96?node-id=0-1&t=g5FpTw5QRHjE3AAe-0)
- [How to build a Rate Limiter using Redis](https://redis.io/learn/howtos/ratelimiting)


