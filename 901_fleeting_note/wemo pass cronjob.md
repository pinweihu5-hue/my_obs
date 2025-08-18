[[wemo_pass_note]]

---

VIP 續訂排程 (每天 22:00 點執行)	
New Job: VIPSubscriptionRenewJob   

VIP 即將到期通知排程 (每天 22:00 執行)	
New Job: VIPExpireNotificationJob 

VIP 每月執行排程 (每天 : 執行)	
New Job: VIPMonthlyJob 

FCM 發送到期通知	
Modify Job: reminder  

從 Data Team 取得推薦方案	
Modify Job: syncDataTeamRecommendations 


---

### New Job: VIPSubscriptionRenewJob VIP 續訂排程 (每天 22:00 點執行)
- 取出有開啟自動續訂且自動續約時間符合排程執行日期的資料 (next_charge_at = expired_at - 2day 20:00)
- 檢查使用者當下是否為有效 VIP 用戶 ( !vip -> skip, set log)
- 以上條件符合則進行續訂付款
- 建立訂閱紀錄，創立訂單
- 依照使用者指定付款方式自動付款
	- 新增一筆 user_vip_detail
	- user_vip_subscription 更新付款時間 payAt
	- 付款成功： 
		- user_vip_detail -> 更新 successPurchasedAt
		- (user_vip_subscription) 更新下次續訂時間 nextChargeAt
		- (user_vip_history) 寫入 續訂成功 History
		- 通知 「續訂扣款成功」@ Appier 
	- 付款失敗：
		- user_vip_detail 不會更新 successPurchasedAt
		- (user_vip_history) 寫入 續訂失敗 History
		- (user_vip_subscription) 更新
		- 通知使用者續訂失敗，需要自行續約
			- (user_vip_subscription.enable set false) 取消使用者自動續訂
			- 通知 「續訂扣款失敗」@ Appier
			

### New Job: VIPExpireNotificationJob VIP 即將到期通知排程 (每天 22:00 執行)
- 取出到期日期剩餘 3 天的使用者
- 無續訂者通知 「方案到期通知」
- 有續訂者通知 「自動續訂繳款提醒」
    

  
### New Job: VIPMonthlyJob VIP 每月執行排程 (每天 24:00 執行)
- 產生補上缺少的 Statistic 同時發放 VIP 券
	- 取出隔天到期使用者，且還沒有建立統計紀錄，且有剩餘月份的 VIP 用戶
	- 建立每月統計紀錄 (user_vip_statistic)
	- 扣除訂閱記錄上的剩餘月份 (user_vip_detail)
	- 發送每月 3 張時間騎乘券
	- 扣除使用者 VIP (user_vip) 剩餘月份，同時更新目前訂閱紀錄ID (details_id)