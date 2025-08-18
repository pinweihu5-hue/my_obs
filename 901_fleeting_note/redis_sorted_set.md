[[redis_idx]]


有序集合允許你根據成員的分數（在這種情況下是時間戳）進行排序和篩選，這使得它非常適合存儲和檢索基於時間的數據。


### 為什麼選擇有序集合（Sorted Set）
- **自動排序**：有序集合中的元素根據分數自動排序。
- **範圍查詢**：可以根據分數範圍進行查詢，這非常適合檢索特定分數範圍內的數據。
- **高效性**：Redis 的有序集合在插入和查詢操作上都非常高效。


使用情境: when collection 裡面的 element, 本質上是惟一且有序的)
- 任何資料，for **sorted range-based queries**
- 計分板
	- online player scoreboard, or Leaderboards
	- a data structure what have the set property
	- and each element have a score, and you can query based on the score
- 時間序列分析 
	- the score is the timestamped, so you can get specific time-rage, remove old data
- 推薦商品排序 
	- product recommendation: the score represent some kind of recommend relevant metric
- 推薦店家排序 
	- geo-spatial data: if you can map longitude and latitude to a score, then you can have query like finding what is the neareast place from my location






# example



## CLI


```bash


# 語法  zadd key 分數1 成員1 分數2 成員2 分數3 成員3
#ZADD key score1 member1 [score2 member2 ...]


127.0.0.1:6379> ZADD ss1 999 ben
(integer) 1
127.0.0.1:6379> ZADD ss1 999 john
(integer) 1
127.0.0.1:6379> ZADD ss1 12 jack
(integer) 1


# 語法去撈分數範圍的資料
# ZRANGE key start stop WITHSCORES (optional)
# ZRANGE key 開始分數(int) 結束分數(int) <WITHSCORES>

# start and stop: The indices defining the range of elements to retrieve. 
# They can be positive integers, negative integers, or special values like -inf and +inf.

# 撈出 ss1 這個key 的 zset 資料，從 0 到最後一個 (-1), 包括分數
127.0.0.1:6379> Zrange ss1 0 -1 withscores
1) "jack"
2) "12"
3) "ben"
4) "999"
5) "john"
6) "999"




# 存事件的時間，時間序列資料，把 timestamp 作為 score
ZADD user:1234:timeline TIMESTAMP event_data


# add event
ZADD user:A:timeline 1644028800 "User A posted a photo"
ZADD user:A:timeline 1644032400 "User A shared a status"

# 拿所有的時間，用時間序排
# get all event in Chronological Order
ZRANGE user:A:timeline 0 -1 WITHSCORES

```


## 找過去10分鐘的數據

#### 1. 安裝 `ioredis`

首先，確保你已經安裝了 `ioredis`：

```sh
npm install ioredis
```

#### 2. 編寫 TypeScript 代碼

創建一個 `index.ts` 文件，並將以下代碼放入其中：

```ts hl:16,29 fold
import Redis from 'ioredis';

const client = new Redis();

interface DataElement {
  timestamp: number;
  value: string;
}

// 添加數據到有序集合
async function addDataToSortedSet(setName: string, value: string): Promise<void> {
  const timestamp = Date.now();
  try {


	const reply = await client.zadd(setName, timestamp, JSON.stringify({ timestamp, value }));

    console.log('Data added to sorted set:', reply);
  } catch (err) {
    console.error('Error adding data to sorted set:', err);
  }
}

// 獲取過去10分鐘的數據
async function getDataFromLast10Minutes(setName: string): Promise<DataElement[]> {
  const tenMinutesAgo = Date.now() - 10 * 60 * 1000;
  try {
  
    const elements = await client.zrangebyscore(setName, tenMinutesAgo, Date.now());
    
    return elements.map(element => JSON.parse(element) as DataElement);
  } catch (err) {
    console.error('Error fetching data from sorted set:', err);
    throw err;
  }
}

// 使用範例
(async () => {
  // 添加一些數據到有序集合中
  await addDataToSortedSet('mySortedSet', 'exampleValue1');
  await addDataToSortedSet('mySortedSet', 'exampleValue2');
  await addDataToSortedSet('mySortedSet', 'exampleValue3');

  // 獲取過去10分鐘的數據
  try {
    const data = await getDataFromLast10Minutes('mySortedSet');
    console.log('Data from last 10 minutes:', data);
  } catch (err) {
    console.error('Error:', err);
  }
})();
```

### 3. 編譯並運行 TypeScript 代碼

使用以下命令來編譯並運行你的 TypeScript 代碼：

```sh
npx tsc
node dist/index.js
```





# wemo code
一樣把 score 當做 unit ts 來使用，撈一段時間的資料
key 是 情況+boxid  `${situation}:${boxId}`
分數是  `moment().valueOf()` (unit ts)
因為是 log, val 直接放  `String(moment().valueOf())`
```js

// item 206 (blackout)
// 移除 60 min 以前的 log
await store.zremrangebyscore('AccessTimes:ApiBatteries', scooter.boxId, '-inf', sixtyMinBeforeNow)

// get all 剩下的 log
const batteryAccesses = await store.zrangebyscore('AccessTimes:ApiBatteries', scooter.boxId, '-inf', '+inf')



////////////


// 呼叫方式：await store.zadd('AccessTimes:ApiBatteries', resVO.box_id, moment().valueOf())
async function zadd(situation: string, boxId: string, weight: number): Promise<void> {
  try {
    // 語法  zadd key 分數1 成員1 分數2 成員2 分數3 成員3
    await client.zadd(`${situation}:${boxId}`, weight, String(weight))
  } catch (error) {
    logger.error({
            tag: `${Tag}/zadd`,
            req: { situation, boxId, weight },
            error
          })
    throw new Error(customErrorsV2.REDIS_OPERATION_FAILURE.type)
  }
}

```