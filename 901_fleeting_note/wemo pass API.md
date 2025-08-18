
[[wemo_pass_note]]

---



須修改/新增 Middleware	
api/v/utils/middlewares/authentications/user.js 使用者認證	


# user app
GET users/me/vip/achievements 成就獎勵

POST /users/me/activities 活動序號兌換	


POST /users/me/promotions 促銷序號兌換	
POST /users/me/promotions/check 促銷序號兌換檢查	


GET /users/me/status - 取得用戶者狀態	
GET /users/me/vip/status - 取得使用者 VIP 狀態	
GET /users/me/userCategories/list 顯示使用者方案與身份	
POST /users/me/status/finishRegister 完成註冊 Api	


GET /vip/plans 取得VIP 計畫資料	
GET /users/me/purchasableTimePlans 取得可訂閱的時間方案	
POST /users/me/vip/purchase/:vipPlanId      使用者 VIP 開始或續訂方案
PATCH /users/me/vip/subscriptions 更新使用者續訂參數
PATCH /users/me/subscriptions 顯示使用者訂閱方案	
GET /users/me/recommendedSubscriptions 取得是否有建議訂閱方案	



## time plan
GET /users/me/timePlans/:id 取得可訂閱的時間方案內容	
POST /users/me/timePlans 訂閱時間方案	
POST /timePlans/details/:timePlanDetailId/paidNotify 啟用使用者時間方案	



## rent related
POST /users/me/rents 預約車輛(reserve)	
PATCH /users/me/rents/:rentId 還車	
GET /users/me/history/rents 取得騎乘紀錄	
GET /users/me/history/rents/:rendId 取得騎乘紀錄	


## order
GET /users/me/orders 取得訂單列表	
GET /users/me/order/:orderId 取得訂單細節	


POST /vip/details/:userVipDetailId/paidNotify 啟用使用者訂閱 VIP 方案	

## Ceres APIs 
POST /orders 建立訂單	
	

# Apollo
GET /users/:userId/vip/status 查詢使用者 VIP 狀態
GET /users/:userId/vip/details 查詢使用者 VIP 購買記錄與使用狀態	
GET /users/:userId/vip/details/:userVipDetailId 查詢使用者 VIP 購買記錄與使用狀態詳細資料	
GET /users/:userId/vip/cancel 取得可取消訂單資料	
DELETE /users/:userId/vip/subscriptions 關閉使用者自動續訂	
DELETE users/:userId(\\d+)/vip/details/:userVipDetailId(\\d+)   取消使用者vip方案
DELETE /users/:userId/vip 取消 VIP 訂單






---

# PATCH /users/me/vip/subscriptions 更新使用者續訂參數


if 取消續訂
payload is
{"enable":false}

res
{
  "result": "success",
  "data": {
    "subscription": {
      "enable": false,
      "allowAction": "changeSetting",
      "nextChargeAt": "2024-06-26T12:00:00.000Z",
      "continueVipPlanId": "1",
      "paymentType": 1,
      "continueVipPlanTitle": "WeMo PASS 月方案"
    }
  }
}




# DELETE users/:userId(\\d+)/vip/details/:userVipDetailId(\\d+) 
- update vip detail table
	- canceledAt
	- cancelManager
	- cancelReason
	- refund(mgmt input)
- update userVipSubscription
	- nextChargeAt to now
	- enable to false
- if lastVipUserStatisticId exist  (有統計紀錄，將到期時間設定為當下)
	- update static table
		- endAt to now
	- 有發放騎乘券，需要取消未使用且未刪除的券
	- get activeVoucher and call `db.rebateVoucher.deleteVoucher`






# POST endpoint: '/me/vip/purchase/:vipPlanId',

- purchaseVipPlan
	- validate part
		- checkUserPurchasableOrThrowError
		- getVipPlanDataOrThrowError
		- validate vip_discount_input_data
	- create and update data
		- calculatePurchasePrice via discountMode
		- create vip detail
		- create charing detail
		- create order
		- set order id in vip detail
		- upsert vip subscription
- 設定VIP訂單已付款 setUserVipDetailPaid  (this also get called by [[wemo pass paidNotify]])  
	- validate
		- 如果沒有 VIP 明細，就跳錯
		- 如果 order id 不一致，就跳錯
		- 如果 successPurchasedAt 已經押上，表示已經付款，return
		- if discount exist and discount successPurchasedAt exist, then cancel order -> 已經發過
		- 如果結帳完成前有發生拒絕條件，則自動取消訂單退款(use isUserAllowPurchaseVip to check)
	- update data
		- 更新 detail, discount and subscription 欄位，like successPurchasedAt, nextChargeAt..等
	- 建立統計資料與發 VIP rebate voucher
	- 發送推播通知購買成功



