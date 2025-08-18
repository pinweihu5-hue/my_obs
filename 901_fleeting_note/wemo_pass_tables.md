[[wemo_pass_note]]




| table name            | definition |
| --------------------- | ---------- |
| vip_plan              | 方案主表       |
| vip_plan_benefit      | 方案優惠內容     |
| vip_plan_pricing      | 方案售價       |
| user_vip_subscription | 使用者續訂設定與資訊 |
| user_vip_detail       | 使用者購買紀錄    |
| user_vip_statistic    | 使用者每月統計資料  |
| user_vip_recharging   | 部分退款重新收費紀錄 |

#### Create Table: vip_plan VIP 計畫資料
- Initial Record:
insert into vip_plan (id, title, "durationMonth") values (1, '{"en":"NT$99/month", "zh-tw":"NT$99/月"}', 1)


主表 vip_plan 每個vip_plan方案會對應到多個 VIP 計畫售價表
當下的售價會依照最後生效的 vip_plan_pricing 為售價

| name          | type                               | description             |
| ------------- | ---------------------------------- | ----------------------- |
| id            | unsigned integer, ai, pk, not null | 計畫ID                    |
| title         | i18n                               | 計畫名稱 (i18n object)      |
| durationMonth | integer                            | 方案週期 (紀錄幾個月，每月定義為 30 天) |
| createdAt     | datetime                           | 建立時間                    |
| updatedAt     | datetime                           | 更新時間                    |
| detededAt     | datetime, null                     | 刪除時間                    |

code
```ts fold
protected getVipPlanQuery(): SelectQueryBuilder<VipPlanPricing> {
    const nowTime = moment().toDate()

    /**
     * 找出每個方案目前最後生效的價錢，預期產出 SQL：
     * select
     *   distinct on (vpp."vipPlanId")
     *     "vpp"."vipPlanId" as id,
     *     "vpp"."price" as price,
     * 	   "vp"."title" as title,
     *     "vpp"."description" as description,
     *     "vpp"."originalPrice" as "originalPrice",
     *     "vp"."durationMonth" as "durationMonth"
     * from
     *   "vip_plan_pricing" "vpp"
     * left join
     *   "vip_plan" "vp" on "vp"."id" = "vpp"."vipPlanId"
     * where
     *   "vpp"."beginAt" <= NOW()
     *   and "vpp"."deletedAt" is null
     * order by
     *   "vpp"."vipPlanId" asc
     *   "vpp"."beginAt" desc
     *
     */
    return this.client
      .getRepository(VipPlanPricing)
      .createQueryBuilder('vpp')
      .leftJoin(VipPlan, 'vp', 'vp.id = vpp."vipPlanId"')  // 把主表和價格表 join 起來
      .select(
        'DISTINCT ON (vpp."vipPlanId") vpp."vipPlanId" as id, 
					vpp.price as price, vp.title as title, vpp.description as description, 
					vpp.originalPrice as "originalPrice", "vp"."durationMonth" as "durationMonth"'
      )
      .andWhere('vpp."beginAt" <= :nowTime', { nowTime })  // 過濾掉還沒開始的
      .andWhere('vpp."deletedAt" is null')  // 還沒軟刪除
      .orderBy('vpp.vipPlanId', 'ASC')     //  vipPlanId 小到大
      .addOrderBy('vpp.beginAt', 'DESC')   //  最近的時間放第一個
  }

```


下面四個表都有 vip plan fk
![[IMG-wemo_pass_tables-20241003104934095.png]]


#### Create Table: vip_plan_pricing VIP 計畫售價
- 時間區間未到達才可以刪除，已經發生則無法再修改或刪除
- Initial Record:
insert into vip_plan_pricing (id, "vipPlanId", price, "beginAt") values (1, 1, 99, now())

| name          | type                               | description        |
| ------------- | ---------------------------------- | ------------------ |
| id            | unsigned integer, ai, pk, not null | 計畫售價ID             |
| vipPlanId     | integer not null                   | 關聯計畫ID             |
| price         | integer not null                   | 販售價錢               |
| originalPrice | integer, nullable                  | 使用者顯示的原始售價 (季年費新增) |
| beginAt       | datetime                           | 開始生效時間             |
| createdAt     | datetime                           | 建立時間               |
| updatedAt     | datetime                           | 更新時間               |
| deletedAt     | datetime, null                     | 刪除時間               |

