[[wemo_pass_note]]

---

### GET /users/me/vip/status - 取得使用者 VIP 狀態

subscription.**allowAction** (string) 有以下四種狀態
- "renewing" 排程正在執行中，這段時間下(max 10 min)，使用者無法調整與操作 (第 28 天晚上 8 點)
- "changeSetting": 用戶可以調整續訂狀態, enable | disable
- "payAgain" 自動續訂扣款失敗使用者需要重新付款
- "renewable" 用戶取消續訂後的狀態，這時用戶可購買續訂


# state 改變
[Figma](https://www.figma.com/board/4MD4T4IegKTKPRN3RdJGE5/my?node-id=23-91&t=7PhA31ZLrXAm4duT-0)

![[IMG-wemo pass 續訂機制-20241003104934087.png|675]]


# 時序圖
- 預設購買後自動開啟續訂，使用者可以自行調整是否開啟 
- 自動續訂扣款為到期前 2 天的晚上 20:00 扣款 
- 到期通知 / 續訂扣款通知 會在續訂前一天通知
![[IMG-wemo pass 續訂機制-20241003104948944.png]]


![[IMG-wemo pass 續訂機制-20241003105011784.png]]


這邊用條件的方式來判定，因此不需要用 crobjob 去切（cronjob 會有時間差風險，類似 100 萬用戶, 最後一人更新時跑到已經是 10 點 10 分）

- 30 天為一個區間 (table defition, see [WeMo Pass 1.0 BE Planning](https://docs.google.com/document/d/1-oJ2WgM24T9gJoP9NEmjey12sQCxt5IiypA3K8pqV1s/edit#heading=h.fj6y4u4filvu)
- 27th day at 22:00
  - if 自動續訂 ebale
    - cronjob [VIPExpireNotificationJob](https://docs.google.com/document/d/1-oJ2WgM24T9gJoP9NEmjey12sQCxt5IiypA3K8pqV1s/edit#heading=h.4o9kvxgtfs0d), 通知即將自動扣款
  - if 自動續訂 disable
    - cronjob [VIPExpireNotificationJob](https://docs.google.com/document/d/1-oJ2WgM24T9gJoP9NEmjey12sQCxt5IiypA3K8pqV1s/edit#heading=h.4o9kvxgtfs0d) [即將到期通知排程](https://docs.google.com/document/d/1-oJ2WgM24T9gJoP9NEmjey12sQCxt5IiypA3K8pqV1s/edit#heading=h.4o9kvxgtfs0d)
- 28th day (也是第二個週期的第一天)
  - 自動續訂(扣款)
    - 如果扣款失敗，自動續訂就取消了
- 28~30 天
  - 如果自動扣款續訂失敗，這兩天可以用手動操作去續訂
- 30 天後
  - 一個週期結束


- 變數意義
  - enable, 是否開啟自動續訂
  - nextChargedAt, 下次付款時間
  - subscription.enable 為 true 時才有意義
  - lastPayAt  手動或自動的最後嘗試付款時間, 成功或失敗都會押上時間
  - 因此 nextChargedAt 應該大於 lastPayAt



- 如果反過來，表示續訂付款失敗
  - [ ] 我猜是因為要續訂成功才會押上 nextChargedAt, 因以續訂失敗， 就沒有下一期的 nextChargedAt, 只有失敗也會押上的 lastPayAt



# 狀態


- "renewing"
	- 排程正在執行中，這段時間下(max 10 min)，使用者無法調整與操作 (第 28 天晚上 8 點)
	- 跑續訂的 cronjob -> [Job: VIPSubscriptionRenewJob VIP 續訂排程 每天 22:00 點執行)](https://docs.google.com/document/d/1-oJ2WgM24T9gJoP9NEmjey12sQCxt5IiypA3K8pqV1s/edit#heading=h.j347zsfezznd)
	- enable: true
	- nextChargedAt < now
	- lastPayAt < nextchargeAt
- "changeSetting": 用戶可以調整續訂狀態, enable | disable
	- nextChargedAt > now
	- lastPayAt < nextchargeAt
- "payAgain"
	- 自動續訂扣款失敗使用者需要重新付款
	-  enable: true
	- nextChargedAt < now
	- lastPayAt >= nextchargeAt , 應該都是下次付款大於上次付款，因此如果這邊是反過來，表示付款失敗了
- "renewable"
	- 用戶取消續訂後的狀態，這時用戶可購買續訂
	- enable: false, 用戶沒有訂閱，自動續訂 is false
	- nextChargedAt > now
	- lastPayAt < nextchargeAt

![[IMG-wemo pass 續訂機制-20241003105037897.png]]


# code
getUserVipSubscriptionByUserId at api/v2/users/service/vip/getUserVipSubscription.ts
```ts

export const getUserVipSubscriptionByUserId = async (userId: string): Promise<userVipSubscriptionVO | undefined> => {
  const tag = `${Tag}/getUserVipSubscriptionByUserId`
  try {
    const userVipSubscription = await db.userVipSubscription.getByUserId(userId)
    if (R.isNil(userVipSubscription)) {
      return undefined
    }

    // 目前訂閱的上次付款時間(可以是自動續訂或是人工)
    const lastPayAt = moment(userVipSubscription.lastPayAt)
    // 目前訂閱的下次續訂自動付款的時間
    const nextChargeAt = moment(userVipSubscription.nextChargeAt)

    let allowAction: 'renewing' | 'payAgain' | 'changeSetting' | 'renewable'
    let enable = userVipSubscription.enable

    // 下次續訂自動付款時間大於 now -> 還沒到
    if (nextChargeAt > moment()) {
      allowAction = 'changeSetting'
      // 上次付款時間 大於等於 下次續訂自動付款時間 -> 已經付款失敗
      // 因此如果付款成功，會押上 nextChargeAt -> 就應該是 lastPayAt < nextChargeAt
    } else if (lastPayAt >= nextChargeAt) {
      //  (自動續訂/使用者續訂) 已經付款失敗
      allowAction = 'payAgain'
      enable = false
    } else if (enable) {
      // 續訂排程執行中 (nextChargeAt <= now and lastPayAt < nextChargeAt and enable is true)
      allowAction = 'renewing'
    } else {
      // 超過自動續訂時間，使用者須重新續訂 (nextChargeAt <= now and lastPayAt < nextChargeAt and enable is false)
      allowAction = 'renewable'
    }

    return {
      enable,
      allowAction,
      nextChargeAt: nextChargeAt.toDate(),
      continueVipPlanId: userVipSubscription.continueVipPlanId,
      paymentType: userVipSubscription.paymentType
    } as userVipSubscriptionVO
  } catch (error) {
    logger.error({ tag, error, userId })
    throw error
  }
}

```


# payAgain and renewable 

![[IMG-wemo pass 續訂機制-20241003105102766.png]]

這兩個算是可以購買的”合法狀態”
因此本來就不會拋錯
  

邏輯在此
```ts

export const _checkUserPurchasableOrThrowError = async (userId: string, currentTime: Date) => {
 const checkPurchasableMode = R.isNil(await getOrCreateUserCurrentStatistic(userId)) ? CHECK_FOR_VIP_PURCHASE : CHECK_FOR_VIP_RENEW
 const checkExistDetailTime = getNextBeginAtForVip(currentTime, 1).toDate()
 const [purchaseCheckResult, subscriptionStatus, nextActiveVipPlan] = await Promise.all([
   isUserAllowPurchaseVip(userId, checkPurchasableMode),
   getUserVipSubscriptionByUserId(userId),
   db.userVipDetail.getUserVipDetailAtTime(userId, checkExistDetailTime)
 ])

 if (!purchaseCheckResult.allow) {
   _throwErrorFromPurchaseCheckerResult(purchaseCheckResult.blockReason)
 }


 if (!R.isNil(subscriptionStatus)) {
   if (subscriptionStatus.allowAction === 'renewing') {
     throw new Error(CustomErrorV2.VIP_SUBSCRIPTION_IS_RUNNING.type)
   }

   if (subscriptionStatus.allowAction === 'changeSetting') {
     throw new Error(CustomErrorV2.VIP_SUBSCRIPTION_CHANGE_SETTING_ONLY.type)
   }

   if (!['payAgain', 'renewable'].includes(subscriptionStatus.allowAction)) {
     throw new Error(`Undefined allowAction value: ${subscriptionStatus.allowAction}`)
   }
 }
  

 if (!R.isNil(nextActiveVipPlan)) {
   throw new Error(CustomErrorV2.VIP_CONFLICT_VIP_PLAN_EXIST.type)
 }
}

```

  


# DB 手動調整為 payAgain
detail
![[IMG-wemo pass 續訂機制-20241003105123831.png]]


detail ID 對應的 統計表, beginAt and EndAt 調成一樣
![[IMG-wemo pass 續訂機制-20241003105123936.png]]

nextChargeAt 自動扣款的時間改為上面的 EndAt 減兩天 → 因為自動扣款本來就應該是 endAt -2 days, 上面改了 endAt, 因此這邊也要調
lastPayAt 改為 比 nextChargeAt 多一點點 → 狀態會是 payAgain
![[IMG-wemo pass 續訂機制-20241003105134268.png]]


打 api 確認
![[IMG-wemo pass 續訂機制-20241003105143079.png|500]]