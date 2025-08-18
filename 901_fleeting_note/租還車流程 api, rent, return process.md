[[wemo_note]]


also can see: https://docs.google.com/document/d/1IsX07ZfGt27fo9pARdKGoC3H5e_dG-y1N9GGYyS6nEQ/edit

```json


// 畫面滑動會呼叫的
GET /v21/parkingLots?lang=zh-tw&lat=25.05547388996712&lng=121.5482349693775 HTTP/1.1
GET /v21/scooters?lat=25.05547388996712&lng=121.5482349693775 HTTP/1.1



// 1. 點選那台車
GET /v21/scooters/AEA-3015?lang=zh-tw HTTP/1.1
response
{
  "result": "success",
  "data": {
    "id": "AEA-3015",
    "batPercent": 100,
    "groupId": "0",
    "lat": 25.055474,
    "lng": 121.548235,
    "styleId": "73",
    "styles": { "pin": "Skyblue" },
    "images": {
      "covers": [
        "https://storage.googleapis.com/wemo-backend/scooterInAppImages/1-Normal/covers/01.png"
      ]
    },
    "availableDistance": 45, // use battery percenage 套公式算的預估里程   (45 * parsedBatPercent) / 100
    "pricing": {
      "pricingPlans": [
        {
          "id": "540",
          "startPrice": 18,
          "freeMinutes": 6,
          "pricePerMinute": 3,
          "name": "一般用戶",
          "isDefault": true,
          "isBasic": true
        }
      ],
      "timePlans": [],
      "rebateVoucher": { "enable": false }
    },
    "parkingSpaceId": null,
    "parkingLot": null,
    "alteredBatPercent": 120,
    "alteredDistance": 54
  }
}


// 2. 預約 post 
POST /v21/users/me/rents HTTP/1.1
{"pricingPlanId":"540","scooterId":"AEA-3015"}

response
{"result":"success","data":{"scooterId":"AEA-3015","rentId":"2460160"}}

code:
endpoint: '/me/rents'




// 還會打其他 拿資訊的 api
GET /v22/appConfig/news
GET /v22/users/me/status
GET /v22/users/me/renting/currentStatus
GET /v22/insurance/description
GET /v22/users/me/switchSetting
GET /v22/users/me/renting
GET /v22/users/me/payments/currentStatus
GET /v22/insurance/description
GET /v22/users/me/rebateVouchers/active$
GET /v21/parkingLots?lang=zh-tw&lat=25.05547388996712&lng=121.5482349693775 HTTP/1.1
GET /v21/scooters?lat=25.05547388996712&lng=121.5482349693775 HTTP/1.1




// 2. patch type 3 開始租用
PATCH /v21/users/me/rents/2460160
{"pricingPlanId":"540","type":3,"timePlanId":"null","rebateVoucherId":"null"}  // type: 3 租用中

code: 
const data = await _rent({ rentId, pricingPlanId, timePlanId, rebateVoucherId, userPO, lang, now })



response
{"result":"success","data":{"scooterId":"AEA-3015"}}



// 3. 打 KeyPage 拿 keypage 畫面資料，這邊有時候連續打 retry cause?
POST /service.asmx/KeyPageInfo
response
{
    "result": "success",
    "time": "0",
    "bat_percent": "100",
    "power": "0",
    "lat": "25.055474",
    "lng": "121.548235",
    "scooter_group_id": "0",
    "distance_remaining": 45,
    "ride_distance": "0",
    "altered_distance": "54",
    "altered_bat_percent": "120",
    "zones": [
      [
        [25.032358, 121.569987],
        [25.032398, 121.572674],
        // ...
      ]
    ]
}



// 4. 開機
POST /v21/users/me/rents/2460160/controls HTTP/1.1
{"action":"powerOn"}
res {"result":"success","data":{"result":"success"}}


// 5. 開車箱, 通常會被打好幾次
POST /v21/users/me/rents/2460160/controls
{"action":"openTrunk"}
res {"result":"success","data":{"result":"success"}}


// 中間騎騎騎，會呼叫其他 api,like
POST /service.asmx/KeyPageInfo
GET /v22/appConfig/news
GET /v22/users/me/status
GET /v22/users/me/switchSetting
GET /v22/users/me/payments/currentStatus
GET /v21/rents/:id(\d+)/latestNotification



// 6. 熄火
POST /v21/users/me/rents/2460159/controls
{"action":"powerOff"}
res {"result":"success","data":{"result":"success"}}


// 有時候(timing?)會確認 current zone
GET /v21/scooters/:id([A-Z0-9]+-[A-Z0-9]+|[A-Z0-9]+)/zones/current


// 7. 還車 API rents, type 4
PATCH /v21/users/me/rents/:rentId HTTP/1.1
req body
{
  "userEvents": [
    {
      "lng": 121.546180772491,
      "type": "RESERVE",
      "lat": 25.052419846971429,
      "eventAt": "2023-08-28T06:19:24.477Z"
    },
    {
      "type": "RENT",
      "eventAt": "2023-08-28T06:19:48.127Z",
      "lng": 121.54597455168755,
      "lat": 25.052479294076708
    },
    {
      "lat": 25.052476566181308,
      "type": "KEY_ON",
      "lng": 121.54596671171082,
      "eventAt": "2023-08-28T06:20:05.582Z"
    },
    {
      "lng": 121.54596671171082,
      "eventAt": "2023-08-28T06:20:13.826Z",
      "type": "OPEN_TRUNK",
      "lat": 25.052476566181308
    },
    {
      "lng": 121.54596671171082,
      "type": "KEY_OFF",
      "eventAt": "2023-08-28T06:20:36.473Z",
      "lat": 25.052476566181308
    },
    {
      "lng": 121.54603023122198,
      "lat": 25.052590353909945,
      "eventAt": "2023-08-28T06:20:43.721Z",
      "type": "RETURN"
    }
  ],
  "type": 4
}

response
{
    "result": "success",
    "data": {
      "scooterId": "ALL-5020",
      "rentDuration": "00:01:59",
      "totalRideMinutes": 2,
      "amount": 21,
      "orderAmount": 21,
      "rentId": "2460159",
      "rentAt": "2023-08-28T06:14:43.431Z",
      "returnAt": "2023-08-28T06:16:42.948Z",
      "pricingBeginUTCAt": "2023-08-28T06:14:43.431Z",
      "isRentTimeTooShort": false,
      "parkingLotId": null,
      "distances": "0",
      "promotionEvents": [],
      "luckyDrawWinnings": [],
      "orderId": "20230828141643680"
    }
  }



// 8. 拿付款資料 （進入付款頁面）
GET /v21/users/me/payments?accept-language=zh-tw&lang=zh-tw HTTP/1.1
res
{
  "result": "success",
  "data": {
    "paymentType": "creditCard",
    "wemoCreditBalance": 0,
    "walletBalance": 0,
    "invoice": { "type": 0, "content": "" },
    "bankTrustCardNo": "",
    "paymentErr": 0,
    "creditCard": {
      "type": "Visa",
      "last4Digits": "4242",
      "expirationDate": "202312"
    },
    "events": [],
    "unpaidOrders": []
  }
}


// 付款
POST /v23/users/me/trans HTTP/1.1

table.addAuthEndpoint(5, { method: 'post', endpoint: '/me/trans', controller: OrderController.transV5 })

req.payload
{
  "amount" : 0,
  "isWaiting" : true,
  "orderId" : "20240426121457746",
  "paymentType" : 1,
  "redirectUrl" : "https://wemoscooter.page.link/payment"
}

res
{
  "result" : "success",
  "data" : {
    "orderAmount" : 35,
    "unpaidAmount" : 0,
    "paidAmount" : 35,
    "payways" : [
      {
        "payway" : "creditCard",
        "amount" : 35
      }
    ]
  }
}



// 拿 user rent history
GET /v21/users/me/history/rents/2460160 HTTP/1.1
{
  "result": "success",
  "data": {
    "rentId": "2460160",
    "totalAmount": 0,
    "orderAmount": 0,
    "rentAt": "2023-08-28T06:19:47.695Z",
    "returnAt": "2023-08-28T06:20:43.869Z",
    "pricingBeginUTCAt": "2023-08-28T06:19:47.695Z",
    "scooterId": "AEA-3015",
    "rideDistance": 0,
    "totalRideMinutes": 1,
    "planDetails": [
      { "type": "pricingPlan", "name": "一般用戶", "minutes": 1, "amount": 0 }
    ],
    "payways": [],
    "hasCustomerReport": false,
    "insuranceAmount": 0,
    "luckyDrawWinnings": [],
    "userVipStatisticId": null
  }
}


```