記錄 vip plan 的 price
另外 vip detail 上也會記錄是用那一個 vip plan pricing
![[IMG-wemo_pass_tables-20241003104948948.png]]


what is price and orignal price?

price 是實際價格
original price 是原價
![[IMG-wemo_pass_tables-20241003105012032.png]]

![[IMG-wemo_pass_tables-20241003105038472.png]]


另一個例子
![[IMG-wemo_pass_tables-20241003105038626.png]]

![[IMG-wemo_pass_tables-20241003105103089.png]]



#### Create Table: vip_plan_benefit VIP 計畫優惠
- 時間區間未到達才可以刪除，已經發生則無法再修改或刪除
- 週期發送時間券預設3張，不可少於3張
- 現階段 record 先寫死，下一階段開發後台編輯資料功能
- Initial Record:
```sql
INSERT
    INTO
    vip_plan_benefit (
        id
        , "beginAt"
        , "tripDiscountMap"
        , "durationVoucherQuantity"
        , "otherBenefits"
        , "durationVoucherTitle"
        , "durationVoucherDescription"
        , "otherBenefitss"
    )
VALUES (
    1
    , now()
    , '[{"threshold":0,"discount":1.00},{"threshold":5,"discount":0.95},{"threshold":10,"discount":0.9},{"threshold":20,"discount":0.85},{"threshold":30,"discount":0.8}]'
    , 3
    , '{"en":"每月3張", "zh-tw":"每月3張"}'
    , '{"en":"每月3張說明", "zh-tw":"每月3張說明"}'
    , '[]'
)
	
```



| name                       | type                               | description                         |
| -------------------------- | ---------------------------------- | ----------------------------------- |
| id                         | unsigned integer, ai, pk, not null | 計畫優惠ID                              |
| beginAt                    | datetime                           | 開始生效時間                              |
| tripDiscountMap            | text                               | 累積趟數折扣門檻表  (stringified json array) |
| durationVoucherQuantity    | number                             | 週期券 (基本3張)                          |
| durationVoucherTitle       | i18n                               | 週期券說明標題                             |
| durationVoucherDescription | i18n                               | 週期券說明描述                             |
| otherBenefits              | text                               | 其他優惠說明  (stringified json array)    |
| createdAt                  | datetime                           | 建立時間                                |
| updatedAt                  | datetime                           | 更新時間                                |
| deletedAt                  | datetime, null                     | 刪除時間                                |


統計和 detail 都會記錄benefit id
![[IMG-wemo_pass_tables-20241003105103213.png]]




#### Create Table:  user_vip_detail  使用者 VIP 訂閱記錄
此表紀錄使用者的訂閱歷程與週期時間
INDEX: [userId + successPurchasedAt + canceledAt] 

| name                   | type                        | description            |
| ---------------------- | --------------------------- | ---------------------- |
| id                     | pk, not null                | 紀錄ID                   |
| userId                 | unsigned integer, not null  | 使用者ID                  |
| vipPlanId              | integer                     | 計畫ID                   |
| vipPlanPricingId       | integer                     | 計價ID                   |
| vipBenefitId           | integer                     | 折扣優惠ID                 |
| orderId                | string                      | 訂單編號                   |
| price                  | integer                     | 訂閱當下的計畫價錢              |
| successPurchasedAt     | datetime, nullable          | 付款成功時間，失敗或未付款為 null    |
| totalDurationMonth     | integer                     | 方案月份數量                 |
| remainingDurationMonth | integer                     | 剩餘月份數量                 |
| beginAt                | datetime, nullable          | 實際開始使用時間               |
| endAt                  | datetime, nullable          | 實際結束使用時間               |
| bySubscription         | bool                        | 此detail是否為自動續訂下購買      |
| refund                 | number, default 0, not null | 退款金額                   |
| canceledAt             | datetime, nullable          | 取消時間                   |
| cancelReason           | string, nullable            | 取消原因註記                 |
| cancelManager          | string, nullable            | 取消操作管理者名稱              |
| lastVipUserStatisticId | unsigned integer, nullable  | 最後生效的統計資料ID，未生效過為 null |
| createdAt              | datetime                    | 建立時間                   |
| updatedAt              | datetime                    | 更新時間                   |


detial 上面會帶 vip pricing, discount and benefit 的 id
然後 recharing 會帶 detail 的 id

iow...
- detail need to have pricing, discount and benefit info
- detial have 3 component: prcing, discount and benefit
![[IMG-wemo_pass_tables-20241003105124060.png]]
  
