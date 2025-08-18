---
aliases:
  - work-log
---

---

- [ ] Fwd: Kudos and farewell to all
	- [ ] [[wemo kudo]]








---





- 有哪些 table 的 schema 有變動或新增
- 新增的 table 的用法 裡面記錄什麼
- 新增 table 和 棄用 table 的關係
- deprecated col and table?
- 如果有回補，不需要去知道 migration logic 去對應




- [x] 如果 enum 有調整，我那邊 nexus backend 也要調整
	- [x] -1 disabled


to study or ask
why?
![[IMG-log_work (wemo)-20250810201310921.png]]

# backup
```ts fold

  async switchPowerSavingMode(
    nexusUserMeta: NexusUserMeta,
    param: { id: string; mode: string },
  ) {
    try {
      const result = await this.httpServiceHelper
        .post<HermesResponse<void>>(
          `/v24/tour/vehicles/${param.id}/mode/${param.mode}`,
          {
            userMeta: nexusUserMeta,
          },
        )
        .then(formatHermesResponse);
      return result;
    } catch (error) {
      throw this.handleProviderError(error, this.switchPowerSavingMode.name);
    }
  }



export enum Mode {
  NORMAL = 0,
  LIGHT_SLEEP = 1,
  DEEP_SLEEP = 2
}

export enum UpdateStatus {
  UPDATED = 0,
  UPDATING = 1,
  UNKNOWN = 2
}


    const res = await request
      .post(`/v24/tour/vehicles/${vehicleId}/mode/${mode}`)
      .set('Authorization', `${AuthType.Manager} dummyToken`)
      .set('Content-Type', 'application/json')


registerAuthEndpoint(table, {
  version: 24,
  method: 'post',
  endpoint: '/vehicles/:vehicleId/mode/:mode',
  controller: { manager: powerSavingModeController.switchPowerSavingModeFromManager }
})
```






本來的 API
POST v24/boxes/:id/powerMode/:mode



evan
- put into src/apis/hermes-proxy/controllers/tour/tour.controller.ts?
- 放在新的 module?
	- src/apis/hermes-proxy/controllers/boxes



可以查看當前車機睡眠狀態
後台可以進入省電，也可以喚醒
在車輛使用中(車輛租借期間)，不可透過後臺或任何方式讓車輛進入省電模式。


我可以查看當前車機睡眠狀態，方便協助用戶確認問題
我可以遠端切換車機睡眠狀態，方便協助用戶排除問題
當車機進入睡眠時我將無法操作其他指令


what is MT車機


