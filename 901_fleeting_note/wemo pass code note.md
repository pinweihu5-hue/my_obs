[[wemo_pass_note]]


---
# VIP 購買前狀態檢查
```ts

export const _checkUserPurchasableOrThrowError = async (userId: string, currentTime: Date) => {
  // 看是否有統計資料, 如果沒有 -> CHECK_FOR_VIP_PURCHASE, 如果有 -> CHECK_FOR_VIP_RENEW
  const checkPurchasableMode = R.isNil(await getOrCreateUserCurrentStatistic(userId)) ? CHECK_FOR_VIP_PURCHASE : CHECK_FOR_VIP_RENEW
  // 如果 CHECK_FOR_VIP_RENEW 就無法買
  const purchaseCheckResult = await isUserAllowPurchaseVip(userId, checkPurchasableMode)
  if (!purchaseCheckResult.allow) {
    _throwErrorFromPurchaseCheckerResult(purchaseCheckResult.blockReason)
  }

  // 如果有 subscriptionStatus 就要在 payAgain or renewable 的狀態下才可以購買
  const subscriptionStatus = await getUserVipSubscriptionByUserId(userId)
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

  // 確認 nextActiveVipPlan 是否存在
  const nextStartOfDay = getNextStartOfDay(currentTime, 1).toDate()
  const nextActiveVipPlan = await db.userVipDetail.getUserVipDetailAtTime(userId, nextStartOfDay)
  if (!R.isNil(nextActiveVipPlan)) {
    throw new Error(CustomErrorV2.VIP_CONFLICT_VIP_PLAN_EXIST.type)
  }
}

```