#### Create Table: user_vip_statistic 使用者 VIP 每個區間用量
此表紀錄使用者 VIP 期間的每個一個週期的統計
- 到期前一天執行排程 (時間待定)
- 可查找出使用者已訂閱未消耗完的訂單
- 建立下一個月的統計紀錄



Unique Key: [userId, userVipDetailsId, durationIndex] => insert or ignore


建立會從 vip detail 's data copy over 當你購買 vip plan時

code can see:
api/v2/users/service/vip/findOrCreateStatistic.ts:74
`const userVipStatistic = await db.userVipStatistic.create(


detial table will write back below cols into its table
- lastVipUserStatisticId
- remainingDurationMonth: userVipDetail.totalDurationMonth - durationIndex


durationIndex =1 if statistic is first created
if statistic is already exist -> durationIndex = 1 + lastUserVipStatistic.durationIndex


also created at VIPMonthlyJob 如果有漏發






| name              | type                       | description             |
| ----------------- | -------------------------- | ----------------------- |
| id                | pk, not null               | 紀錄ID                    |
| userId            | unsigned integer, not null | 使用者ID                   |
| userVipDetailId   | integer                    | VIP 訂閱記錄ID              |
| vipPlanId         | integer                    | VIP 方案ID                |
| vipBenefitId      | integer                    | 目前使用者的折扣優惠ID            |
| durationIndex     | integer                    | 方案第幾週期                  |
| beginAt           | datetime                   | 區間開始                    |
| endAt             | datetime                   | 區間結束                    |
| tripCount         | interger                   | 有效計算趟數                  |
| usedRebateVoucher | interger                   | 已使用折價券張數                |
| rebateVoucherIds  | string                     | 發送的時間騎乘券ID (json array) |
| createdAt         | datetime                   | 建立時間                    |
| updatedAt         | datetime                   | 更新時間                    |


統計表上會有plan id, user id, benefit id
另外 rent 和 detail 可以 related 到 統計表

in other word
統計表 gather the relationship b/n plan, user and benefit
like, which user at what plan have what beneift 的 record
![[IMG-wemo_pass_tables-20241003105134398.png]]


#### Create Table: user_vip_subscription 使用者 VIP 訂閱設定

紀錄使用者是否續訂 VIP 設定與 VIP 方案/付款方式
- 客服取消時，此紀錄應該取消自動續訂
- 如果 lastPayAt 大於等於 nextChargeAt 表示續訂付款失敗

| name              | type                           | description     |
| ----------------- | ------------------------------ | --------------- |
| userId            | unsigned integer, not null, pk | 使用者ID           |
| enable            | boolean                        | 是否開啟自動續訂        |
| nextChargeAt      | datetime                       | 方案到期前 2 天 20:00 |
| lastPayAt         | datetime                       | 最後嘗試付款時間        |
| continueVipPlanId | interger                       | 續訂計畫ID          |
| paymentType       | integer                        | 續訂付款方式          |
| createdAt         | datetime                       | 建立時間            |
| updatedAt         | datetime                       | 更新時間            |
訂閱表 上記錄 誰(user id )訂閱了那個plan(plan id)
![[IMG-wemo_pass_tables-20241003105143190.png]]




#### Modify Table: rent 機車租借紀錄

| name               | type               | description      |
| ------------------ | ------------------ | ---------------- |
| userVipStatisticId | interger, nullable | Vip User 統計資料 ID |
| startupUTCAt       | datetime, nullable | 使用者實際開始騎乘時間      |


#### Modify Table: rebate_voucher_template 促銷活動
參考 api/v2/rebateVouchers/service/index.ts addVoucher  
設計直接發送騎乘券

| name       | type   | description   |
| ---------- | ------ | ------------- |
| sourceType | string | 行銷類型，增加 (vip) |

![[IMG-wemo_pass_tables-20241003105143292.png]]

- [ ] sourceId is vip plan?



#### Add Record: charging_item 收費項目

| id  | name  | type |
| --- | ----- | ---- |
| 8   | VIP方案 | 7    |
|     |       |      |
  ![[IMG-wemo_pass_tables-20241003105150593.png]]

## Ceres Migrations

#### Modify Table: Orders 訂單資料

增加 VIP 訂單類型與 VIP 資料 ID

| name            | type             | description                                                                                                                                         |
| --------------- | ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| orderType       | number           | 訂單類型<br><br>rent = 0,<br><br>timePlan = 1,<br><br>ticket = 2,<br><br>walletArrears = 3,<br><br>pickup = 4,<br><br>reservation = 5,<br><br>**vip=6** |
| userVipDetailId | number, nullable | userVipDetail id                                                                                                                                    |
|                 |                  |                                                                                                                                                     |
![[IMG-wemo_pass_tables-20241003105156499.png]]




# all tabls view

```plantuml




