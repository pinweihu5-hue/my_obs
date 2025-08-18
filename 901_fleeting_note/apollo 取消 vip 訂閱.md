[[wemo_pass_note]]



DELETE users/:id/vip/details/:id
payload: {"refund": 1, "comment":"qq333333"}



然後會拿目前 user details
GET users/:id/details
```tsx
{
    "result": "success",
    "data": [
        {
            "userId": "201212",
            "vipPlanId": "10",
            "price": 1,
            "orderId": "20230807085451576",
            "beginAt": "2023-08-07T00:54:52.769Z",
            "successPurchasedAt": "2023-08-07T00:54:52.769Z",
            "endAt": "2023-09-05T15:59:59.999Z",
            "canceledAt": "2023-08-07T00:56:13.434Z",
            "userVipDetailId": "533",
            "status": "canceled",
            "vipPlanTitle": "tt"
        }
    ],
    "meta": {
        "total": 1,
        "offset": 0,
        "limit": 10
    }
}
```


也確認狀態
GET users/:id/vip/status
```tsx
{"result":"success","data":{"isVipUser":false}}
```

![[IMG-apollo 取消 vip 訂閱-20241003104933820.png]]