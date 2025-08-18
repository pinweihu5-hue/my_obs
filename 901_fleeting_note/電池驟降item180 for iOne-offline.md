---  
alias:  
creation_date: 2024-06-11 15:59  
tags: 
related:
- [[_wemo_tickets]]
- [[item 權限, 觸(Accessible)和視(visible)]]
---  


issue
[[wemo_issue]]


結論：
k8s上了，但 pm2 沒上
k8s and pm2 要 deploy 都要手動在 gitlab 畫面上按下按鈕
![[IMG-電池驟降item180 for iOne-offline-20241003104934141.png|478]]


local debug cron job and pm2

change script to
```json

"scripts": {
    "dev": "cross-env TZ=UTC NODE_ENV=development node ./pm2_cron/serverTimers/every10minsAllInOne.js",

```


把不需要的地方的 comment 掉
- 第一個要 comment 掉的地方是寫入 db 的地方
- 或是其他 earlier return, 如果無關，也可以 comment 掉，因為我們要看的是 scooter


測試
- 找一個scooter 是 vehicle model is '2'
- 去 mne 建立一個 item 180 讓他可以被 check and update 抓到

```sql

INSERT INTO
    "mnemosyne-qat".maintenance (
        "isActive"
        , "isUnique"
        , "text"
        , "resolvedAt"
        , "verifiedAt"
        , "scooterId"
        , "itemId"
        , "creatorId"
        , "resolverId"
        , "verifierId"
        , idle
        , "rentId"
        , "managerCreatorId"
        , "managerVerifierId"
        , "vehicleModelId"
    )
VALUES(
    TRUE
    , TRUE
    , NULL
    , NULL
    , NULL
    , 'QAT-0120'
    , 180
    , NULL
    , NULL
    , NULL
    , NULL
    , NULL
    , NULL
    , NULL
    , '2'
);


```





歷程


Jun 21, 2024 @ 00:30:04.731	/v2/boxes/TWBB60D024A100319/maintenances
{"pwd":"reNgqjuo7YAwy4TE2eRyiyMnDbHFvy17QGXskb0YT0IkGtbf6qQooeTeDX1VmYqdf13jgWdJLNik6EUFXW5VGCyUEXS8OmhwQCXoO3m3vY0CQzxDL6PkyXbpmM9gqfErReXQQjLcA8R190uqLu3X0imku6egtgLVvergyFrc0axWcsZnMPcn7uxAxFnEmKhA2MNj3dlbjof4fmlNlejhTSxu7BH93Bn5vNYQYtPOGYhmLkX3DjO9nec91bQbj9KE","itemIds":[180]}


Jun 21, 2024 @ 04:24:06.901 /v2/boxes/TWBB60D024A100319/maintenances	{"pwd":"reNgqjuo7YAwy4TE2eRyiyMnDbHFvy17QGXskb0YT0IkGtbf6qQooeTeDX1VmYqdf13jgWdJLNik6EUFXW5VGCyUEXS8OmhwQCXoO3m3vY0CQzxDL6PkyXbpmM9gqfErReXQQjLcA8R190uqLu3X0imku6egtgLVvergyFrc0axWcsZnMPcn7uxAxFnEmKhA2MNj3dlbjof4fmlNlejhTSxu7BH93Bn5vNYQYtPOGYhmLkX3DjO9nec91bQbj9KE","itemIds":[161]}


Jun 21, 2024 @ 04:29:45.415	/v2/boxes/==TWBB60D024A100319==/==maintenances==/towing
{"coordinate":[25.0463,121.55071],"pwd":"reNgqjuo7YAwy4TE2eRyiyMnDbHFvy17QGXskb0YT0IkGtbf6qQooeTeDX1VmYqdf13jgWdJLNik6EUFXW5VGCyUEXS8OmhwQCXoO3m3vY0CQzxDL6PkyXbpmM9gqfErReXQQjLcA8R190uqLu3X0imku6egtgLVvergyFrc0axWcsZnMPcn7uxAxFnEmKhA2MNj3dlbjof4fmlNlejhTSxu7BH93Bn5vNYQYtPOGYhmLkX3DjO9nec91bQbj9KE","zoneId":0}


![[IMG-電池驟降item180 for iOne-offline-20241003104948975.png]]


Jun 21, 2024 @ 08:58:43.423	
log:
set scooter EWN-0293 status to 0 because it has items which user cannot access

Jun 21, 2024 @ 09:23:07.671	
[{"scooter":{"id":"EWN-0293"},"item":{"id":1},"text":" 電量驟降回廠 "}]

Jun 21, 2024 @ 09:23:07.700	
set scooter EWN-0293 status to 0 because it has items which user cannot access: 1

有其他機制讓他回到 status = 0 ??