how to know current status
[Nexus 後台 – Figma](https://www.figma.com/design/vemT4ioLLPe8uU6Yj8bArh/%F0%9F%93%82-Nexus-%E5%BE%8C%E5%8F%B0?node-id=586-365&node-type=canvas&t=g54sF8La74V0iYrx-0)




---


- [ ] how to handle old order/rent display pppID issue
- [x] check VS is empty result
- [ ] VS remove cache MR
- [x] check cache is clear in qat (manual test)
- [x]  跟 hao 開一下卡
- [ ] cache at mobile side when switch user?



- [x] FAIL test/api/v2/users/utils/getScooterIdsByVehicleId.test.js (1393 MB heap size)
- [x] FAIL test/api/v2/users/utils/getVehicleIdsByScooterIds.test.js (1191 MB heap size)
- [x] FAIL test/api/v2/scooters.test.js (51.665 s, 1207 MB heap size)
- [x] FAIL test/api/v2/users/service/scooterNotify.test.js (46.769 s, 1583 MB heap size)
- [x] FAIL test/api/v2/scooters/service/userApp.test.js (42.121 s, 1051 MB heap size)
- [ ] FAIL test/api/v2/users/me/rents.test.js (664.864 s, 812 MB heap size)
- [ ] FAIL test/api/v2/users/service/rents/index.test.js (5.138 s, 1110 MB heap size)


===


FAIL test/api/v2/users/service/rents/index.test.js (5.364 s, 1049 MB heap size)




FAIL test/api/v2/users/me/rents.test.js (685.879 s, 823 MB heap size)
- [ ] 先讓他都過，再找那個噴掉的 case 調整一下


[259](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/349773#L259)


- [ ] debug for redTag ios白色框框 -> 開 internal IOS/BE
	-  選紅標
	-  改一般
	-  按掉，在回去同一台查價格
	- req 傳過來的是紅標(應該要是一般)





apollo
PATCH https://apollo-qat.wemoscooter.com/hermes/v2/users/201212
![[IMG-log_work (wemo)-20250810201314250.png]]
![[IMG-log_work (wemo)-20250810201315228.png]]





穩定出現的 error at qat
2024-12-09T02:58:43.926Z error: "{\n  tag: 'maps/checkAvailableParkingLots',\n  error: { message: 'coordinates must contain numbers' },\n  xRequestId: '80529080-b5d9-11ef-aa0e-8925bcc15 │
│     at /usr/src/app/api/v2/users/service/maps.ts:346:24                                                                                                                                   │
│     at Generator.next (<anonymous>)                                                                                                                                                       │
│     at fulfilled (/usr/src/app/api/v2/users/service/maps.js:24:58)                                                                                                                        │
│     at runMicrotasks (<anonymous>)                                                                                                                                                        │
│     at processTicksAndRejections (internal/process/task_queues.js:97:5)




- [ ] 不用付錢會跳 -> 應該要改為 info
2024-12-09T02:51:03.499Z error: "{\n  tag: 'v2/users/service/history/rents/getByIdV24',\n  msg: 'order for rent 2467190 not found, which should never happen',\n  orderId



 2024-12-09T02:59:26.251Z error: "{\n  tag: 'v7/rents/2467191/maintenances',\n  error: { message: 'NOT_FOUND' },\n  xRequestId: '98970720-b5d9-11ef-aa0e-8925bcc151b5'\n}"                 │
│ Error: NOT_FOUND



- [ ] ????
 2024-12-09T03:10:38.308Z error: "{\n  tag: '/v2/users/controller/userCategories/getDefault',\n  error: { message: 'NOT_FOUND' },\n  xRequestId: '2a3b0130-b5db-11ef-aa0e-8925bcc151b5'\n} │
│ Error: NOT_FOUND












- [ ] 如果 get scooters 拿不到價格，不要噴 500
- [ ] 改 name
- [ ] why pricing dev not work



- [ ] core
	- [ ] 想要有一個 planning before 星期一
		- 好處
			- 大家可以知道哪些卡，大家知道大家做什麼，知識不會孤島
			- 大家可以更早先想要皆那個, 或是私底下討論，不是星期一馬上就被減走
			- 可以更早 identify 有沒有漏掉的，沒有開不好的 task, 甚至大家可以好好估分數
	- high stack work 一定要有 jack or josh 在一起, tech lead or mgm 一起
		- 盡量縮短了，已經半天就回報了, 但是如果 high stack, 可能就要隨時參與會更好
	- UI 還沒 complete 就開始，最後又說 UI 應該解決用戶體驗，code 要 make it work
		- 有關為何 UI 改畫面
		- UI 的畫面，沒有更細節的描述
			- BE 實作是依照當初 Josh 寫的設計架構去做
			- PM 也沒有詳細的文件
			- 所以 BE 是依照 Josh 的設計架構去做的
			- UI 上沒有寫的很細
			- 實作後，BE 需去問 UI 上要做到什麼程度，類似 歷史編輯log, 類似 excel 表上的 crud的限制等等
			- 這些東西 UI 上只有有畫面，沒有更細緻的描述
			- 簡單說就是 UI 不是出來, BE 才開始開發，是 BE 開始開發， UI 也一直還在調整
			- 然後 BE 開後到哪裡， UI 發現哪邊邏輯怪怪的，還是有點跟 BE 架構差很大，或是要達到 UI 目前的架構根本做不到的情況下
			- 就需要去重新跟 UI, PM, 討論
		- UI 的畫面 沒有跟目前 BE 提供的功能 in sync?


- [ ] why slow my perspective
	- [ ] 體感 still work hard
	- [ ] 2~3次的 interviww 有影響
	- [ ] 花了 JC 1.5天至少，Josh1.5天？
	- [ ]
			-  


- [ ] 繼續測試  


- [ ] 【2024 年度工作表現評核】 2024/12/27前完成繳交

kong demo video -> [https://drive.google.com/file/d/1cbrJnT7Itmev-AnUsVq_buCqcfUGDniI/view](https://drive.google.com/file/d/1cbrJnT7Itmev-AnUsVq_buCqcfUGDniI/view)

目前判斷是跟 redis cache 沒清理有關

random test fail:
test/pricing.e2e.spec.ts
test/pricingRule/pricingRule.service.spec.ts

reproduce script:
for i in {1..100}; do npm run test; done




for i in {1..100}; do npm run test test/pricing.e2e.spec.ts; done
for i in {1..100}; do npm run test test/pricingRule/pricingRule.service.spec.ts; done

test/pricingRule.e2e.spec.ts

![[IMG-log_work (wemo)-20250810201315341.png]]



![[IMG-log_work (wemo)-20250810201316276.png|662]]


否，以下頁面也有使用  
- 使用者 > 使用者列表 > 支付紀錄 > WeMo PASS > 更多資訊 > 訂閱紀錄 > 展開  
- 營運 > 車輛租用紀錄  
- 使用者 > 保安工單 > 細節  
- 信用積分 > 違規管理 > New/Edit Ticket > Rent ID > Search Rent  
- 使用者 > 使用者資料匯出 > 填 User ID > Rent Id > link


昨天那個很小的 MR  
[https://wemoscooter.atlassian.net/browse/WSF24-1505?atlOrigin=eyJpIjoiYjYwMGRjNjg0MTRlNDQ5MGI3NTE0OTViZDY2MTU5MzQiLCJwIjoiaiJ9](https://wemoscooter.atlassian.net/browse/WSF24-1505?atlOrigin=eyJpIjoiYjYwMGRjNjg0MTRlNDQ5MGI3NTE0OTViZDY2MTU5MzQiLCJwIjoiaiJ9)  
  
有空幫我看看 感謝

看 MR




- [x] 如果hao okay, 加入 internal card

- [ ] review this # How to Think Like an Architect - Mark Richards
- [ ] think about those 2 tech vid on architect
- [ ] finish u resting lc, at least 3

[[pricing plan and pricing group 目前是對應到什麼]]


### backup
```js fold

 


使用者租賃
https://apollo-qat.wemoscooter.com/hermes/v17/rents?limit=10&offset=0&userIds[]=201212&expandSelect=pricingPlanPrice.startPrice,pricingPlanPrice.freeMinutes,pricingPlanPrice.pricePerMinute,pricingPlan.i18nName,pricingGroup.i18nName,timePlan.i18nName,timePlan.freeMinutes,timePlan.type,timePlanDetailHistory.usedFreeMinutes,rebateVoucherTemplate.title,rebateVoucherHistory.usedAmount,insurance&photos=true&lang=zh-TW

訂單列表
https://apollo-qat.wemoscooter.com/hermes/v17/rents?expandSelect=pricingPlanPrice.startPrice,pricingPlanPrice.freeMinutes,pricingPlanPrice.pricePerMinute,pricingPlan.i18nName,pricingGroup.i18nName,timePlan.i18nName,timePlan.freeMinutes,timePlan.type,timePlanDetailHistory.usedFreeMinutes,timePlanDetail.useBeginAt,timePlanDetail.useEndAt,rebateVoucherTemplate.title,rebateVoucherHistory.usedAmount,insurance&userIds=201212&limit=10&offset=0&rentIsNull=false&returnIsNull=false&orderIsNull=false&lang=zh-TW


強制還車
https://apollo-qat.wemoscooter.com/hermes/v17/rents?limit=40&offset=0&expandSelect=pricingPlanPrice.startPrice,pricingPlanPrice.freeMinutes,pricingPlanPrice.pricePerMinute,pricingPlan.i18nName,pricingGroup.i18nName,timePlan.i18nName,timePlan.freeMinutes,timePlan.type,timePlanDetailHistory.usedFreeMinutes,timePlanDetail.useBeginAt,timePlanDetail.useEndAt,rebateVoucherTemplate.title,rebateVoucherHistory.usedAmount,insurance&userIds=201212&lang=zh-TW



```





roleId + pricingId  + queryTime -> get 


可以不停機調整？
- [ ]  1/3(五) 下午 堯睿 meeting 
如果你買公司股票公司會補助你 15%，買的上限為薪水的 20%，每個月固定扣款?

---







retro
- 謝謝 clarance and Josh 一來既往的幫忙，有這個 BE team 好幸福


只要查到過去的時間，還是可以查到
- [ ] deletAt 要確認

2aoxjgju!!netApp

- [ ] to clarance => pricing calculate need to merge, since you modify the interface, and the interface I use it to verify the when we got db data back
- [ ] **綁車頁上進行編輯** 也要納入?


跟你 double check, 上星期五我們提到的 "綁車頁上需要進行編輯的 API", 之後應該沒有後續討論了嗎? 那應該就是要加入這次 sprint

[[wm-1078-core pricing migration]]



Asana 考古帳號  
asana_group@wemoscooter.com  
VGFD3GB9kYt@





---

[VS release plan 2024 Oct](https://docs.google.com/document/d/1BftLg6u20B3Q_ponWPsahDmXsdk0TlmK_SUFcM0JLI8/edit?tab=t.0)
[vs secret](https://athena.wemoscooter.com/sre/infra/argocd-kubernetes-manifest/-/blob/master/helm/projects/vehicle-service/prod-values.yaml)


add pricing 做完要推到 qat, 兩個 branch, 和 related 的 proto 都要對到



[Jira Jean 使用方法](https://docs.google.com/presentation/d/1I9zNcYcI2AMFDk3416F16XaE_a_UeuhKmL_GMF5ICq4/edit#slide=id.g30c47b34605_0_6)


- 討論 jo, jack and clarsan, core
- 今天會先和 clarance 先就處理 VS release 的準備工作，然後再去 core 開發



方法
- use nodeJ




- [ ] todo
	- [ ] test
	- [ ] 思考 code 有哪些議題要討論




- [ ] 星期三議題 log
	- grpc service side
	- 如果可以做出來 -> 那 微服務就可以使用 grpc  auto gen 的 type, 這部份也不需要另外去測試
	- 那有些基本的檢查就不需要 class validator or zod, 有機會可以省下更多的 code


資料移轉
- use node.js to get all scooterID, only serveral K -> save into mem -> create those record in DB
- 


建立車牌
改車盤


clarance and Josh 討論要做的 core 卡




![[IMG-log_work (wemo)-20250810201316386.png]]







- [x] discus
Jack idea
- 為何要 any
- 因為他要站在不認識 VS 的角度去看, 因此本來就可以丟 type 限制的東西
- 另外就是，如果你不這樣處理，你就無法側
	- 你寫了就要側，不然你為何要寫
- 如果未來我們 server 的 interface 也是由 grpc autogen, 那我們可以考慮不需要加上 validator
	- 或是彈性使用


兩個優化的卡
[\[WSF24-621\] - Jira](https://wemoscooter.atlassian.net/browse/WSF24-621)
[\[WSF24-622\] - Jira](https://wemoscooter.atlassian.net/browse/WSF24-622)

- [ ] 10/18週五11:00  -> 還沒請假 -> 還沒決定日期


- [ ] check jack MR  [Sign in · GitLab](https://athena.wemoscooter.com/wemo/nexus-backend/-/merge_requests/81)



我在微服務A有 enumABC

我在另外一個微服務需要用到這個 enum的值
請問best practice 會希望我在另一個微服務也定義這個 enum a87



I have interface ABC at microserviceA
but I need to use this ABC in microserviceB
shall I create another same interface in microserviceB? but this violate the concept of 'single source of trueth'
what is the best practice for this scenario?




---




BE - [Core Pricing][ps] - Defined configuration structure for scooter(四輪和未來擴充考慮)
BE - [Core][Nexus](Hermes BE web api) 綁車
BE - [Core][Nexus](Nexus BE web api) 綁車


BE - [Core][Nexus](Nexus BE web api) get userRoles and get userRoles/:id
BE - [Core][Nexus](Hermes BE web api) get userRoles and get userRoles/:id
BE - [Core][hermes] - (Hermes BE web api) 取價格
BE - [Core][hermes] - (Nexus BE web api) 取價格



BE - [Core Pricing][hermes] - 調整 GET /v24/users/me/renting
- 這個 API 也會回 pricing plan 相關的資料
- 因此也需要改為使用 core module 來的資料
- 另外也需要進行 query cache 的處理
- (有跟 App 端雙方確認過，這個 API 呼叫時機會在 **預約後**，因此會從 rentConfig 拿資料)
```ts fold

GET /v24/users/me/renting HTTP/1.1

{
  "result" : "success",
  "data" : {
    "rentStatus" : 1,
    "scooterId" : "AEA-3008",
    "reservationTimeLeft" : "0",
    "timePlanId" : null,
    "pricingPlanId" : "571",
    "rentId" : "2464901",
    "scooter" : {
      "id" : "AEA-3008",
      "lat" : 25.0524057,
      "lng" : 121.5461422,
      "parkingLot" : null,
      "parkingSpaceId" : null,
      "styles" : {
        "pin" : "Skyblue"
      },
      "styleId" : "73",
      "pricing" : {
        "pricingPlans" : [
          {
            "id" : "571",
            "startPrice" : 18,
            "freeMinutes" : 6,
            "pricePerMinute" : 3,
            "name" : "一般用戶",
            "isDefault" : true,
            "isBasic" : true
          }
        ],
        "timePlans" : [ ],
        "rebateVoucher" : {
          "enable" : true
        }
      },
      "availableDistance" : 45,
      "batPercent" : 100,
      "groupId" : "0",
      "lastReturnPhotoUrl" : "https://storage.googleapis.com/wemo-backend-qat/rents/2464665/files.jpeg?GoogleAccessId=hermes%40backend-qat.iam.gserviceaccount.com&Expires=1726020998&Signature=iPfZWbRHidKNLr51qQhha8M19lW8Fr4J73XsSdd3JsN8J3Ri7%2BbDvPjvFIEruAh%2FtAemrFRYmkQvCs%2F62EGcodtWJOJsmsFcYMWHDEuYmHb10VTjNI2NueC7nvHgkzyiaSkZyHUEY7NSa%2FM6Sx%2Bro3cDWZxn13ZzcOGf0UOLK2GdDVXZxITyS2GKwInHYbAp2tjzLGfQ%2Btu26RWtQbjjo8KGhwLuUu27%2BvNpCado0bGaq403wkGcJK1JuTH4H%2FhpDgPX2aDfagTmIEfIxC8b57Jsm8%2FHIaM59aDVcQ8zEZ2qL5sIyqgFNg4m%2BOMsK2Fyghyd%2BNt6SVQihE2NQ9zK%2FQ%3D%3D",
      "userBatSwap" : false,
      "alteredBatPercent" : 120,
      "alteredDistance" : 54,
      "regionalOperatorId" : "1"
    },
    "availableParkingLotIds" : [
      "1",
      "3",
      "4",
      "5",
      "6",
      "7",
      "9",
      "14",
      "41",
      "45",
      "46",
      "47",
      "51",
      "52",
      "65",
      "66",
      "70",
      "72",
      "76",
      "99"
    ],
    "availableZonesIds" : [
      2,
      3,
      777,
      166,
      649
    ]
  }
}
```



- [[design doc on Lock quotes time in redis]]
- [[core_module get pricing rule id]]
- [[core-pricing For Web API documents]]



---

- [x] 這部電影〈峰迴路轉〉??
- [x] 電子書 汪培珽生了一個夢幻幼兒園

- [[修改 縱貫線停車場記錄]]

==

- [[update proto to specific data type for each algo]]
- [[learn mathy]]
- [[query slow for 排班表]]

[[pricing role 討論]]

討論放假

```md fold node
- what I want
- 放假下午
- 工作希望在高雄，除非沒人再高徐
- at Taiana 想要放假狀態
- 不要狂放，但是數量可以討論
```

- [x] test algo validate
- [x] Plant to instance need to use on...

  - [x] on validate?
  - [x] on return?

- ben and clarance 先寫 core
- jack and water 比較熟 -> 先搞 ops

2024_08_04-20_25_18

- [ ] 確認哪幾天需要請假

1 CS couse for 14 week course
one week for 2-3 week actual to finish
5 key course

2024_08_03-09_16_13

- [x] 想一下有沒有更好的 unify type
- [x] 星期一 ask Josh

  - [x] new way

- possible to do - grpc auto gen - server side?
- dto decicion call

2024_08_02-08_09_37

- [ ] check josh MR

- [x] 為何要包的那麼複雜出去?
  - box svc 就沒有這樣做
  - what is DTO -> data transfer object
  - why need DTO?
    - 資料從外面進來 -> 我們會需要一個地方去確保資料結構應該長什麼樣就是什麼樣
    - 也可以順便 validate
    - 讓內部 資料結構和外部資料結構 decouple
  - how DTO is bad
    - 很多層嵌套 -> a nested complicated code to maintain
      - 本來就已經 3d + pricing + 2 層 arr, naming 很冗
  - why I want to propose not using DTO
    - complicated data strcuture -> not flexible, more code to maintain
      - 有點為了要變成 class 而變成 class, 就是硬要結構用 class 的方式呈現，讓多層嵌套的結構會變得很複雜去處理
    -

why we need DTO?

- [x] change to union type for ori jsonBase
- [x] Dto study

2024_08_01-17_48_33
log

2024_07_31-08_09_50

- [ ] Wemo pass got different param?
- [ ] Zod?

- [x] 會開強更? => no

  - [ ] new 結構 才會考慮
  - 不過建議寫兩個 api
    - 會更新的 user 會用新的 api ver -> 開新的 api 處理
    - 不會更新的人 user 用舊的 api ver
    - 讓時間去讓不更新的人剩下 like 10%, 這時候再看要不要強更
    - 因為強更體驗不好
  - [ ] 慢慢過渡到第二個

- [x] need a meeting to App team
- [x] 多語系 -> apollo need

retro

- 開發 core 有很多 設計決策
- 一般我們比較不夠厲害的會請教 sr
- 但是這往赴確認其實會花很多時間，思考設計
- 往往估算的分數，類似 2 分，但其實整個討論＋設計，變得會很久
- 當初 estimate 2 分是實作, 沒有考慮到設計
- 結果就是
  - 會發現 2 分為何花了 3-4 天以上，sprint 完成就會降低
  - 做的當下也會有點擔心說，要花時間想清楚，還是快點弄一弄 (不過目前跟團隊互動下的共識，還是會偏向比較架構性的，底層的，影響深遠的設計，都會經過 sr 的確認，因此還是回到地一點)

2024_07_30-10_09_13

- [x] how to prevent forget credit card?

retro

- Josh init and bring us to finlize a grpc auto-gen, this is very helpful for our future grpc development.

2024_07_29-10_59_32

看一下 mr of hermes

車上有 tag -> priceID

-> 去拿到很多 pricingConfig
-> 最後透過 role 等規則 -> 拿到一個

2024_07_26-15_55_32

- [x] if I can use cleancen one?
  - [ ] if his get config get the latest one?

2024_07_25-10_11_22

wana check this MR
[Sign in · GitLab](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5206/diffs#9cb18f643e644b8c8f4b9d8183c0d55936d4b6da)

- [ ] check OPS blocked ticket
- [x] ask 意見
- [x] if no algo 2 setup, overload is not working

- [x] 開 4 wheel and then add some comment
- [ ] see what is the natural next

2024_07_24-10_05_41

-> not use algo helper
-> use geneic
-> inject and use

- 這裡我們使用 overload 來確保 algorithm 和 config 需要配對正確，另外全部的 algorithm 都可以在這邊集中管理

  - 要把 config 放在 method or constructor, 可以討論
  - 這邊先試著放在 method, 是想要讓 constructor 的職責單純化

- 另一種作法是，我們不需要這個 AlgoHelper 集中管理

  - 直接在 client code 去 分別 注入 algorithm
  - 然後直接在 client code 此用 注入 algorithm 的 loadConfig 方法 使用 config
  - 使用上彈性更好，不需要 AlgoHelper，但是我們無法有任何形式的 type 檢查 去確認 algorithm 和 config 的配對

-

2024_07_22-10_01_45

conti work on pricing config MR

2024_07_19-10_09_40

- [x] ask josh help check, 4:00

- [ ] see mr
  - [ ] clarance
  - [ ] jack

2024_07_17-13_17_33

- [x] angelia will provide me the old 保安, shall be 4 -> mgmt

==

先不要管 type, 先把用法定義出來
see if josh idea work or not
draft out the josh favor strcuture

2024_07_16-09_56_11

how to covert this dayType to dayType2

```ts

enum dayType {
  weekday = "weekday",
  holiday = "holiday",
  regularDayOff = "regularDayOff",
  makeUpWorkday = "makeUpWorkday",
  bridgeDay = "bridgeDay",
}



```

```ts

type dayType2 =
  | "weekday"
  | "holiday"
  | "regularDayOff"
  | "makeUpWorkday"
  | "bridgeDay";

```

find ts string enum magic

Questions

- flat or not flat for 3 dim (dayType X period X step)

  - flat

    - easier to read, edit to json
    - not hard to access via ts type
      ![[IMG-log_work (wemo)-20250810201317267.png]]

  - not flat / nested
    - easier to access e.g. priceConfig.reserve.weekday.peakTime
    -

- type proctect level
  - if we all list out(even no value). we will have 4(step)_ 5(5 day type) _ 4(4 period type) -> 80 個-> too much, hard to read
  - so I want to just use object and the user need to add type by himself to have code protected
- code 的部份?

- how to handle tp
  - 決定：no need to pass tp ratePerKm, will just use ratePerKm in pricing config
    - tp 這部份的 ratePerkm will , in the future, is pricing svc's resposibility
- where can I put the type or interface in pricing svc?
- define the type

如果定死，比較安全，如果是動態的，比較不安全
ref eventConfig

- review the strcuture by other
- update code

2024_07_15-10_06_50

check doc pricing on algo1

---

apollo 確認功能有出來

需求：Dispatch-是否開通 VAN 下車串接建立維修工單 - Y
搞懂 sql

---

what is 241?

UPDATE
"mnemosyne-qat"."Config"
SET
value = ' { "dispatchBiker": [...異動當下原資料, 241],
"dispatchCollect": [...異動當下原資料, 241],
"dispatchDeliver": [...異動當下原資料, 241] } ' :: jsonb :: jsonb
WHERE
"key" = ' officeLocationTypes ',
