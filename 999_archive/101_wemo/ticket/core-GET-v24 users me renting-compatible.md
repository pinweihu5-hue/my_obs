related:
- [[design doc on Lock quotes time in redis]]
- [[core module get pricing]]

Need to ask APP side
有關  app 打 users/me/renting API 時
回應資料大概如下

```js fold

{
  "result" : "success",
  "data" : {
	  // skip many fields..
      "styleId" : "73",
      "pricing" : {            
        "pricingPlans" : [  
          {
            "id" : "571",
            "startPrice" : 18,
            "freeMinutes" : 6,
            "pricePerMinute" : 3,
            "name" : "一般用戶",
            "isDefault" : true, <---
            "isBasic" : true
          }
        ],
        "timePlans" : [ ],
        "rebateVoucher" : {
          "enable" : true
        }
	 // skip many fields
```
app 是不是預期目前正在使用的 pricingPlanPrice價格方案的 isDefault為 true?

PS: 正在使用的意思是說， 之前預約和租借時記入後端資料庫的id
PS:  因為目前 BE 有一段 有年紀的code就是在做這個事情 (會去資料庫拿目前的  pricingPlanPriceId, 把這個 id 下的 isDefault設定為 true, 然後如果找不到，會設定第一個方案(sort by id)的 isDefault為 true)


==



BE 後端這邊有一段 code，這段 code 會把 db 拿到的 pricingPlanPrice 設定為 

/**
* 車輛在預約中時，如 app 打 users/me/renting API 時，預期 scooter.pricing.pricingPlans 中一定會有一筆 pricingPlanPrice.isDefault = true 的資料
* isDefault = true 是為了在畫面上顯示選擇了哪一種方案費率
* 目前 (2021-02-05) ，如果在預約中 scooter.pricingGroup 被改變，會沒有 isDefault = true 的資料，導致 app 不能租車。故先排序，將第一筆設成 true 讓 app 得以顯示
* 把拿到的 ppp 設定為 true
* 然後把 true放前面， 如果沒有 true 的話，就用 id 排序
*



---





### 邏輯盤點

contorller
`table.addAuthEndpoint(16, { method: 'get', endpoint: '/me/renting', controller: rentsController.getRentingV16 })`


use old ver contoller: wemoRouter
- `const tag = '/v16/users/renting'`


service level
api/v2/users/service/rents/renting.ts:96

`export async function getRenting(user: User, locale: lang): Promise<RentingVO> {`


這個 API 也會回 pricing plan 相關的資料
因此也需要改為使用 core module 來的資料
另外也需要進行 query cache 的處理
(有跟 App 端雙方確認過，這個 API 呼叫時機會在 預約後，因此會從 DB rentConfig 拿資料)



### isDefault

結論，都沒用到，可以寫死為 false


![[IMG-core-GET-v24 users me renting-compatible-20250810201310948.png]]
![[IMG-core-GET-v24 users me renting-compatible-20250810201312959.png]]


### API shape
```ts fold

GET /v24/users/me/renting HTTP/1.1

{
  "result" : "success",
  "data" : {
    "rentStatus" : 1,
    "scooterId" : "AEA-3008",
    "reservationTimeLeft" : "0",
    "timePlanId" : null,
    "pricingPlanId" : "571",  // 需要調整為 pricingRuleId
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
      "pricing" : {            // pricing 需要調整
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

