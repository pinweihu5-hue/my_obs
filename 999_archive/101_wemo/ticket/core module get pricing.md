- [mr](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5272)
- [userRole mr](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5231/diffs#a6f438b9f4128d412732605df9aae4d39a5f3d69)


異動範圍
- scooters
    - lock quote 機制
    - filter out non-rentable scooters
- scooterById
    - lock quote 機制
    - return response 相容處理
- reserve
    - lock quote 機制
        - 確認 app 傳的 ppp id 跟 cache 是否一樣 -> 不同 throw err
    - save to rentConfig
- rent
    - lock quote 機制
    - 確認 app 傳的 ppp id 跟 db 是否一樣 -> 不同的話確認是否是合法 id -> 不是的話 throw err / 是的話 update rentConfig

---

修 test
- 4 個 API 的 test (scooter, scooterbyId, reserver and rent)
	- [x] 修
		- [x] test/api/v2/scooters.test.js
	- [x] 新增應該要有的
		- [x] scooters
			- 應該要檢查有過濾的功能
		- [x] get scooter by id
				- 可以正確拿到價格
					- 一般車
					- 紅標車
	- [x] 修預約
	- [x] 新增預約測試邏輯
		- 一般車和紅標車 情境都要測試到
		- 確認有噴錯 when id 不對上
		- 確認如果沒噴錯，要存入 rentConfig
	- [x] 新增租車測試邏輯
		- 如果app有傳上來
			- 確認噴錯當 rentconfig 沒東西
			- 確認如果 id mismach時
				- 不是合法的要 噴掉
				- 合法的要 update rentConfig
		- 如果app沒有傳上來話 -> do nothing
- core module 的 test
	- [x] vehiclePricing
- 有哪些 unit test/ svc level 的 test 要寫
	- ~~api/v2/scooters/utils/pricingRuleTransformer.ts~~
	- ~~getScooterIdsByVehicleId~~
	- ~~getVehicleIdsByScooterIds~~
	- ~~getCachedQuote~~
		- ~~default -> eager case~~
			- ~~cachedQuote is undef -> check cachedQuote use db userRoleIds and current time~~
			- ~~cachedQuote is CloseToExpired -> check cachedQuote use db userRoleIds and current time~~
		- ~~use false -> not eager case~~
			-  ~~cachedQuote is undef -> check cachedQuote use db userRoleIds and current time~~
			- ~~cachedQuote is CloseToExpired -> use cached data~~
			- ~~cachedQuote is effective -> use cached data~~
	- ~~coreModules/services/vehiclePricingService.ts~~
		- ~~getVehicleIdToPricingRules~~
			- ~~no vehicleIds case => return {}~~
			- ~~throw this error -> getVehicleWithTags failed since no pricing id found: rentalInfos${JSON.stringify(rentalInfos)~~
				- ~~順便思考一下是否合理~~
			- ~~call vs and ps with many data~~


# 預約目前邏輯

const tag = '/v2/users/controller/rents/post'
const resultVO = await rentAgent.reserveScooter({

- req. pricingPlanPriceId  pass in
- 有 validate  via validateNumberString
	- const pricingPlanPriceId = validateNumberString(pricingPlanId) as string
	- 只有確認以下兩點
		- 確認不是空字串
		- 確認進來的字串轉為 Number 後， isNaN 會 ret false
	- [ ] 如果這邊需要處理 `_old` ->  這邊要改
- pricingPlanPriceId 會存入 rent 表


所以預約這邊目前沒有任何機制去跟車子時候的價格掛勾，就是沒有 lock 機制
-> 因此如果 我們現在要做 lock 機制 -> 這邊也不可以因為 新增的 lock 機制 error 掉


# 租車目前邏輯
export const patchByIdV19: WeMoRouter = async (req, res, next) => {
const data = await _rent({ rentId, pricingPlanId, timePlanId, rebateVoucherId, userPO, lang, now })
const tag = `${this.tag}/rent`

- 一樣會 pass req.pricingPlanId 進來
- 如果 pricingPlanId 是 undef -> 會經過  validateNumberString
- 如果 pricingPlanPriceId 有值，然後跟rent.pricingPlanPriceId不同，會 warn, 然後會使用req傳上來的 pricingPlanPriceId
- `const jobs: Promise<any>[] = [`
	- 這一段 code 會把要更新的相關 db 操作並行打出去, save rentConfig 也要放在這裡嗎?



目前的邏輯
- reserve
	- validate input 的 pricingPlanPriceId 的 isNaN is false
	- 然後把 pricingPlanPriceId 會存入 rent 表
- rent
	- validate input 的 pricingPlanPriceId 的 isNaN is false
	- 確認 傳入的 pricingPlanPriceId 和 rent 表一樣
	- 如果不一樣，使用 input的為準, logg 一下

調整後的邏輯
- reserve
	- 先不管 validate (先不管 `_old` 這段)
	- old code still work 下，新機制可以有 lock 機制 -> 存入 rentConfig 表
- rent
	- 先不管 validate (先不管 `_old` 這段)
	- 確認 傳入的 pricingPlanPriceId 和 已經在rentConfig 表裡面的 rule 是否 一樣
	- 如果不一樣，使用 input的為準, logg 一下



# 預約
- 處理一下本來這邊的 code, pricingPlan 會來這邊 validate, 要改成 validate pricingRule?
- get cach (queryTime and roleIds)
	- 從結果判斷 -> 有三種情況 undef | effective | 快過期
		- undef 表示 cache 沒資料
			- 表示  get scooter/:id  5分鐘後都沒有點預約，資料已經不見。
			- 回補新的 cache Info  (set by id)
			- 去 VS and PS get pricing rule
			- check if pricing rule 是否 跟 input 一樣 
				- 如果不一樣，log.warn
				- 如果一樣，那就是這段時間資料面沒有變動
			- save into rentConfig
		- effective
			- 直接用cached info 去拿資料 
			- use cached info 去 拿到新的 ruleId 
				- 應該要跟 input 一樣
				- 如果不同，應該是實作有問題，應該要噴錯
			- save into rentConfig
		- 快過期
			- 同上
- 哪邊要 save into rentConfig???
- 有關 `_old` 的處理
	- ask josh first 
	- 如果要改3個版本，那這邊要把 pass in 的 `_old` 拿掉




bk
```ts fold


export async function getPricingRules(
  pricingPlans: PricingPlan[],
  purchasedTimePlans: PurchasedTimePlan[],
  userCategoryId: string,
  userId: string,
  rebateVoucherEnable?: boolean,
  scooterId?: string
): Promise<GetPricingResVO> {
  const tag = 'v2/users/utils/pricingPlan/getPricingRules'

  /**
   * PricingPlan(price) by user category id */
  const pricingPlansByUserCategory = R.groupWith((a, b) => a.userCategoryId === b.userCategoryId, pricingPlans)
  let pricingPlanPrices = await Promise.all(pricingPlansByUserCategory.map(async (pp) => getCurrentPricingPlanPrice(pp, userCategoryId, userId)))

  /**
   * default pricing not found
   * (Maybe selected userCategory is not setting pricing plan)
   * using user default fallback
   */
  if (!pricingPlanPrices.find((p) => p.isDefault)) {
    const standardOrJunior = pricingPlanPrices.find((p) => p.isBasic === true)
    if (!standardOrJunior) {
      throw new Error(`No basic pricing plan fetched for userCategoryId[${userCategoryId}]`)
    }
    standardOrJunior.isDefault = true
  }

  const first = pricingPlanPrices.find(({ isDefault, isBasic }) => (isDefault && isBasic) || (isDefault && !isBasic))
  const last = pricingPlanPrices.find(({ isDefault, isBasic }) => isBasic && !isDefault)
  const rest = pricingPlanPrices
    .filter(({ isDefault, isBasic }) => isDefault === false && isBasic === false)
    .sort((a, b) => Number(a.pricePerMinute) - Number(b.pricePerMinute))
  const sorted = []
  first && sorted.push(first)
  rest && sorted.push(...rest)
  last && sorted.push(last)

  // Sort by boolean and true is first!
  const timePlans = purchasedTimePlans.sort((a, b) => Number(b.isDefault) - Number(a.isDefault))

  const useCoreSystem = true

  if (useCoreSystem && scooterId) {
    // scooterId to vehicleId
    const tenantId = config.get<string>('wemoTenantId')

    const vehicleId = (
      await CoreModule.getInstance().vehicle.getVehicleByPlateWithTags({
        tenantId,
        licensePlate: scooterId
      })
    ).data?.id

    if (R.isNil(vehicleId)) {
      throw new Error('No vehicle found')
    }


    // TODO: VS and PS 的交互的邏輯整合層要寫在哪裡? 
    const nowInISOString = moment().toISOString()
    const pricingAndRentalInfoResponse = await CoreModule.getInstance().vehicle.getPricingAndRentalInfo({
      vehicleIds: [vehicleId],
      rentFlow: 1,
      queryTime: nowInISOString
    })

    const pricingId = pricingAndRentalInfoResponse.data[0].pricingId
    if (R.isNil(pricingId)) {
      throw new Error('No pricing id found')
    }

    const userCapabilities = await getUserCapabilities(userId)
    const userRoleId = await getActiveUserRoleIds(userCapabilities)

    const pricingRulesAtQuoteResponse = await CoreModule.getInstance().price.getPricingRulesAtQuote({
      pricingIds: [pricingId],
      roleIds: userRoleId.map((v) => Number(v)),
      ISOStringAtQuote: nowInISOString
    })

    const queryCache = new LockedQuoteInfo(userId).setById(userId, userRoleId)

    //   export interface GetPricingRulesAtQuoteResponse {
    //     result: string;
    //     pricingIdAndPricingRules: PricingIdAndPricingRules[];
    // }
    // export interface PricingIdAndPricingRules {
    //     pricingId: string;
    //     data: PricingIdAndPricingRules_PricingRule[];
    // }
    // export interface PricingIdAndPricingRules_PricingRule {
    //     id: string; // pricingRule
    //     algorithm: number;
    //     role: number;
    //     configs?: CurbBasicConfigs | undefined;
    // }

    // TODO: 這邊你確定只會有第一個?
    const pricingRules = pricingRulesAtQuoteResponse.pricingIdAndPricingRules[0].data.map((pricingRule) => {
      return {
        id: pricingRule.id,
        startPrice: pricingRule.configs?.return?.weekdayNonSpecialPeriod?.initialMinPrice || 0,
        freeMinutes: pricingRule.configs?.return?.weekdayNonSpecialPeriod?.initialMin || 0,
        pricePerMinute: pricingRule.configs?.return?.weekdayNonSpecialPeriod?.pricePerMin || 0
      }
      // const pricingPlans = [
      //   {
      //     id: "571",
      //     startPrice: 18,
      //     freeMinutes: 6,
      //     pricePerMinute: 3,
      //     name: "一般用戶",  // TODO: 要如何處理這三個? XD
      //     isDefault: true,
      //     isBasic: true,
      //   },
      // ];
    })
  }

  return {
    pricingPlans: sorted,
    timePlans,
    rebateVoucher: { enable: R.defaultTo(false, rebateVoucherEnable) }
  }
}

```



user check -> cache快到了補上
user reserve -> 不需要

哪裡要改?
需要多一個 getById

- 預約時需要 getById

1-6 min, 預約的 ttl
看車還會看即將到期 5~6 min and then get new quote

之後可以做的:
scooter and scooters
多一個欄位
query time 給 users
這個 query time 是 redis 存的
然後 app 就可以比對差異，就可以知道報價是否過期, 就可以跳 toss

預約 validate 機制
兩個情況會報錯
一個是真的 input err
一個是預約時過期，因此 app 傳上來的 id 跟 redis 記錄的對不上


// (1, null, null, null), -- 所有用戶
// (2, '1', null, null), -- WeMo 員工
// (3, '2', null, null), -- 一般用戶
// (4, '5', null, null); -- MKT



第一版 pricingRuleMigration
```ts fold

  if (config.has('pricingRuleMigration')) {
    // pricingRuleMigration: "old", "both" ,"new"
    const pricingRuleMigration = config.get('pricingRuleMigration')
    const {
      pricingPlans: pricingPlansOld,
      timePlans,
      rebateVoucher
    } = await getPricing(pricingPlans, purchasedTimePlans, userCategoryId, user.id, setting.rebateVoucher)

    if (pricingRuleMigration === 'old') {
      // TODO: 確定一下要不要搞 old?
      // TODO: pricingPlansOld 裡面要帶上 "old postfix"
      // TODO: get scooter:/id, 預約, 起租  3 個 API，APP 回傳的 pricingPlanId 會帶上 pricingPlanId '571_old', 需要把它轉為  571
      pricing = {
        pricingPlans: pricingPlansOld,
        timePlans,
        rebateVoucher
      }
    } else if (pricingRuleMigration === 'both') {
      // 先打 new, 如果出問題，就 fallback to old data
      try {
        const pricingRules = await getPricingRule(scooterId, user.id)
        pricing = {
          pricingPlans: pricingRules,
          timePlans,
          rebateVoucher
        }
      } catch (error) {
        logger.warm({ tag, error })
      }

      if (R.isNil(pricingRules)) {
        pricing = {
          pricingPlans: pricingPlansOld,
          timePlans,
          rebateVoucher
        }
      }
    } else if (pricingRuleMigration === 'new') {
      try {
        const pricingRules = await getPricingRule(scooterId, user.id)
        pricing = {
          pricingPlans: pricingRules,
          timePlans,
          rebateVoucher
        }
      } catch (error) {
        logger.warm({ tag, error })
        throw error
      }
    } else {
      throw new Error(`Unknown pricingRuleMigration setting: ${pricingRuleMigration}`)
    }
  }

```


setById
```ts fold
  /**
   * 當使用者呼叫 scooters, scooters:/id 等AP撈取 pricing, style 等資料時
   * 我們會透過此 API lock 使用者的報價時間
   */
  async setById(userId: string, lockedData: LockedData) {
    const tag = this.TAG + '/set'
    try {
      return await client.set(this.getKey(), JSON.stringify(lockedData), 'EX', this.TTL_IN_SEC)
    } catch (error) {
      logger.error({ error, tag, userId })
      throw new Error(CustomErrorsV2.REDIS_SET_DATA_FAILURE.type)
    }
  }

  /**
   * 當使用者呼叫 scooters, scooters:/id 等AP撈取 pricing, style 等資料時
   * 我們會透過此 API 拿取使用者的報價時間, 確認是否過期
   * 如果過期的話，我們需要重新設定使用者的報價時間
   * 如果沒有過期，我們會使用此資料來獲取報價資料
   */
  async getById(userId: string) {
    const tag = this.TAG + '/getById'
    try {
      const data = await client.get(this.getKey())
      if (data === null) return

      return JSON.parse(data)
    } catch (error) {
      logger.error({ error, tag, userId })
      throw new Error(CustomErrorsV2.REDIS_GET_DATA_FAILURE.type)
    }
  }


```
 
- [ ] rebase feat/core_module_for_pricing 因為這邊已經以整入 pricing svc 的東西



# expiredAt
double check 一下上次問的 expiredAt 欄位的行為～
expiredAt 他印象跟月租方案有關, 但是如果現在月租沒了，是否這個欄位就可以不用給了
這跟我們上次談的理解不同，想要跟你 double check 是否還是會需要?


下面這個 API (get scooter by id) ，裡面的pricingPlans 的 expiredAt 屬性
- 還有在使用嗎?
- 使用行為是什麼?


結論：
顯示會有差異


===
IOS

如果有expiredAt 而且 startPrice == 0 顯示 : %@前 %@ 分鐘 0 元，後續每分鐘 %@ 元，%@ %@ 到期。  
如果有expiredAt 而且 startPrice != 0 顯示 :優惠費率前%@分鐘 %@元，後續 %@元／分，%@ %@到期。  
如果沒有expiredAt 前 %@ 分鐘 %@ 元，後續 %@ 元/分

安卓
![[IMG-core module get pricing-20250810201310961.png|615]]

![[IMG-core module get pricing-20250810201313007.png]]


---

```ts fold



when renConfig.pricingRuleId is not the same with pricingPlanPriceId
update renConfig when pricingPlanPriceId is valid
throw error when pricingPlanPriceId is invalid

backup

   // console.log('pricingRuleTransformer::pricingRule -->', pricingRule)
    // console.log('lang -->', lang)

    // console.log('userApp old _pricingPlans -->', _pricingPlans)
    // [
    //   {
    //     id: '3',
    //     startPrice: 15,
    //     freeMinutes: 6,
    //     pricePerMinute: 2.5,
    //     name: 'Standard',
    //     isDefault: true,
    //     isBasic: true,
    //     expiredAt: undefined
    //   }
    // ]

```

---


# 盤點 APIs

get /v2/scooters/:id([A-Z0-9]+-[A-Z0-9]+|[A-Z0-9]+)/serviceLogs ??
get /v2/scooters/:id([A-Z0-9]+-[A-Z0-9]+|[A-Z0-9]+)/maintenanceLogs ??
get /v2/scooters/:scooterId([A-Z0-9]+-[A-Z0-9]+|[A-Z0-9]+)/lastReturn ??
get /v2/scooters/:id([A-Z0-9]+-[A-Z0-9]+|[A-Z0-9]+)/zones/current ??
get /v2/users/me/scooterStyles ??

get /v13/scooters/:id([A-Z0-9]+-[A-Z0-9]+|[A-Z0-9]+)$

- table.addAuthEndpoint(13, { method: 'get', endpoint: '/:id([A-Z0-9]+-[A-Z0-9]+|[A-Z0-9]+)$', controller: controller.listByIdV13 })
- data = await userApp.listById(id, req.me.raw, { lang: req.getLocale(), updateBusinessState }).then((scooter) => {
- 原本 get style 的機制
  - exports.listById = async (id, user, { lang, updateBusinessState }) => {
- 還是要這一層抽掉?? const pricing = await getPricing(pricingPlans, purchasedTimePlans, userCategoryId, user.id, setting.rebateVoucher)

get scooter by id

get /v2/scooters/
- const tag = 'v2/scooters/controller/list'
	- call const [data, metaRadius] = await list(me.raw, lat, lng, radius)
		- let scooters = (await db.scooter.getManyForUser(groupIds, boundingRegion
	- call checkHasScooterWithinCustomRadius
		- call filterByRadius




預約
POST /v24/users/me/rents HTTP/1.1


prod 
- 員工 pricingPlanId 559
- 一般用戶  pricingPlanId 630


{"pricingPlanId":"571","scooterId":"ION-0011"}

{
  "result" : "success",
  "data" : {
    "scooterId" : "ION-0011",
    "rentId" : "2464930"
  }
}



起租
PATCH /v24/users/me/rents/2464930 HTTP/1.1


{"type":3,"timePlanId":"null","pricingPlanId":"571"}
{
  "result" : "success",
  "data" : {
    "scooterId" : "ION-0011"
  }
}



# GET /v24/users/me/renting 呼叫時機


![[IMG-core module get pricing-20250810201315066.png]]
![[IMG-core module get pricing-20250810201316038.png]]



![[IMG-core module get pricing-20250810201316934.png]]

![[IMG-core module get pricing-20250810201317059.png]]

# isBasic and isDefault

結論： isBasic 沒差，一律給 true

```ts


  pricingPlans: [
	{
	  id: "571", //
	  startPrice: 18,
	  freeMinutes: 6,
	  pricePerMinute: 3,
	  name: "一般用戶",
	  
	  // 安卓: 沒用到，要給, 但給t/f都沒差
	  // iOS: 如果是true 則會拿這個方案 預設選取
	  isDefault: true,  


	  // 安卓: 有用到 -> 給 T/F 文案會幾乎99%一樣（中文一樣，英文有一點點不同）
	  // iOS: 沒有用到
	  isBasic: true,  
	},

```



## 安卓 isBasic
下圖是安卓用用兩個地方: 摺疊和彈窗
摺疊：
中文一樣
英文意思幾乎一樣, 文法有一點不同
如下
![[IMG-core module get pricing-20250810201317838.png]]
彈窗
中文一樣
英文也算一樣(每一個英文字都一樣，除了一個minutes，一個是 minute, 單複數)
-> 結論，BE 給 T/F 都沒差，但是要給


拿車 by id
GET /v24/scooters/APP-0010?lang=zh-tw HTTP/1.1
```json fold

{
  result: "success",
  data: {
    id: "APP-0010",
    batPercent: -1,
    groupId: "0",
    lat: 25.0525019,
    lng: 121.5460794,
    styleId: "73",
    styles: {
      pin: "Skyblue",
    },
    images: {
      covers: [
        "https://storage.googleapis.com/wemo-backend/scooterInAppImages/1-Normal/covers/01.png",
      ],
    },
    availableDistance: 0,
    pricing: {
      pricingPlans: [
        {
          id: "571", //
          startPrice: 18,
          freeMinutes: 6,
          pricePerMinute: 3,
          name: "一般用戶",
          
          // 安卓: isDefault 沒有用到，要給, 但給t/f都沒差
          // iOS: T -> ??  F -> ??
          isDefault: true,  


          // 安卓: 有用到 -> 給 T/F 文案會幾乎99%一樣（中文一樣，英文有一點點不同）
          // iOS: T -> ??  F -> ??
          isBasic: true,  
        },
        {
          id: "573", //
          startPrice: 18,
          freeMinutes: 6,
          pricePerMinute: 3,
          name: "WeMo員工",
          isDefault: true, //   
          isBasic: true,   //
        },
      ],
      timePlans: [],
      rebateVoucher: {
        enable: true,
      },
    },
    parkingSpaceId: null,
    parkingLot: null,
    alteredBatPercent: -1,
    alteredDistance: 0,
    userBatSwap: false,
  },
};


```

![[IMG-core module get pricing-20250810201317960.png]]

![[IMG-core module get pricing-20250810201318385.png]]


拿車 by id
GET /v24/scooters/APP-0010?lang=zh-tw HTTP/1.1
```ts


{
  result: "success",
  data: {
    id: "APP-0010",
    batPercent: -1,
    groupId: "0",
    lat: 25.0525019,
    lng: 121.5460794,
    styleId: "73",
    styles: {
      pin: "Skyblue",
    },
    images: {
      covers: [
        "https://storage.googleapis.com/wemo-backend/scooterInAppImages/1-Normal/covers/01.png",
      ],
    },
    availableDistance: 0,
    pricing: {
      pricingPlans: [
        {
          id: "571", //
          startPrice: 18,
          freeMinutes: 6,
          pricePerMinute: 3,
          name: "一般用戶",
          isDefault: true,  
          isBasic: true,  
          expiredAt: '2024-10-01T04:39:47.942Z'
        },
        {
          id: "573", //
          startPrice: 18,
          freeMinutes: 6,
          pricePerMinute: 3,
          name: "WeMo員工",
          isDefault: true, 
          isBasic: true,   
          expiredAt: '2024-10-01T04:39:47.942Z'
        },
      ],
      timePlans: [],
      rebateVoucher: {
        enable: true,
      },
    },
    parkingSpaceId: null,

```


拿車
GET /v24/scooters?lat=25.0523409158859&lng=121.5464905276895 HTTP/1.1

```json fold

Host: kottos-qat.wemoscooter.com
Accept: _/_
Accept-Encoding: gzip, deflate
User-Agent: WeMoScooter/6.4.2QAT (iOS 17.6.1; iPhone13)
Accept-Language: zh-tw
Authorization: WeMo eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIyMDEyMTIiLCJ1c2VyX2lkIjoiMjAxMjEyIiwidmVyc2lvbiI6IjIuMCIsImlhdCI6MTcyNTk1MTc1OSwiZXhwIjoxNzI2MzgzNzU5fQ.tpbf7-H05jkqR6cKyrjwnfIpanO4XKVL0Ria2IMMjn0
Connection: close

{
  result: "success",
  data: [
    {
      id: "QAL-0521",
      groupId: "0",
      batPercent: 45,
      lat: 25.0535613797,
      lng: 121.5438345906,
      styles: { pin: "Skyblue" },
      styleId: "73",
      alteredBatPercent: 45,
      alteredDistance: 20.2,
      userBatSwap: false,
    },
    {
      id: "EWN-2012",
      groupId: "0",
      batPercent: 45,
      lat: 25.0528766824,
      lng: 121.546208063,
      styles: { pin: "Skyblue" },
      styleId: "73",
      alteredBatPercent: 45,
      alteredDistance: 20.2,
      userBatSwap: false,
    },
  ],
  meta: { radius: 1000, hasScooterWithinCustomRadius: true, customRadius: 150 },
};



```

停車場
GET /v24/parkingLots?lang=zh-tw&lat=25.0523409158859&lng=121.5464905276895 HTTP/1.1
```json fold

Host: kottos-qat.wemoscooter.com
Accept: _/_
Accept-Encoding: gzip, deflate
User-Agent: WeMoScooter/6.4.2QAT (iOS 17.6.1; iPhone13)
Accept-Language: zh-tw
Authorization: WeMo eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIyMDEyMTIiLCJ1c2VyX2lkIjoiMjAxMjEyIiwidmVyc2lvbiI6IjIuMCIsImlhdCI6MTcyNTk1MTc1OSwiZXhwIjoxNzI2MzgzNzU5fQ.tpbf7-H05jkqR6cKyrjwnfIpanO4XKVL0Ria2IMMjn0
Connection: close

{
  result: "success",
  data: [
    {
      id: "51",
      lat: 25.092051,
      lng: 121.581129,
      nickname: "水街停車場",
      businessHours: "24H",
      regularBusinessHours: [
        { endAt: null, beginAt: null },
        { endAt: null, beginAt: null },
        { endAt: null, beginAt: null },
        { endAt: null, beginAt: null },
        { endAt: null, beginAt: null },
        { endAt: null, beginAt: null },
        { endAt: null, beginAt: null },
      ],
      specialBusinessHours: [],
      phone: "02-1234567890",
      totalSpaces: 1,
      availableSpaces: 1,
      images: { covers: [""], maps: [""] },
      isAvailable: true,
      beginAt: "2020-08-01T00:00:00.000Z",
      endAt: "2030-12-20T09:16:32.060Z",
      layerId: 50,
      openType: "etag",
      state: {
        key: "closed",
        reservable: false,
        returnable: true,
        isDefault: null,
      },
      scooters: [
        {
          id: "SAM-0005",
          groupId: "0",
          batPercent: 50,
          userBatSwap: false,
          availableDistance: 22.5,
          parkingSpaceId: "",
          alteredBatPercent: 50,
          alteredDistance: 22.5,
        },
      ],
      name: "i18nName zhtw2",
      description: "i18nDescription zhtw",
      address: "i18nAddress zhtw",
    },
    {
      id: "93",
      lat: 23.7124949,
      lng: 120.54279,
      nickname: "斗六火車站",
      businessHours: "24H",
      regularBusinessHours: [
        { endAt: "23:59+0800", beginAt: "00:00+0800" },
        { endAt: "23:59+0800", beginAt: "00:00+0800" },
        { endAt: "23:59+0800", beginAt: "00:00+0800" },
        { endAt: "23:59+0800", beginAt: "00:00+0800" },
        { endAt: "23:59+0800", beginAt: "00:00+0800" },
        { endAt: "23:59+0800", beginAt: "00:00+0800" },
        { endAt: "23:59+0800", beginAt: "00:00+0800" },
      ],
      specialBusinessHours: [],
      phone: "0800",
      totalSpaces: 4,
      availableSpaces: 5,
      images: {
        maps: [
          "https://storage.googleapis.com/wemo-backend-qat/parkingLotImages/a81e9cd0-549a-11ef-b180-6d4b50c1866b",
        ],
        covers: [
          "https://storage.googleapis.com/wemo-backend-qat/parkingLotImages/a82860d0-549a-11ef-b180-6d4b50c1866b",
        ],
      },
      isAvailable: true,
      beginAt: "2024-08-07T08:54:08.412Z",
      endAt: null,
      layerId: 283,
      openType: "automatic",
      state: {
        key: "normal",
        reservable: true,
        returnable: true,
        isDefault: true,
      },
      scooters: [
        {
          id: "AEA-3017",
          groupId: "0",
          batPercent: 100,
          userBatSwap: false,
          availableDistance: 45,
          parkingSpaceId: "4",
          alteredBatPercent: 120,
          alteredDistance: 54,
        },
      ],
      name: "WeMo Scooter 斗六火車站",
      description: "車牌辨識",
      address: "雲林縣斗六市民生路243號",
    },
  ],
};

```


- [ ] 這個要處理嗎? -> next stage
GET /v24/users/me/scooterStyles HTTP/1.1
```js fold

Host: kottos-qat.wemoscooter.com
Accept: _/_
Accept-Encoding: gzip, deflate
If-None-Match: W/"49d6-+Lnv4qoQrzC0jdfucvqRTzjG1oQ"
User-Agent: WeMoScooter/6.4.2QAT (iOS 17.6.1; iPhone13)
Accept-Language: zh-tw
Authorization: WeMo eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIyMDEyMTIiLCJ1c2VyX2lkIjoiMjAxMjEyIiwidmVyc2lvbiI6IjIuMCIsImlhdCI6MTcyNTk1MTc1OSwiZXhwIjoxNzI2MzgzNzU5fQ.tpbf7-H05jkqR6cKyrjwnfIpanO4XKVL0Ria2IMMjn0
Connection: close

{
  "result": "success",
  "data": [
    {
      "id": "83",
      "pinImage": "https://storage.googleapis.com/wemo-config-qat/scooterStyles/pinImage_1724815940709.png",
      "coverImages": [
        "https://storage.googleapis.com/wemo-config-qat/scooterStyles/coverImage_1724815940709.png"
      ],
      "pinHex": "#ED8898FF"
    },
    {
      "id": "82",
      "pinImage": "https://storage.googleapis.com/wemo-config/scooterStyles/pinImage_1704259677325.png",
      "coverImages": [
        "https://storage.googleapis.com/wemo-config/scooterStyles/coverImage_1704259677325.jpg"
      ],
      "pinHex": "#5BC2E7FF"
    }
  ]
}

```





# 之後整合要小心測試, 因為..
- 本來單體的架構，很多功能unit test 都可以測試 -> 現在透過 core system, 很多功能目前都測試不出來了，需要透過整合測試。
- 紅標車價格 -> core 設定好 -> hermes mock it -> 透過整合才知道對不對
- reserve e2e 目前把 db 都 mock 了..
	- userRole 和 core 的對應 -> 需要用整合去測試 (本來就要了,e2e 就算有打到 db -> db 的資料跟core 的資料對應上才會正確)
	- 本來的邏輯可能就可以透過 e2e 那邊去測試
		- reverse 有 mock db, 但是 list, scooterByid 就沒 mock, 都有測試到了

# Archive

todo
- remove相容性  -> done
- 調整pricingRule   -> clarance -> 等下星期一接新的 proto
	- proto 如果有回傳 pricingRule 的 data 要多回傳i18nName欄位
		- get quote
		- get pricing rule by id
		- 其他有包 pricingRule的 grpc, like get pricing timeline
	- PS 的 code 要改
		- 上面的 API 要多回傳這個欄位
- isBasic and isDefault   -> done
	- isBasic 新版 可以抓 userole table 第一筆
	- isDefault 可能要看一下 userCateogty 目前邏輯，是如何 mapping 到 目前的 role Id
- 處理 userRoleId存入 rentConfig
- red tag 處理   ING
	- get scooters and get scooter by Id 都要處理
	- 可以拿到 scooter group is 20
	- get pricing rule by id
	- for these scooter, 直接overwrite ppp 設定，就用這個價格
	- WeMo 員工 要可以還是免費
- 還沒處理起租的邏輯
	- 會 reuse reserve many logic
	- 確認 傳入的 pricingPlanPriceId 和 已經在 rentConfig 表裡面的 rule 是否 一樣
	- 如果不一樣，使用 input的為準, warn 一下
- get scooters 
	- 還是要做，因為你需要把沒有價格的濾掉
	- 不會調整到 response shape
	- 但是 response 需要 filter 掉
