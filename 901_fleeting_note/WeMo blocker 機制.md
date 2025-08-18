- [BE Phone Number 阻擋與封鎖名單機制](https://docs.google.com/document/d/1ziRuI1WJpqosTHBjyk8-getSaScJlRcTUbPbeCacH1Q/edit?hl=zh-tw&tab=t.0#heading=h.l6pp6k2uhniq)



- src code key word: AbstractBlocker
- 主要用到 hset + multi, 來包住多個操作


[] 
## overall logic flow

POST /v23/users/me/phone API


register flow: sendAuthSmsForSentry(userSentryInfo: SentryInfo, payload: SendAuthSmsRequest)
- run rate limiter 1 & 2
login flow: sendAuthSms: ControllerWeMoWithRequestBody
- run rate limit 2 only
    


## rate limit1
- 連續傳送三個不同的驗證碼就會 throw err -> 回到登入頁
- POST /v23/users/me/phone
- user input “phone number” and then click “verification code”/傳送驗證碼
- under the same sentryToken, check if we have over 3 “changed” -> throw error
- no set phone number into block list, just throw error and force user need to start from begin to get the sentry token again
- we will not block phone in this case, just also add to table for a record
- endAt is also currentTime in this case
- other logic follow the same logic

例子
- ABC -> 算3次
	- “”->A, A->B, B->C
- AAB -> 算2次
	- “”->A, “A”->B
- AAA -> 算1次
	- “” -> A
    

PS: 下面 rate limit1 的設計, 本來用 string 如下, 後來全部改用 hset 來記錄更豐富的資訊

logic
- key: Register:PhoneChanged4TimesInSentryToken:{sentryToken}
- value: int
- 
- if key is not exist
	- set value = 1 and TTL set to current default sentry token TTL
- if key is exsit
	- if value < 3
		- value incr
	- if value >=3
		- add to db block list
		- throw error (status 401)
    
    

## rate limit2
- click “resent verification code” “重新發送驗證碼”  
- 同一個手機號碼連續點擊三次 "“重新發送驗證碼”  " -> 鎖 180 分鐘
- 因為要可以後台解鎖，所以需要存入 db
- check if the same phone number is within a 10 min window? -> the 4th time, throw error and block this phone number for 180 min  
- this is cross sentry token, if the user use different sentry token, still continue count

logic
- key: PhoneRepated4TimesIn10Min:{phone}
- value: shset 

```js

{
	endAt: xxx // can we remove this?, only use isBlocked?
	isBlocked: xxx
	counter: 1
}

```


- if key is exsit in redis
	- if isBlocked is true -> throw err  (如果惡意攻擊，這邊就噴了，不會打到 db, 會讓 redis 扛)
- if key is not exist in redis
	- if phone is in block db
		- get db data, re-calcualte ttl, update DB and redis(isBlocked and ttl) -> throw err
	- if phone is not in block db
		- incr by 1
		- if counter < 3, counter increment (hinc or redis method)
		- if counter >=3
			- set isBlocked to true
			- entend ttl to 180 min
			- add to db block list
			- throw error (status 401)
		    
