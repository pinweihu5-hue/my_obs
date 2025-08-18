[[wemo_pass_note]]


---

(fn : isUserAllowPurchaseVip at api/v2/users/utils/vip.ts)

1. 使用者必須為 ACTIVE
2. 使用者當下只能有一般身份
3. 使用者要是可騎乘狀態 finish allow
4. 沒有月租方案
5. 沒有 MeN GO 方案
6. 沒有 activityCode (企業方案)
7. 使用者不是在騎乘中

反面論述：
只要有以下任一條件都不可以購買 WeMo Pass 
1. 身上有非一般用戶群組 
2. 不是 Active 狀態 
3. 機車運具不能使用 
4. 身上有月租方案 
5. 身上有 MenGo 方案 
6. 身上有活動邀請碼 
7. 用戶騎乘機車中

目前只支援 creditCard and wallet
if (![PaymentType.creditCard, PaymentType.wallet].includes(paymentType))