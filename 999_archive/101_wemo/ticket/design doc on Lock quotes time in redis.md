# doc
- [車輛計價與設定流程 jack](https://docs.google.com/document/d/1Gx0HoOHuwSpBZO1WLK1DVyLCbRVHPMXcnbc6u0GV89A/edit)
- asana: [Asana](https://app.asana.com/0/1206177962100119/1207795409156782/f) for pricing id
- [Core Pricing Migration mechanism on pricingPlans id](https://docs.google.com/document/d/1REPjCxh5c5NViPFmsXswo-Fh_z-CVegMs-Y0fQO7zsY/edit)


最後成果
see src:

/Users/re4388/project/work/hermes/infra/stores/LockedQuoteInfo.ts
at branch: edu/core_module_for_pricing
```ts fold


type LockedInfoState = 'Effective' | 'CloseToExpired'

interface LockedInfo {
  roleIds: string[]
  queryTime: string  // ISO8601 string
}

export type LockedInfoReturn = Pick<LockedInfo, 'roleIds' | 'queryTime'> & { state: LockedInfoState }

export class LockedQuoteInfo {
  private readonly TAG = 'infra/stores/LockedQuoteInfo'
  private readonly PREFIX = 'hermes:LockedQuoteInfo'
  protected readonly userId: string

  protected TTL_IN_SEC = 60 * 6 // 6 min
  protected BUFFER_IN_SEC = 60 // 1 min

  constructor(userId: string) {
    this.userId = userId
  }

  /**
   * LockedInfoReturn has 3 cases
   * 1. state: 'Effective'
   * 2. state: 'CloseToExpired'
   * 3. undefined (means when cache is not existed, aka: not set before or expired or cache data is invalid)
   */
  public async getById(userId: string): Promise<LockedInfoReturn | undefined> {
    const tag = this.TAG + '/getById'
    logger.debug({ tag, userId })

    try {
      const lockedInfo = await client.get(this.getKey())

      // case1: lockedInfo is not existed
      if (R.isNil(lockedInfo)) {
        logger.debug({ tag, msg: 'lockedInfo is not existed in redis', userId, payload: JSON.stringify(lockedInfo) })
        return undefined
      }

      // case2: lockedInfo is invalid, shall not happen!
      const lockedDataInCache = JSON.parse(lockedInfo) as LockedInfo
      if (R.isNil(lockedDataInCache.queryTime) || R.isNil(lockedDataInCache.roleIds)) {
        logger.debug({ tag, msg: `lockInfo is invalid, shall not happen. userId: ${userId}. lockedDataInCache: ${JSON.stringify(lockedDataInCache)}` })
        return undefined
      }

      // case3: lockedInfo is close to expire
      if (moment().utc().diff(moment(lockedDataInCache.queryTime), 'seconds') > this.TTL_IN_SEC - this.BUFFER_IN_SEC) {
        logger.debug({ tag, msg: `lockInfo is CloseToExpired. userId: ${userId}. lockedDataInCache: ${JSON.stringify(lockedDataInCache)}` })
        return {
          roleIds: lockedDataInCache.roleIds,
          queryTime: lockedDataInCache.queryTime,
          state: 'CloseToExpired'
        }
      }

      // case4: lockedInfo is not close to expire, return it
      logger.debug({ tag, msg: `lockInfo is Effective. userId: ${userId}. lockedDataInCache: ${JSON.stringify(lockedDataInCache)}` })

      return {
        roleIds: lockedDataInCache.roleIds,
        queryTime: lockedDataInCache.queryTime,
        state: 'Effective'
      }
    } catch (error) {
      logger.error({ error, tag, userId })

      throw new Error(CustomErrorsV2.REDIS_GET_DATA_FAILURE.type)
    }
  }

  public async setById(userId: string, roleIds: string[], queryTimeInISOString: string) {
    const tag = this.TAG + '/setById'
    logger.debug({ tag, msg: 'setById', userId, roleIds })

    const lockedData: LockedInfo = {
      roleIds,
      queryTime: queryTimeInISOString
    }

    try {
      return await client.set(this.getKey(), JSON.stringify(lockedData), 'EX', this.TTL_IN_SEC)
    } catch (error) {
      logger.error({ error, tag, userId })
      throw new Error(CustomErrorsV2.REDIS_SET_DATA_FAILURE.type)
    }
  }

  private getKey(): string {
    return `${this.PREFIX}:${this.userId}`
  }
}



```



```note

我最近的一個任務中，需要設計一個價格快取機制和將此機制整合入現有的租車顯示價格的功能。
這個快取機制需要達到以下要求
使用者畫面上會有很多車子，撈取每一台車子的價格不需要每次都從系統資料庫拿取。
使用者看車子的價格，租車流程等每個環節的 API 的價格需要一致
撈車子價格的調整需要整合到 legacy code, 這邊需要確保程式邏輯維持一樣。
..等等

這個過程中，最讓我挫折感的事件是需求不斷變動，因此我重新設計快取機制非常多次，也包括對現在機制不理解不夠，因此整個設計需要不斷調整，被 review reject, 變動，至少有5次以上。

```



# meeting min 2024_09_12-10_50_53

key is user ID
val is string
roleId and queryTime
ttl is 10 min

這邊你會 lock queryTime
rentFlow 先寫死

herme -> VS
export interface {  
 vehicleIds: string[];  
 rentFlow: number;  
 queryTime: string;  
}

這邊只要你 lock queryTime, 你拿到的 pricing ID 應該都會一樣, 因為 VS 的設計是有歷程的

hermes -> PS
export interface GetPricingRulesAtQuoteRequest {  
 pricingIds: string[];  
 roleIds: number[];  
 ISOStringAtQuote: string;  
}

一樣的 queryTime, 一樣的 roleIds -> 你會拿到一樣的時間

- [ ] scooter 需要 get vehicle id
- [ ] 查那個 range 可用的 車子

5 分鐘就需要更新囉, need to go inside to check
ttl set 10 min -> 多一個時間來當 log

**這張卡的重點是 lock price, 不是 cache**
另外如果你目前的 time 是裡面寫死，變成只能是 now, 但是如果我想要查的不是 now 怎半?

**要 lock 的是，從看 scooters, scooter by id 到使用者按下預約的價格**
看 scooter, scooter by id ->
把 pricingId and pricingRule 全部資訊都 存入 redis

lockQueryTime:{userid}:{accessToken} TTL 30 min

todo

- [x] add grpc in core module
- [ ] get scooters and get scooter by id, get parkingLot and get parkingLot by id??

  - [x] 找哪裡要改? line?
  - [ ] 這邊會呼叫 core module VS and PS and cache pricingResult in redis (prcing id and pricing rule ids)
  - [ ] 使用者按下預約 -> 從 cache 拿，跟 app 傳來的 prcingRuleId 核對，然後存入 rentConfig
    - [ ] 預約那邊的 code 要改, 這邊要存入 rentConfig, 另外要核對 使用者選擇方案後是否和 cache 得邏輯的一樣
  - [ ] 使用者租車和計算價格
    - [ ] 租車那邊的 code 要改

- 需要改 new api
  - 可以 reuse 的部份盡量 reuse
- 目前規劃大概會改在 `export async function getPricing(`
  - 裡面

```ts
  const role = getRole(pricingPlansByUserCategory )
  pricing = coreMOduleGetPricing

```

---




2024_09_06-11_36_45

重點在於...
rediskey -> hash

hash 結構
{
QAT_001: {}
QAT_002: {}

}

![[IMG-design doc on Lock quotes time in redis-20250810201310953.png]]

# note 2024_09_04-13_49_00 on reids

goal

- 在地圖頁滑看到的價格，點車子看到的價格，和預約，租起來看到的價格，和最後計價的價格，都需要是一樣的

地圖頁上滑
get scooters/

需要 cache query time, key is queryTime
大概 cache 2 min
這是每一台車子的 priceId 和 pricing

一台車要有 pricing and id 才可以賣
目前沒有 pricing, 不需要處理
但還是需要有 ping, style

style 記錄在 TagAction
因此你還是需要跟 VS 拿 styleId

因此你還是需要 cache 這個 Tag 資訊
key is queryTime

點車子 get scooter/id
cache key is queryTime + scooterId
cache, tag VS 的必要結果 -> tag 還會有其他地方會用到

然後用這個 cache 去拿 PS to get pid
cache key is queryTime + priceId

你的 cache 機制要確保在地圖頁滑看到的價格，點車子看到的價格是一樣的
讓 滑地圖頁的 queryTime 的那個 cache 跟 點進去看的車子的 querytime
然後如果失效了，就也要失效

==

- getScootersStyleAndPrice function
  - 需要 cache
  - cache 內容會有 all scooter 的 styleId and pricingId -> 兩分鐘都要同樣的結果
- getScooterStyleAndPriceById(scooterId)
  - 也需要 cache
  - cache 內容會有 scooter 的 styleId and pricingId
- 使用者操作上，會先呼叫 getScootersStyleAndPrice, 然後會呼叫 getScooterStyleAndPriceById, 需要確保當我使用第一個拿到的結果，和第二個拿到的結果，需要一致。譬如，如果第一個快取失效拿到新的 value, 第二個的結果也是一樣。
- 使用 redis cache
- 推薦適合的 redis cache 資料結構
- 建議用一個 redis key ，還是兩個 redis key 管理?

redis 要如何達到上面的機制?

是否可以有辦法一個 cache key 但是同時達到上面的機制?

==



# release plan

先確定這樣前端可以處理
類似目前我是傳 "123"回去，但是前端可以吃 123old
然後前端也要這樣回給我

- [ ] 需要跟前端 sync 此作法
	- [ ] 我們return "123_old" 前端可以吃這個 response 而不會壞, 前端也要可以pass request with pricingPlans 的 id 是 "123_old"?

1 is oldVer, 2 is newVer and 3 is bothVer
pricingReleaseToggle: 1,2,3

```ts
    "pricing": {
      "pricingPlans": [
        {
          "id": "571_old",  // <=== 這裡 postfix 加上  "_old"
          "startPrice": 18,
          "freeMinutes": 6,
          "pricePerMinute": 3,
          "name": "一般用戶",
          "isDefault": true,
          "isBasic": true
        }
      ],
```


step
1. 先 release 一版, pricingPlans id 回 "123_old"
2. 過一個禮拜，等到大部分的 user 都開始使用 123_old
3. release 有新邏輯的版本, 這個版本 "123" -> 走 core module 邏輯, 123_old -> 舊 pricing 邏輯
4. 過一段時間，最後 只會剩下 123 -> 走新的邏輯。這個時候可以拆舊的邏輯
   ![[IMG-design doc on Lock quotes time in redis-20250810201312963.png]]


# lesson learn

## redis store 分層

store is store 裡面不可以跟 biz logic 有關連
像是我本來的想法就會綁在一起
因為我還在裡面去進行商業判斷，然後想要一次在 scooter and rent 裡面呼叫一次

josh suggest 要切三層
第一層是 store
這邊就是簡單的 get and set
不要 get 裡面又 set
set 裡面又 get

第二層就是使用cache的商業邏輯
呼叫第一層
邏輯會是
如果 get 不到，然後告訴我 state
然後依照 state 去判斷要不要 set

第三層是 API 層
呼叫第二層
這邊**跟 API有關的判斷**才可以放在這裡
類似 reserve 如果發現快到期，不需要回補。但是如果 scooters 發現快到期就要需要回補了


## logger 用法

debug 用在 RD debug
info, 用在系統資訊，或是一些特定的資訊提供
warn, 不應該發生但是發生，會想要告警的事件
error, when throw error



# cache impl progress and design note



## josh meeting min

- [x] ask josh
- 一個問題請教~~
- 本來的想像是：hermes 打 VS 拿 tag 回來的，放到 cache1, 然後用 tag 的結果去打 PS 放到 cache2。
- 一般情況下，都應該是 VS 的 cache 先過期，然後才是 PS, 但是如果因為時間差+一些地方 hang 導致 race condition 之類的，讓 VS 過期了，但是 PS 還沒過期，這樣 VS cache miss 去重拿，打 PS 還是吃快取，這樣拿到的資料就可能是過期的(或是說跟資料面的狀態不一致)，這樣可以接受嗎？
- 還是說，ttl 大概或設定 2~5 分鐘，這個資料過期的時間因為 ttl 的關係，本來就是一個可以接受的情況?
- 還是說，可以設計成，之前提到過的，直接 cache VS+PS 的結果？你之前提到， VS 的 tag 結果還有其他用途，建議也可以 cache，因為可以用在其他用例。但是目前沒有，是否可以等到有具體的用例，再建立 VS tag 的快取? 進一步想，或許其他用例也是跟這個一樣，要快取的只是多個微服務的整合結果，而不是 VS tag 本身的 cache?
- 目前如果 cache VS + PS 的結果，cache hit 是兩個 micro svc 都不用打, cache miss 就是兩邊都要重撈，感覺這樣管理起來還更簡單?

如果 cache 用下面的方式做，上面就算用 cache 1 and cache2 好像也就沒有這個問題了

- [x] 另一題
- 另外，你說的 queryTime，如果你 cache 10 分鐘，但是如果把動態的 queryTime 放 key, 也基本上也就是沒 cache 了。所以如果要設計 cache 5 min, 就設計 ttl = 10 min 就好了?
- 請教一下，如果要設計 10 分鐘內過來都是用同一個 快取, ttl 設定 10 分鐘應該就做完了，好像也不用把 queryTime 放到 key 裡面吧? 反而把 queryTime 放到 key 裡面， like date.now(), 這樣反而都無法 cache hit。
- 還是說需要某種寫法，類似 moment.min(), 可以把每一分鐘都是一樣的值，但是每 3 分鐘，每 10 分鐘，可能就要寫個 util 來處理了。
- [[cache_queryTime.excalidraw]]

==

- [ ] 想一下這樣設計有什麼問題
- [ ] discuss w/ Josh

goal:

-

- is this a cache about not need to call VS and PS? or the cache for the return of **GET /api/v21/scooters/:scooterId**
- what key and what content
  - key is `queryTime + hash(scooterIds.sort())`
  - content the final result
- cache TTL?
  - per min? per 3 min? per 5 min?
  - I think 3 min is fine, 5 min is a little longer to wait for pricing refresh, and 1 min is too little to cache stuff
- 要拿 scooter 標價的 那個 API
- 這個時間點下的撈回來的東西，都快取起來，不需要另外去 PS and VS 拿
- what data strcutrue
  - string or hash(有必要?)


## redis poc

isRentable
reids

- key: `hermes:scooters:styleAndPrice:${vehicle_id}`
- hash: `{ styleId: "2", pricingId: "3"}`
- ttl: 2 min

/scooters

- 8000 多筆，甚至以後 1~2 萬台車，對 redis 來說應該還好(?)，每一筆存的資料量也不大。
- 用 pipeline 一個 round trip to reids 拿到 all scooter keys 的全部 result
- 撈出可以租借的車子，去 VS 拿 tag -> get priceId and styleId
  - key: `hermes:scooters:GetPricingAndRentalInfo:${vehicle_id}`
  - hash: `{ isRentable: "2", pricingId: "3"}`
- 可以用 priceId 去跟 PS 拿到 pricing 詳細資訊
  - key:`hermes:scooters:GetPricingRulesAtQuote:${vehicle_id}`
  - hash:

/scooter/:id

- 去 VS 拿 tag -> get pricingId and styleId
- 用 priceId 去跟 PS 拿到 pricing 詳細資訊
- 因為 key by scooter 切, 因此使用 key 和更新 key 都很方便

---

use 上面的 key 去撈

- 共用同一個資料結構, 最適合的大概就是 hash
- all scooter 是一個 cache？還是一個 vehicleId 是一個 cache/key
- 全部是一個 key
  - 這一包的資料比較大，8000 台，~ 1x mb 資料量(style+priceId)來回
  - 但是 scooter/:id 要用的話，也要拿一整包回來，再去找
- 一個 vehicleId 是一個 key
  - 傳過去比較有彈性，可以用 pipeline, 一個 roundTrip，或是多個 roundtrip (batch/chunk)
  - scooter/:id 要用很簡單

== 下面先不用想
預約 post rent
租車 patch rent (type=3?)
還車 patch rent (type=4)

![[IMG-design doc on Lock quotes time in redis-20250810201314682.png]]




# redis related study



## 全部 scooter 放在一個 key

### 推薦的 Redis 資料結構

使用 Redis 的 Hash 資料結構來存儲所有 scooter 的 styleId 和 pricingId。這樣可以方便地存取和更新每個 scooter 的數據。

### Redis Key 設計

使用一個 Redis key 來存儲所有 scooter 的數據，例如 `scooters:styleAndPrice`。
這個 Hash 結構的每個 field 可以是 scooter 的 ID，對應的 value 可以是一個 JSON 字符串，包含 styleId 和 pricingId。

### 具體實現

#### 1. `getScootersStyleAndPrice` 函數

這個函數會從 Redis 中獲取所有 scooter 的 styleId 和 pricingId。如果快取不存在或已過期，則從數據庫中獲取數據並更新 Redis 快取。

```javascript

const redis = require('redis');
const client = redis.createClient();
const { promisify } = require('util');
const getAsync = promisify(client.hgetall).bind(client);
const setAsync = promisify(client.hmset).bind(client);
const expireAsync = promisify(client.expire).bind(client);




async function getScootersStyleAndPrice() {
	const cacheKey = 'scooters:styleAndPrice';
	let scootersData = await getAsync(cacheKey);
	if (!scootersData) {
		// 從數據庫中獲取所有 scooter 的 styleId 和 pricingId
		scootersData = await fetchScootersFromDatabase();
		// 將數據存儲到 Redis 中
		await setAsync(cacheKey, scootersData);
		await expireAsync(cacheKey, 120); // 設置兩分鐘的過期時間
	}
	return scootersData;
}

async function fetchScootersFromDatabase() {
	// 模擬從數據庫中獲取數據
	const scooters = {
		'scooter1': JSON.stringify({ styleId: 'style1', pricingId: 'price1' }),
		'scooter2': JSON.stringify({ styleId: 'style2', pricingId: 'price2' }),
		// 添加更多 scooter 數據
	};
	return scooters;
}

```

#### 2. `getScooterStyleAndPriceById` 函數

這個函數會從 Redis 中獲取特定 scooter 的 styleId 和 pricingId。
如果快取不存在或已過期，則從數據庫中獲取數據並更新 Redis 快取。

```javascript

const hgetAsync = promisify(client.hget).bind(client);

async function getScooterStyleAndPriceById(scooterId) {
	const cacheKey = 'scooters:styleAndPrice';
	let scooterData = await hgetAsync(cacheKey, scooterId);

	if (!scooterData) {
		// 從數據庫中獲取特定 scooter 的 styleId 和 pricingId
		const allScootersData = await fetchScootersFromDatabase();
		// 將數據存儲到 Redis 中
		await setAsync(cacheKey, allScootersData);
		await expireAsync(cacheKey, 120); // 設置兩分鐘的過期時間
		scooterData = allScootersData[scooterId];
	}

	return JSON.parse(scooterData);

}

```

### 總結

- 使用一個 Redis key (`scooters:styleAndPrice`) 來存儲所有 scooter 的 styleId 和 pricingId。
- 使用 Redis 的 Hash 資料結構來存儲每個 scooter 的數據。
- 確保在快取失效時，所有數據都會被更新，從而保持一致性。
  這樣的設計可以確保當 `getScootersStyleAndPrice` 和 `getScooterStyleAndPriceById` 被調用時，數據是一致的，並且簡化了快取的管理。

---



## 一次性將 8000 筆資料寫入 Redis 是可以實現的

一次性將 8000 筆資料寫入 Redis 是可以實現的，但需要考慮性能和效率問題。Redis 本身能夠處理大量的數據，但一次性寫入大量數據可能會導致短暫的性能下降。為了提高效率，可以使用批量操作來減少網絡延遲和提高寫入速度。

### 使用 Redis 的批量操作

Redis 提供了多種批量操作方法，例如`pipeline`和`transaction`。這些方法可以將多個命令打包成一個請求，從而減少網絡延遲和提高效率。

以下是使用 Node.js 和`ioredis`庫來實現批量寫入的示例：

#### 安裝 ioredis

首先，確保你已經安裝了`ioredis`庫：  `npm install ioredis`

#### 批量寫入示例

```javascript fold
const Redis = require("ioredis");
const redis = new Redis();




async function batchSetScooters(scooters) {
  const pipeline = redis.pipeline();

  for (const [scooterId, data] of Object.entries(scooters)) {
    pipeline.hset("scooters:styleAndPrice", scooterId, JSON.stringify(data));
  }

  await pipeline.exec();

  await redis.expire("scooters:styleAndPrice", 120); // 設置兩分鐘的過期時間
}

async function main() {
  // 模擬8000筆scooter數據

  const scooters = {};

  for (let i = 1; i <= 8000; i++) {
    scooters[`scooter${i}`] = { styleId: `style${i}`, pricingId: `price${i}` };
  }

  await batchSetScooters(scooters);

  console.log("Batch set completed");
}

main().catch(console.error);


```

### 解釋

1. **Pipeline**：使用`pipeline`來批量執行多個 Redis 命令。這樣可以將多個命令打包成一個請求，從而減少網絡延遲。
2. **HSET**：使用`hset`命令將每個 scooter 的數據寫入 Redis 的 Hash 結構中。
3. **EXPIRE**：設置過期時間，確保數據在兩分鐘後過期。

### 注意事項

1. **性能**：雖然 Redis 能夠處理大量數據，但一次性寫入大量數據可能會導致短暫的性能下降。可以根據實際情況調整批量操作的大小。
2. **錯誤處理**：在實際應用中，應該添加錯誤處理邏輯，以應對可能的網絡問題或 Redis 服務器故障。
3. **資源限制**：確保 Redis 服務器有足夠的資源來處理大量數據寫入，並監控 Redis 的性能指標。

通過使用批量操作，可以有效地將 8000 筆數據一次性寫入 Redis，從而提高效率並減少網絡延遲。

---


## 思考是否有需要用 multi/tx

https://stackoverflow.com/questions/29327544/pipelining-vs-transaction-in-redis

- pipeline 就是 netowrk 優化而已，1 個 command 一個 roundtrip -> 變成多個 command 多的 round trip
- reids 本來就是 single thread, so each command is atomic
- 但是多個 command 不保證
- tx 可以確保多個 command atomic
- [ ] 如果有開 10 個 pod, 10 個 redis client, 這 10 個彼此間的 command 會如何互相干擾? 會 tx 會有什麼影響?


---

## how many keys I can put into redis usually? 1000? 10000?

The number of keys you can store in Redis is primarily limited by the available memory on your server. Redis can handle millions of keys efficiently, but the exact number depends on several factors:

1. Memory Available: The total amount of RAM on your server.
2. Key and Value Size: The size of each key and value pair.
   Redis Configuration: Settings like maxmemory and eviction policies.
   Data Structure Overhead: Different data structures (strings, lists, sets, etc.) have different memory overheads.
   For example, if you have a server with 16GB of RAM and each key-value pair takes up 1KB, you could theoretically store around 16 million key-value pairs. However, this is a simplified calculation and actual results may vary based on the factors mentioned above.
   In practice, Redis can handle:
   Thousands of keys with ease.
   Tens of thousands to millions of keys, depending on the memory and configuration.

結論：看你 redis 的 mem 大小


---



## 將 `scooterId` 設為 Redis key 的一部分

將 `scooterId` 設為 Redis key 的一部分，確實是一種可行的設計，特別是在需要頻繁查詢單個 scooter 的情況下。這樣的設計可以使得 `getScooterStyleAndPriceById` 函數更加高效，因為它只需要查詢特定的 key，而不需要遍歷或解析整個 Hash 結構。

### 設計比較

#### 原本的設計

- **優點**：
- 所有 scooter 的數據集中存儲在一個 Hash 結構中，便於管理和更新。
- 適合一次性獲取所有 scooter 的數據。

- **缺點**：
- 當數據量很大時，單次操作可能會比較耗時。
- 查詢單個 scooter 的數據需要從整個 Hash 結構中提取，效率較低。

#### 新的設計

- **優點**：
- 查詢單個 scooter 的數據非常高效，只需查詢特定的 key。
- 更新單個 scooter 的數據也很方便，不需要更新整個 Hash 結構。

- **缺點**：
- 需要管理大量的 Redis key，可能會增加 Redis 的內存開銷。
- 如果需要一次性獲取所有 scooter 的數據，可能需要多次查詢。

### 新設計的實現

#### 1. `getScootersStyleAndPrice` 函數

這個函數會從 Redis 中獲取所有 scooter 的 styleId 和 pricingId。
如果快取不存在或已過期，則從數據庫中獲取數據並更新 Redis 快取。

```javascript fold


const Redis = require("ioredis");
const redis = new Redis();

async function getScootersStyleAndPrice() {
  const cacheKeyPrefix = "scooters:styleAndPrice:";

  const scooterIds = await getRentableScooterIds();

  const pipeline = redis.pipeline();
  for (const scooterId of scooterIds) {
    pipeline.hgetall(`${cacheKeyPrefix}${scooterId}`);
  }
  const results = await pipeline.exec();

  let scootersData = {};

  for (let i = 0; i < scooterIds.length; i++) {
    const scooterId = scooterIds[i];
    const result = results[i][1];

    if (result && Object.keys(result).length > 0) {
      scootersData[scooterId] = result;
    } else {
      // 如果快取中沒有數據，從數據庫中獲取
      const data = await fetchScooterFromVSandPS(scooterId);

      scootersData[scooterId] = data;
      await redis.hmset(
        `${cacheKeyPrefix}${scooterId}`,
        "styleId",
        data.styleId,
        "pricingId",
        data.pricingId
      );
      await redis.expire(`${cacheKeyPrefix}${scooterId}`, 120);
    }
  }

  return scootersData;
}

async function fetchScooterFromVSandPS(scooterId) {
  // 模擬從數據庫中獲取數據
  return { styleId: `1`, pricingId: `2` };
}

async function getRentableScooterIds() {
  // 模擬獲取所有的scooterId
  return ["1", "2", "3", "4", "5"]; // 示例數據
}

/////////

// `getScooterStyleAndPriceById` 函數
// 這個函數會從 Redis 中獲取特定 scooter 的 styleId 和 pricingId。如果快取不存在或已過期，則從數據庫中獲取數據並更新 Redis 快取。
async function getScooterStyleAndPriceById(scooterId) {
  const cacheKey = `scooters:styleAndPrice:${scooterId}`;

  let scooterData = await redis.hgetall(cacheKey);

  if (!scooterData || Object.keys(scooterData).length === 0) {
    // 從數據庫中獲取特定 scooter 的 styleId 和 pricingId
    scooterData = await fetchScooterFromVSandPS(scooterId);

    // 將數據存儲到 Redis 中
    await redis.hmset(
      cacheKey,
      "styleId",
      scooterData.styleId,
      "pricingId",
      scooterData.pricingId
    );
    await redis.expire(cacheKey, 120); // 設置兩分鐘的過期時間
  }

  return scooterData;
}


```

#### 2.

### 總結

- **新設計**：將 `scooterId` 設為 Redis key 的一部分，這樣可以提高單個 scooter 查詢和更新的效率。

- **一致性**：確保在 `getScootersStyleAndPrice` 和 `getScooterStyleAndPriceById` 中使用相同的邏輯來更新快取，從而保持數據的一致性。

- **性能**：新設計在查詢和更新單個 scooter 時性能更高，但需要管理更多的 Redis key。

根據你的需求和使用場景，選擇適合的設計方案。如果頻繁查詢和更新單個 scooter 的數據，新設計會更合適。如果需要一次性獲取所有 scooter 的數據，原設計可能更方便。

---




# meeting min and discuss history

### 停機思考

早上想了一下
好像如果可以停機就不需要 old and new version

如果關機再開

新的BE code 是 新的

那一開始我給的 pppId 也是 pricingRule
那 app給我的 pppId也會是 pricingRule

那就是應該要做 fallback就好
就是如果新的有問題，就開舊的
風險就是，如果新的有問題...
那我們要服務要停機直到修到好



===

PS: 如果沒有停機，那會出現預約是 ppp, 租車是 pricingRule, 這樣你會需要去回補 預約時應該做的事情，就是回補 rentConfig



===

- 確定要停機? 風險?
- userRoleId 要從哪裡拿? 因為我要存到 rentConfig
- name: "一般用戶", and isDefault: true, and isBasic: true, 要如何處理
- 哪裡會需要做 mapping表?



目前思路:
- 先上一版, 加上 `_old`
- 那我們就先需要出一個版本，加上 `_old` -> 就是 pricingPlanPriceId過來
- 這樣，只要前端給我沒有帶上 `_old` 就是 pricingRule


===
