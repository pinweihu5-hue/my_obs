

# BE Phone Number 阻擋與封鎖名單機制

[原本文件](https://docs.google.com/document/d/1kSkbPFBe_-DlYeMCqGilLvoxFE87eebfMYD8YrzpJNU/edit?tab=t.0)
  

## DB migration

[Apollo - UI](https://www.figma.com/design/8GORW3KqwUKkyB1QrXCMf7/Apollo?node-id=9650-2&t=tjOQqY9iWYZnDv8p-0)

PM DOC: [[PRD] 註冊登入：新增異常阻擋流程](https://docs.google.com/document/d/1_qRpvri3BCf4NBgF6CL6wRuurkIO81Rg0sZnOukQ_b0/edit#heading=h.v15y0lwjgatd)

[Flow Doc](https://www.figma.com/board/OpzZy9GGki5tsYXIaj5GOw/%E8%A8%BB%E5%86%8A%E6%B5%81%E7%A8%8B%E5%84%AA%E5%8C%96?node-id=2277-24528&t=tgvjzob44YaI8nS8-0)

### table name: block_record

  

|   |   |   |   |
|---|---|---|---|
|Col name|data type|comment|idx|
|id|bigserial|primary key<br><br>unique|yes|
|beginAt|timestamp|when to start block, sort|yes|
|endAt|timestamp|nullable<br><br>null means block indefinitely|yes|
|updatedAt|timestamp|nullable<br><br>auto genernation||
|blockManagerId|varchar|nullable<br><br>managerId to add record to this table||
|unBlockManagerId|varchar|nullable<br><br>managerId to unblock record to this table||
|flow|int|enum: 註冊登入流程 registerAndLoginFlow，修改電話流程PhoneChangingFlow<br><br>nullable<br><br>null when manager create|yes|
|rule|int|not null<br><br>enum: PhoneChanged4Times, PhoneVerifyResend4timesIn10Min|yes|
|blockTarget|varchar|ex: 09376765112, myhome@gmail.com, …etc|yes|

- key:
	- PhoneVerifyResend4timesIn10Min:{phone},
	- PhoneChanged4Times:{sentryToken}
- a cronjob to remove old record
- remove if endAt is 3 month old (like sendRecord in Angelia)
    

  
  

query 被 blocked 的資料上會比較醜，也會用到我們討厭的 or
where endAt > now or null

- [ ] how abt use +100 yrs?
- not 100 yrs expire, it’s +100 yrs expire
- [pg timestamp store 64 bit,](https://medium.com/@honglong/postgresql-timestamp-%E8%B3%87%E6%96%99%E5%9E%8B%E6%85%8B%E7%9A%84%E6%A5%B5%E5%A4%A7%E5%80%BC-5ca661f896bb) no unix (32 bit) timestamp problem

  
  
  
  

### db access pattern

Apollo
- query all records to show on apollo
	- where
		- condition is PhoneVerifyResend4timesIn10Min
		- currentTime within beginAt and endAt
- manager add phone into block table
	- beginAt = now
	- endAt = null
	- blockTarget = 09376765112
	- blockManagerId = managerId
	- condition = PhoneVerifyResend4timesIn10Min
- manager unblock phone
	- where
		- blockTarget = 09376765112
	- update to:
		- endAt = now
		- updatedAt = now
		- unBlockManagerId = managerId
    

userApp
- on rate limiter2: check DB when redis found key is not exist
	- where
		- blockTarget is 09376765112
		- condition is PhoneVerifyResend4timesIn10Min
		- currentTime within beginAt and endAt
- system add record
	- blockTarget = 09376765112
	- flow = 註冊登入流程…etc
	- condition = PhoneVerifyResend4timesIn10Min, depends
	- beginAt = now
	- endAt = now + 180 min
    

### index

應對 user app query

複合 idx  
CREATE INDEX idx_xxxxxxxxxxxx  
ON your_table (blockTarget, condition, beginAt, endAt);

## User App API

- 📑  Planning
    
- ⛑️  Working On It
    
- ✈️  Deployed to QAT, but code review is NOT DONE
    
- ✅  Done and Deployed to QAT
    

  

## ✅ 發送驗證簡訊 POST /users/me/phone (Auth Token: Sentry Token)

### Request Body: No change

### Response Body: No change

### Error Codes

|   |   |   |   |
|---|---|---|---|
|Status code|Error type|Description|BE suggestion|
|401|BLOCK_BY_RESEND_IN_TIME_WINDOW|重送次數到達上限||
|401|BLOCK_BY_REPEATED_CHANGES|更改號碼次數到達上限|BErevoke sentry token|

  

## [No change]發送驗證簡訊 POST /users/me/phone (Auth Token: WeMo Token)

### Request Body: No change

### Response Body: No change

### Error Codes: No change

  

## [No change]發送驗證簡訊 POST /users/me/phone/verify

## Auth Token: WeMo Token / Sentry Token

### Request Body: No change

### Response Body: No change

### Error Codes: No change

  
  

## Apollo API

- 📑  Planning
    
- ⛑️  Working On It
    
- ✈️  Deployed to QAT, but code review is NOT DONE
    
- ✅  Done and Deployed to QAT
    

  

## ✅ 取得封鎖列表 GET /blocklist

- get currently blocked data for manager
    
- manager created if flow is null
    
- BE note: can consider use pagination design or not
    

- but consider not to use CRUD pkg, which cause future upgrade harder
    
- maybe there’ no much data in this table, since we will have keep only 3 month of data
    

### Auth Token: Manager Token

### Query parameters

|   |   |   |   |
|---|---|---|---|
|field name|data type|description|BE suggestion|
|limit|number, required, default 100|每頁資料數量||
|offset|number, optional,<br><br>default: 0|位移數量||
|rule|enum,required|查詢限制條件<br><br>1: 電話驗證重試超過四次<br><br>2: 同一個註冊流程中 (same sentryToken) 電話變更超過 3 次|目前需求來說固定帶 1 就可以了|
|blockTarget|string, optional|查詢限制條件|目前來說把電話號碼帶上來，沒有帶就是全部|
|isBlocking|boolean, optional|是否為生效中條件<br><br>1: 只顯示生效中<br><br>0: 只顯示未生效<br><br>null, undefiend: 不限制條件，包含生效與未生效|目前需求來看應該只要生效中？ 可以跟 PM 確認一下|

  

### Response body

{

  result: "success",

  data: [

    // example when system create a record for PhoneVerifyResend4timesIn10Min

    {

      id: "1",

      beginAt: "2022-08-01T00:00:00.000Z", // not null, ISO 8601 format

      endAt: "2022-08-01T03:00:00.000Z", // nullable, ISO 8601 format  ( null means ban forever)

      blockTarget: "09376765112", // not null, phone number, email, license plate, etc

      blockManagerId: null, // nullable if system create

      unBlockManagerId: null, // nullable if system create

      flow: 1, // 1. RegisterAndLoginFlow (註冊登入流程) | 2. PhoneChangingFlow (修改電話流程) | null when manager create

      rule: 1, // 1. PhoneVerifyResend4timesIn10Min (電話驗證重發4次) | 2. PhoneChanged4Times (電話變更4次)

      updatedAt: "2022-08-01T00:00:00.000Z", // not null, db auto-generated

    }

  ],

  meta: {

    total: 210, // 資料總數

    count: 25, // 本頁資料數量

    page: 10, // 目前第幾頁

    pageCount: 9, // 全部有幾頁

    limit: 100,

    offset: 0

  }

}

  

### Error Cores

|   |   |   |   |
|---|---|---|---|
|Status code|Error type|Description|BE suggestion|
|400|ZOD_VALIDATION_ERROR|資料欄位檢查錯誤||

  

## ✅ 人工新增封鎖 POST /blocklist

- create a record in block table
    
- flow is null if the record is created by manager
    
- also need to create redis record, currently the condition need to choose `PhoneVerifyResend4timesIn10Min`
    

  

### Auth Token: Manager Token

### Request Json Body

{

  "blockTarget": "+886912345678", // 限制目標 (目前需求應為電話號碼)

  "rule": 1 // 1. PhoneVerifyResend4timesIn10Min (電話驗證重發4次) | 2. PhoneChanged4Times (電話變更4次)

}

### Response body

{

  "result":"success", 

}

  

### Error Cores

|   |   |   |   |
|---|---|---|---|
|Status code|Error type|Description|BE suggestion|
|400|ZOD_VALIDATION_ERROR|資料欄位檢查錯誤||

  

## ✅ 解除封鎖 PATCH /blocklist/:id(\d+)/unblock

- unblock a record in block table
    
- ednAt update to `now`
    
- default is to block indefinitely
    
- remove redis record
    

### Auth Token: Manager Token

### Path parameters

|   |   |   |   |
|---|---|---|---|
|field name|data type|description|BE suggestion|
|id|number, required|資料 ID||

  

### Request Body

- No need
    

### Response body

{

  "result":"success"

}

  

### Error Cores

|   |   |   |   |
|---|---|---|---|
|Status code|Error type|Description|BE suggestion|
|404|NOT_FOUND|查無封鎖資料||
|400|NO_RECORDS_UPDATED|沒有資料可以更新|資料已經到期了不需更新|
|400|ZOD_VALIDATION_ERROR|資料欄位檢查錯誤||

  
  

## overall logic flow

[註冊流程優化 – FigJam](https://www.figma.com/board/OpzZy9GGki5tsYXIaj5GOw/%E8%A8%BB%E5%86%8A%E6%B5%81%E7%A8%8B%E5%84%AA%E5%8C%96?node-id=2277-24528&t=tgvjzob44YaI8nS8-0)

POST /v23/users/me/phone API

register flow: sendAuthSmsForSentry(userSentryInfo: SentryInfo, payload: SendAuthSmsRequest)

- run rate limiter 1 & 2
    

login flow: sendAuthSms: ControllerWeMoWithRequestBody

- run rate limit 2 only
    

## rate limit1

  
  
  

- user input “phone number” and then click 傳送驗證碼
    
- POST /v23/users/me/phone
    
- under the same sentryToken, check if we have over 3 “changed” -> throw error
    

- ABC -> 3
    

- “”->A, A->B, B->C
    

- AAB -> 2
    

- “”->A, “A”->B
    

- AAA -> 1
    

- “” -> A
    

- no set phone number into block list, just throw error and force user need to start from begin and acquire the sentry token again
    

logic

- key: Register:PhoneChanged4TimesInSentryToken:{sentryToken}
    
- value: number
    
- if key is not exist
    

- set value = 1 and TTL set to current default sentry token TTL
    

- if key is exsit
    

- if value < 3, value incr
    
- if value >=3
    

- add to db block list
    

- we will not block phone in this case, just also add to table for a record
    
- endAt is also currentTime in this case
    
- other logic follow the same logic
    

- throw error (status 401)
    

## rate limit2

click “重新發送驗證碼”  
check if the same phone coming in with 10 min window? -> the 4th time, throw error and block this phone number for 180 min  
this is cross sentry token, if the user use different sentry token, still continue count

logic

- key: PhoneRepated4TimesIn10Min:{phone}
    
- value: string or hset
    

{

endAt: xxx // can we remove this?, only use isBlocked?

isBlocked: xxx

counter: 1

}

- if key is not exist
    

- check if phone is db is still in block period -> get data, calculate shall be block TTL, update redis isBlocked and update TTL
    
- setup key and counter = 1 and TTL 10 min
    

- if key is exsit
    

- if isBlocked is true -> throw 401
    
- if isBlocked is false
    

- if counter < 3, counter increment (hinc or redis method)
    
- if counter >=3
    

- isBlocked -> true
    
- entend ttl to 180 min
    
- add to db block list
    
- throw error (status 401)
    

## handle different phone format

- we have below 3 phone formats (every8d accept)
    

- “886936675118”
    
- “0936675118”
    
- “+886936675118”
    

- need to treat them as the same one
    
- use regex to match below 3 case can convert to 936675118
    

## tasks

- create DB table
    
- setup rate limit core logic util
    
- use rate limit util in register and login flow
    
- apollo API (get block list, unblock user, add user into block list)
    
- cronjob (everyday) to check block table endAt to unblock ppl -> no ticket now
    

  
  

# time to vote

table name

- deniedList
    
- blockList    Jack 
    
- RestrictedAccessList
    
- RestrictedList
    

  
  

API PATH name

- firewall/deniedList
    
- waf/deniedList
    
- deniedList
    
- blockList
    
- 等等,  歡迎提供
    

  
  
  
**