entity vip_plan {
    id: bigserial(pk)
    title: i18n jsonb
    durationMonth int
    planType varchar
    continuedPlanId int8
}

entity vip_plan_pricing {
    id	bigserial (pk)
    vipPlanId int8 (fk)
    ---
    description jsonb
    price int4
    originalPrice int4
}

vip_plan ||--|{ vip_plan_pricing: pricing 可以選 plan


entity vip_plan_benefit{
    id	bigserial(pk)
    ---
    tripDiscountMap	jsonb
    durationVoucherQuantity	int4
    durationVoucherTitle	jsonb
    durationVoucherDescription	jsonb
    otherBenefits	jsonb
}
'note bottom {
'	tripDiscountMap: [
'		 {
'            "discount": 1,
'            "threshold": 0
'          },
'          {
'            "discount": 0.95,
'            "threshold": 5
'          },
'          {
'            "discount": 0.9,
'            "threshold": 10
'          },
'          {
'            "discount": 0.85,
'            "threshold": 15
'          },
'          {
'            "discount": 0.8,
'            "threshold": 20
'          }]
'}

entity user {
    id	int8 (pk, idx)
    ---
    ...
}


entity user_vip_subscription {
    userId	int8 (pk, idx)
    continueVipPlanId	int8(fk)
    ---
    enable	bool
    nextChargeAt	timestamp
    lastPayAt	timestamp
    paymentType	int4
    ---
    idx: enable_nextChargeAt
}

vip_plan ||--|{ user_vip_subscription
user ||--|{ user_vip_subscription


entity user_vip_statistic {
    id	bigserial(pk)
    userId	int8 (fk)
    userVipDetailId	int8 (fk)
    vipPlanId	int8	 (fk)
    vipBenefitId	int8	(fk)
    ---
    durationIndex	int4
    tripCount	int4
    usedRebateVoucher	int4
    rebateVoucherIds	jsonb
    ---
    idx: userId_beginAt_endAt
    idx: userId_userVipDetailId_durationIndex
}




entity order {
id: pk
---
...
}

entity user_vip_detail {
id	bigserial(pk)
userVipDiscountId	int8(fk)
lastVipUserStatisticId	int8(fk)
vipPlanPricingId	int8(fk)
userId	int8(fk)
vipPlanId	int8(fk)
vipBenefitId	int8(fk)
---
orderId	varchar(20)
price	int4
successPurchasedAt	timestamp
totalDurationMonth	int4
remainingDurationMonth	int4
bySubscription	bool
refund	int4
canceledAt	timestamp
cancelReason	text
cancelManager	varchar(128)
---
idx: userId_successPurchasedAt_cancaledAt
idx: lastVipUserStatisticId
}

entity user_vip_discount {
id: pk
}

user ||--|{ user_vip_statistic
user_vip_detail ||--|{ user_vip_statistic
vip_plan ||--|{ user_vip_statistic
vip_plan_benefit ||--|{ user_vip_statistic

user_vip_discount ||--|{ user_vip_detail
user_vip_statistic ||--|{ user_vip_detail
vip_plan_benefit ||--|{ user_vip_detail
user ||--|{ user_vip_detail
vip_plan ||--|{ user_vip_detail
order ||..|{ user_vip_detail: no fk


entity user_vip_recharging {
id	bigserial(pk)
userId	int8	(fk)
userVipDetailId	int8 (fk)
---
originalPrice	int4
rechargePrice	int4
orderId	varchar(20)
rechargedAt	timestamp
rechargeReason	text
rechargeManager	varchar(128)
}

user ||--|{ user_vip_recharging
user_vip_detail ||--|{ user_vip_recharging
order ||..|{ user_vip_recharging: no fk


entity user_vip_discount{
id	bigserial	(pk)
userId	int8	(fk)
---
source	varchar	
sourceId	int8	
discount	int4	
successPurchasedAt	timestamp	
allowVipPlanIds	jsonb	
discountMode	varchar	
usedVoucher	int4	
addTripCount	int4	
--
idx: userId_beginAt_endAt
}
user ||--|{ user_vip_discount




```