check if any one online it?
https://apollo-qat.wemoscooter.com/hermes/api/service-console/scooters
PATCH
{"patch":{"status":"online"},"scooters":["QAT-xxx"]}
=> no record for EWN-0293


check and update .js 
if item 180 is being drop -> then it will switch back to 0
- [ ] anyone drop 180 from scooter?

- [ ] 上 maintainitem



因為目前 
1. 如果上 180 後，然後改 scooter status = 0，應該不存在其他機制在 180 is still active (如下 table 的 isActive) 下可以讓scooter status 切回 2 嗎？
2. 根據下圖顯示:
	1. 180 被 2024-06-20 16:30:04.678 被 box 上了
	2. verified at 是 null 表示沒有被解掉
	3. 以上理解正確嗎?
	4. 180 最後一次 2024-06-21 03:17:45.023 被 upate (不太懂這塊)
![[IMG-電池驟降item180 for iOne-offline-20241003105013807.png]]


9:25 才變成 0
![[IMG-電池驟降item180 for iOne-offline-20241003105013908.png]]


[docs.google.com/spreadsheets/d/1BrpyYXyN4AWpuWnGLFl13q39r9SPMXtB3mFKC41jU80/edit?gid=1645070502#gid=1645070502](https://docs.google.com/spreadsheets/d/1BrpyYXyN4AWpuWnGLFl13q39r9SPMXtB3mFKC41jU80/edit?gid=1645070502#gid=1645070502)






---

# todo

- [x] test on QAT
	- [x] ione
	- [x] candy
- [x] code review
- [x] chat
	- [x] 拉其他人進來

- [x] use tdd
- [x] 要改checkAndUpdate -> item180 for iOne need to offline

- [x] ask josh need to do this? (think before ask)
	- [x] 要改 GET /v23/scooters -> item180 for iOne need to offline
		- [x] exports.listById = async (id, user, { lang, updateBusinessState })

- [x] 跳通知功能
	- [x] 我有把你加到 Auxiliary Battery Low 的 chat (prod/qat) 你可以仿製
		- [x] or check water MR
- [x] refactor




# 優化


如果你的 taks 不重要, 類似通知
可以放到最後跑
先塞到 arr 裡面
後面慢慢 promise

確保你如果這邊爆掉，不要影響到其他人 -> 加上 try-catch
直接放到最後

1. 把 send msg 放到邏輯最後面，因此 await 不會卡住其他主要邏輯。
2. send msg 另外包上 try-catch, 因此如果通知爆掉，不會 throw error for checkAndUpdate










# ref

## 區分 ione and candy
```ts fold

  if (vehicleModelId === VehicleModelEnum.IOneFly) {
    if (state.scooter_trunk_open === true || state.power === 1 || state.batQty !== 2) {
      logger.error({ tag, msg: `boxReturnState: ${JSON.stringify(payload)}`, vehicleModelId })
      throw WeMoErrorHandler.factories.newWeMoError(ErrorType.RENTAL_RETURN_CHECK_INVALID, { payload })
    }
  } else {
    if (state.power === 1) {
      logger.error({ tag, msg: `boxReturnState: ${JSON.stringify(payload)}`, vehicleModelId })
      throw WeMoErrorHandler.factories.newWeMoError(ErrorType.RENTAL_RETURN_CHECK_INVALID, {
        payload: R.pick(['power', 'reason'], payload)
      })
    }
  }

export enum VehicleModel {
  Candy = '1',
  IOneFly = '2'
}


```





## 為何要做 scooter list 那邊
哪邊會呼叫 checkAndUpdate
因為 bb 上了item -> put
會呼叫 checkAndUpdate
那就夠了





## chat 參考
abnormal reservation
chat 線上 UI 生成 code: [UI Kit Builder](https://addons.gsuite.google.com/uikit/builder?hl=zh-tw)


## 呼叫 checkAndUpdate 的地方
```ts


// ota 
BatteriesService.prototype._updateOTAItem

exports.ota


// maintenances 相關
/v2/boxes/controller/maintenances/create

// put
const tag = 'v2/maintenances/put'

// new create
async function createMaintenanceAtNew(req, res, next)




// 強還 
const forceReturn = (req, res, next)

 // 還車正流程
 export async function returnScooter(



// 下面都是 service app 
async function finishById(req, res, next)

async function resolveMaintenanceById(req, res, next)

async function revokeMaintenanceById(req, res, next) {

async function verifyMaintenanceById(req, res, next) {

export async function cancel(

export async function reserve(



// worker 相關
 async function assignWorker(req, res, next) {

const postServiceAtNewOfMe: WeMoRouter = async (req, res, next) => {



// every10 min
pm2_cron/serverTimers/every10minsAllInOne.js:186


// force online
services/scooters/forceOnline.js:175








```