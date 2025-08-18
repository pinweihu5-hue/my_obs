[[_wemo_tickets]]

---

# related note

- [[PM DOC 註冊登入：新增異常阻擋流程]]
- [[wemo 登入流程]]
- [[blocker 機制 google doc backup]]

---



- [ ] add looger

PhoneChanged4Times
PhoneVerifyResend4timesIn10Min

vid_creator [State of JavaScript 2023: Resources](https://2023.stateofjs.com/en-US/resources/#video_creators)



- [ ] add below test
precondition
{
isBlock is false
counter = 3
}

db is active
-> run
{
isBlock is true
counter = 4
}




- db 已經有封鎖的資料，redis 沒資料
- 不可以產生新的




- [ ] ask josh
	- [ ] mr: should check no active records in db before you write in. Or we can setup prev record to expired, if there has any unexpired records.
	- [ ] phone

- [x] migrate core into class
- [x] write test to confirm it works
- [x] refactor some josh reminder part, like add prefix
	- [x] check MR msg
- [ ] write test to confirm helper work
- [ ] remove previous input and using redis
- [x] update phone -> fillback +886
- [ ] update unblock




- [x] 確認 redis key 是否有 text 限制??
`Redis keys are binary safe, this means that you can use any binary sequence as a key, from a string like "foo" to the content of a JPEG file. The empty string is also a valid key.`
[Naming Convention and Valid Characters for a Redis Key - Stack Overflow](https://stackoverflow.com/questions/30271808/naming-convention-and-valid-characters-for-a-redis-key)


ask josh
- [x] poc 看看 incr 的功效
- 如果兩個 proc coming in , 如果你用 hset incre -> 拿到就直接返回是否超過
	- 可以預防很近的 req ，很多 read 都是一樣的 value, 然後都不會撞到 你的 limit


- use builder pattern
	- make invoke more eleganet
	- higher level, not just user/utils level

- when create a new block reocrd..need to clean up dirty record
	- say another mgmt or whatever reason have a old record which is still active
	- when you create a record, also need to remove the same target (which is still active yet)
	- say manager want to create a record and make it ban forever, and this target already have a active record in db, but not ban forever
		- we shall remove the old-active record and use the new ban forever record



bk
```ts

  await blockResendInTimeWindow({
    blockTarget: normalizePhoneNumber(phone),
    allowRepeatedTime: 3,
    timeWindowInSec: 10 * 60, // 10 分鐘
    blockTimeInSec: 180 * 60, // 180 分鐘
    flow: BlockFlow.registerAndLoginFlow,
    rule: BlockRule.PhoneVerifyResend4timesIn10Min
  })

  await restrictChangeFrequency({
    allowChangingTime: 3,
    TTLinSec: 40 * 60, // 35分鐘 + 5 分鐘 buffer, a little longer than SentryToken TTL
    currentTarget: normalizePhoneNumber(phone),
    previousTarget: normalizePhoneNumber(userSentryInfo.phone ?? ''),
    flow: BlockFlow.registerAndLoginFlow,
    rule: BlockRule.PhoneChanged4Times,
    restrictBy: userSentryInfo.sentryToken
  })

```


# pm

	# todo




## now

- [x] fix MR
- [ ] think about what nfr I add or josh add in this ticket
- [ ] this abt nfr

### later

- [ ] rtry to replicate josh work, test index by myself, check against if chatgpt is correct
  - [ ] see this MR how he test

---

# 阻擋與封鎖名單機制 Study

[codimd company link ](https://note.wemoscooter.com/UNh_tKOFTNS9q1bdQsekKw)

## DB migration

[Apollo - UI](https://www.figma.com/design/8GORW3KqwUKkyB1QrXCMf7/Apollo?node-id=9650-2&t=tjOQqY9iWYZnDv8p-0)
![[IMG-BE - 阻擋與封鎖名單機制 - Plan and Prepare API doc-20241003104933787.png]]

### table name: block_record

extensibility considerationd



| Col name         | data type | comment                                                  | idx |
| ---------------- | --------- | -------------------------------------------------------- | --- |
| id               | bigserial | pk, unique                                               | yes |
| beginAt          | timestamp | when to start block, sort                                | yes |
| endAt            | timestamp | nullable, null means block indefinitely                  | yes |
| updatedAt        | timestamp | auto gen, nullable                                       |     |
| blockManagerId   | varchar   | nullable, managerId to add record to this table          |     |
| unBlockManagerId | varchar   | nullable, managerId to unblock record to this table      |     |
| flow             | varchar   | enum: 註冊登入流程, 修改電話流程, null when manager create           | yes |
| rule             | varchar   | enum: PhoneChanged4Times, PhoneVerifyResend4timesIn10Min | yes |
| blockTarget      | varchar   | ex: 09376765112, myhome@gmail.com, ...etc                | yes |

- key:
  - PhoneVerifyResend4timesIn10Min:{phone},
  - PhoneChanged4Times:{sentryToken}
- a cronjob to remove old record
  - remove if endAt is 6 month old


query 被 blocked 的資料上會比較醜，也會用到我們討厭的 or
where endAt > now or null

- [ ] how abt use +100 yrs?


### db access pattern

**Apollo**

- query all records to show on apollo
  - where
    - condition is PhoneVerifyResend4timesIn10Min
    - currentTime within beginAt and endAt
- manager add phone into block table
  - beginAt = now
  - endAt = null
  - blockTarget = 09376765112
  - blockManagerId = managerId
  - condition = PhoneVerifyResend4timesIn10Min
- manager unblock phone
  - where
    - blockTarget = 09376765112
  - update to:
    - endAt = now
    - updatedAt = now
    - unBlockManagerId = managerId

**userApp**

- on rate limiter2: check DB when redis found key is not exist
  - where
    - blockTarget is 09376765112
    - condition is PhoneVerifyResend4timesIn10Min
    - currentTime within beginAt and endAt
- system add record
  - blockTarget = 09376765112
  - flow = 註冊登入流程..etc
  - condition = PhoneVerifyResend4timesIn10Min, depends
  - beginAt = now
  - endAt = now + 180 min

### index

應對 user app query

複合 idx
CREATE INDEX idx_xxxxxxxxxxxx
ON your_table (blockTarget, condition, beginAt, endAt);

## apollo API

GET /blockList
- get currently blocked data for manager
- manager created if flow is null

POST /blockList/:id
- create a record in block table
- flow is null if the record is created by manager
- also need to create redis record, currently the condition need to choose `PhoneVerifyResend4timesIn10Min`

PATCH /blockList/:id
- unblock a record in block table
  - ednAt update to `now`
- default is to block indefinitely
- remove redis record


most operation can see above 'db access pattern' section for query condition
API doc will be generate when work on this ticket

## overall logic flow

**POST /v23/users/me/phone API**

register flow: `sendAuthSmsForSentry(userSentryInfo: SentryInfo, payload: SendAuthSmsRequest)`

- run rate limiter 1 & 2

login flow: `sendAuthSms: ControllerWeMoWithRequestBody`

- run rate limit 2 only

## rate limit1

- user input "phone number" and then click 傳送驗證碼
- POST /v23/users/me/phone
- under the same sentryToken, check if we have over 3 "changed" -> throw error
  - ABC -> block
    - ""->A, A->B, B->C
  - AAB -> pass
    - ""->A, "A"->B
  - AAA -> one change -> pass
    - "" -> A
- no set phone number into block list, just throw error and force user need to start from begin and acquire the sentry token again
  [註冊流程優化 – FigJam](https://www.figma.com/board/OpzZy9GGki5tsYXIaj5GOw/%E8%A8%BB%E5%86%8A%E6%B5%81%E7%A8%8B%E5%84%AA%E5%8C%96?node-id=2277-24528&t=tgvjzob44YaI8nS8-0)
  ![[IMG-BE - 阻擋與封鎖名單機制 - Plan and Prepare API doc-20241003104948768.png]]

rateLimit(previousPhone, currentPhone, sentryToken )

- redis key: Register:PhoneChanged4TimesInSentryToken:{sentryToken}
- redis value: number
- if key is not exist yet
  - set value = 1 and TTL set to current default sentry token TTL
- if key is exsit
  - if value < 3 and previousPhone not equal to currentPhone -> incrment value
  - if value >=3
    - add to db block list
      - we will not block phone in this case, just also add to table for a record
      - endAt is also currentTime in this case
      - other logic follow the same logic
    - throw error (status 401)

~~logic~~

- ~~get the previous_phoneNumber from sentryInfo~~
- ~~check if current_phoneNumber !== previous_phoneNumber -> if yes, count as one~~
- ~~redis~~
  - ~~key: sentryToken~~
  - ~~value: count(number)~~
- ~~check if the value is more than 3~~

## rate limit2

click “重新發送驗證碼”
check if **the same** phone coming in with 10 min window? -> the 4th time, throw error and block this phone number for 180 min
this is cross sentry token, if the user use different sentry token, still continue count
![[IMG-BE - 阻擋與封鎖名單機制 - Plan and Prepare API doc-20241003105001235.png]]

rateLimit2(phone)

- redis key: PhoneRepated4TimesIn10Min:{phone}
- redis value: string, JSON.stringify(below_object)

```ts
{
	endAt: xxx // can we remove this?, only use isBlocked?
	isBlocked: xxx
	counter: 1
}

```

- if key is not exist
  - check if phone is db is still in block period -> get data, calculate shall be block TTL, update redis isBlocked and update TTL
    - then, if the phone is new one, then we will have a lot of db hit?
  - setup key and counter = 1 and TTL 10 min
- if key is exsit
  - if isBlocked is true -> throw 401
  - if isBlocked is false
    - if counter < 3, counter increment (hinc or redis method)
    - if counter >=3
      - isBlocked -> true
      - entend ttl to 180 min
      - add to db block list
      - throw error (status 401)

~~logic

- ~~check if phone in block table -> throw error~~
- ~~redis~~
  - ~~key: phone~~
  - ~~value: count~~
- ~~if key is not exist, set key and value = 1and TTL 10 min~~
- ~~if key is exist and value <=3 value increment~~
- ~~if key is exist and value >3 -> throw error and add phone into block list~~

## handle different phone format

- we have below 3 phone formats (every8d accept)
  - "886936675118"
  - "0936675118"
  - "+886936675118"
- need to treat them as the same one
- use regex to match below 3 case can convert to `936675118`

## tasks

- create DB table
- setup rate limit core logic util
- use rate limit util in register and login flow
- apollo API (get block list, unblock user, add user into block list)
- cronjob (everyday) to check block table endAt to unblock ppl

# working POC


```ts title:poc_block_helper_test fold

const { readiness: redisInit, client: mockedRedisClient, disconnect: redisDisconnect } = require('../../../../../../infra/stores')
import { BlockRecord } from '../../../../../../infra/entities/BlockRecord'
import { BlockRule, BlockFlow } from '../../../../../../infra/enums/blockRecord'
import { clean, initDB, getRepository } from '../../../../../utils/db'
import moment from 'moment'
import db from '../../../../../../repositories/db'
import * as jestDateMock from 'jest-date-mock'
import { RestrictChangesBlocker } from '../../../../../../api/v2/blockList/service/blocker/RestrictChangesBlocker'
import { BlockHelper } from '../../../../../../api/v2/blockList/service/blocker/BlockHelper'

// jest.mock('ioredis')
// initDB()

// let now: moment.Moment

beforeAll(async () => {
  //   await redisInit()
  //   now = moment().utc()
})

afterAll(async () => {
  //   await redisDisconnect()
})

describe('restrictChangesBlocker', () => {
  const now = moment()

  beforeEach(async () => {
    await clean()
    await mockedRedisClient.flushdb()
    jestDateMock.clear()
    jest.resetAllMocks()
  })

  afterEach(async () => {
    jestDateMock.clear()
  })

  class ResendInTimeWindowBlockerMock {
    constructor(protected db: any, protected redis: any, protected flow: BlockFlow, protected opt: any) {
    }
    run() {
      return Promise.resolve()
    }
  }

  //   const getKey = (blockTarget: string) => `Hermes:${BlockRule.PhoneChanged4Times}:${blockTarget}`

  test('redis no record and no changes detected -> no error, redis have expected data', async () => {
    // run
    const blockTarget = 'dummy_token'
    const phoneNumber = '123456789'
    //  const key = getKey(blockTarget)
    const blockHelper = new BlockHelper(BlockFlow.registerAndLoginFlow)

    blockHelper.addRule(BlockRule.PhoneVerifyResend4timesIn10Min, { blockTarget: '+886936675118' }).addRule(BlockRule.PhoneChanged4Times, {
      blockTarget: 'dummyToken',
      previousInput: '+886936675118',
      currentInput: '+886936675118'
    })

    //@ts-ignore for testing private field
    expect(blockHelper.blockList).
    //  expect(await mockedRedisClient.get(key)).toEqual(null)
  })
})


```


```ts title:blockResendInTimeWindow_use_hset fold

import moment from 'moment'
import { BlockRule, BlockFlow } from '../../../../../infra/enums/blockRecord'
import { client as redis } from '../../../../../infra/stores'
import db from '../../../../../repositories/db'
import logger from 'logger'
import customErrorsV2 from '../../../../../infra/configs/custom-errors-v2'
import R from 'ramda'

interface RateLimitData {
  isBlocked: boolean
  counter: number
}

export interface BlockResendInTimeWindowArg {
  blockTarget: string
  allowRepeatedTime: number
  timeWindowInSec: number
  blockTimeInSec: number
  flow: BlockFlow
  ruleName: BlockRule
}

const tag = 'api/v2/users/utils/blockResendInTimeWindow'

export async function blockResendInTimeWindow(arg: BlockResendInTimeWindowArg) {
  logger.info(tag, arg)
  const { blockTarget, allowRepeatedTime, timeWindowInSec, blockTimeInSec, flow, ruleName } = arg

  const bufferInSec = Math.floor(blockTimeInSec / 10)
  const key = `${ruleName}:${blockTarget}`

  const value = await redis.hgetall(key)
  if (value === null) {
    // check db to ensure data consistency
    const blockedTarget = await db.blockRecord.queryBlockedTarget({ blockTarget, rule: ruleName })

    // if db has blocked record, backfill data to redis
    if (!R.isNil(blockedTarget)) {
      const backFillData = {
        isBlocked: true,
        counter: allowRepeatedTime + 1
      }
      // ban forever when endAt is null
      if (blockedTarget.endAt === null) {
        // await redis.set(key, JSON.stringify(backFillData))
        const trans = redis.multi()
        trans.hset(key, 'isBlocked', String(backFillData.isBlocked))
        trans.hset(key, 'counter', String(backFillData.counter))
        trans.exec()
      } else {
        const endAt = moment(blockedTarget.endAt)
        const now = moment()
        // await redis.set(key, JSON.stringify(backFillData), 'PX', endAt.diff(now))
        const trans = redis.multi()
        trans.hset(key, 'isBlocked', String(backFillData.isBlocked))
        trans.hset(key, 'counter', String(backFillData.counter))
        trans.pexpire(key, endAt.diff(now))
        trans.exec()
      }
      throw new Error(customErrorsV2.BLOCK_RESEND_IN_TIME_WINDOW.type)
    }

    // if record is not in db, target is not blocked, redis and db are consistent, continue
    const data = {
      isBlocked: false,
      counter: 1
    }
    // await redis.set(key, JSON.stringify(data), 'EX', timeWindowInSec)
    const trans = redis.multi()
    trans.hset(key, 'isBlocked', String(data.isBlocked))
    trans.hset(key, 'counter', String(data.counter))
    trans.expire(key, timeWindowInSec)
    trans.exec()
  } else {
    const data = value as unknown as RateLimitData
    if (data.isBlocked) {
      logger.error(tag, data)
      throw new Error(customErrorsV2.BLOCK_RESEND_IN_TIME_WINDOW.type)
    } else {
      const newCounter = await redis.hincrby(key, 'counter', 1)
      if (newCounter < allowRepeatedTime) {
        const counterAdded = {
          isBlocked: false,
          counter: newCounter
        }
        const remainingTTL = await redis.ttl(key)
        // await redis.multi().set(key, JSON.stringify(counterAdded)).expire(key, remainingTTL).exec()

        const trans = redis.multi()
        trans.hset(key, 'isBlocked', String(counterAdded.isBlocked))
        trans.hset(key, 'counter', String(counterAdded.counter))
        trans.expire(key, remainingTTL)
        trans.exec()

        logger.debug(tag, counterAdded)
      } else {
        // 設置為封鎖狀態並延長TTL
        const blockedData = {
          isBlocked: true,
          counter: newCounter
        }
        // await redis.set(key, JSON.stringify(blockedData), 'EX', blockTimeInSec + bufferInSec)
        const trans = redis.multi()
        trans.hset(key, 'isBlocked', String(blockedData.isBlocked))
        trans.hset(key, 'counter', String(blockedData.counter))
        trans.expire(key, blockTimeInSec + bufferInSec)
        trans.exec()

        const now = moment()
        const blockRecord = {
          blockTarget,
          flow,
          rule: ruleName,
          beginAt: now.toDate(),
          endAt: now.clone().add(blockTimeInSec, 'seconds').toDate()
        }
        await db.blockRecord.create(blockRecord)

        logger.error(tag, blockedData, blockRecord)
        throw new Error(customErrorsV2.BLOCK_RESEND_IN_TIME_WINDOW.type)
      }
    }
  }
}


```

```ts title:bk_for_change_req_no_class_code fold

import moment from 'moment'
import { BlockRule, BlockFlow } from '../../../../../infra/enums/blockRecord'
import { client as redis } from '../../../../../infra/stores'
import db from '../../../../../repositories/db'
import logger from 'logger'
import customErrorsV2 from '../../../../../infra/configs/custom-errors-v2'

export interface RestrictChangeFrequencyArg {
  ruleName: BlockRule
  blockTarget: string
  previousInput: string
  currentInput: string
  allowChangingTime: number
  TTLinSec: number
  flow: BlockFlow
}

const tag = 'api/v2/users/utils/restrictChangeFrequency'

export async function restrictChangeFrequency(arg: RestrictChangeFrequencyArg) {
  logger.info(tag, arg)

  const { previousInput, currentInput, allowChangingTime, TTLinSec, flow, ruleName, blockTarget } = arg
  if (previousInput === currentInput) {
    return
  }

  // 確認 redis key 是否有 text 限制??
  const key = `${ruleName}:${blockTarget}`
  const value = await redis.get(key)

  if (value === null) {
    // Key 不存在，設置初始值和 TTL
    await redis.set(key, 1, 'EX', TTLinSec)
  } else {
    const newCount = await redis.incr(key)
    if (newCount > allowChangingTime) {
      const now = moment.utc()
      const blockRecord = {
        blockTarget,
        flow,
        rule: ruleName,
        beginAt: now.toDate(),
        endAt: now.toDate()
      }
      await db.blockRecord.create(blockRecord)
      logger.error(tag, arg, blockRecord)
      throw new Error(customErrorsV2.RESTRICT_CHANGE_FREQUENCY.type)
    }
  }
}


```

```ts title:bk_for_blockResendInTimeWindow_no_class_code fold

import moment from 'moment'
import { BlockRule, BlockFlow } from '../../../../../infra/enums/blockRecord'
import { client as redis } from '../../../../../infra/stores'
import db from '../../../../../repositories/db'
import logger from 'logger'
import customErrorsV2 from '../../../../../infra/configs/custom-errors-v2'
import R from 'ramda'

interface RateLimitData {
  isBlocked: number // 0 means false, 1 means true
  counter: number
}

export interface BlockResendInTimeWindowArg {
  blockTarget: string
  allowRepeatedTime: number
  timeWindowInSec: number
  blockTimeInSec: number
  flow: BlockFlow
  ruleName: BlockRule
}

const tag = 'api/v2/users/utils/blockResendInTimeWindow'

export async function blockResendInTimeWindow(arg: BlockResendInTimeWindowArg) {
  logger.info(tag, arg)
  const { blockTarget, allowRepeatedTime, timeWindowInSec, blockTimeInSec, flow, ruleName } = arg

  async function setData(data: RateLimitData, TTLinSec?: number) {
    const multi = redis.multi().hset(key, 'isBlocked', data.isBlocked).hset(key, 'counter', data.counter)

    if (TTLinSec !== undefined) {
      multi.expire(key, TTLinSec)
    }

    return await multi.exec()
  }

  const bufferInSec = Math.floor(blockTimeInSec / 10)
  const key = `${ruleName}:${blockTarget}`

  // isExist 0 means key does not exist
  const isExist = await redis.hexists(key, 'isBlocked')
  if (!isExist) {
    const blockedTarget = await db.blockRecord.queryBlockedTarget({ blockTarget, rule: ruleName })
    if (!R.isNil(blockedTarget)) {
      const backFillData = {
        isBlocked: 1,
        counter: allowRepeatedTime + 1
      }
      // ban forever when endAt is null
      if (blockedTarget.endAt === null) {
        setData(backFillData)
      } else {
        const endAt = moment(blockedTarget.endAt)
        const now = moment()
        await setData(backFillData, endAt.diff(now) / 1000)
      }
      throw new Error(customErrorsV2.BLOCK_RESEND_IN_TIME_WINDOW.type)
    }

    // if record is not in db, target is not blocked, redis and db are consistent, continue
    const data = {
      isBlocked: 0,
      counter: 1
    }
    await setData(data, timeWindowInSec)
  } else {
    const value = (await redis.hgetall(key)) as unknown as RateLimitData
    // const data = value
    if (Number(value.isBlocked) === 1) {
      logger.error(tag, value)
      throw new Error(customErrorsV2.BLOCK_RESEND_IN_TIME_WINDOW.type)
    } else {
      const newCounter = await redis.hincrby(key, 'counter', 1)
      if (newCounter <= allowRepeatedTime) {
        const counterAdded = {
          isBlocked: 0,
          counter: newCounter
        }
        const remainingTTL = await redis.ttl(key)
        await setData(counterAdded, remainingTTL)
        logger.debug(tag, counterAdded)
      } else {
        // 設置為封鎖狀態並延長TTL
        const blockedData = {
          isBlocked: 1,
          counter: newCounter
        }
        await setData(blockedData, blockTimeInSec + bufferInSec)

        const now = moment()
        const blockRecord = {
          blockTarget,
          flow,
          rule: ruleName,
          beginAt: now.toDate(),
          endAt: now.clone().add(blockTimeInSec, 'seconds').toDate()
        }
        await db.blockRecord.create(blockRecord)

        logger.error(tag, blockedData, blockRecord)
        throw new Error(customErrorsV2.BLOCK_RESEND_IN_TIME_WINDOW.type)
      }
    }
  }
}


```

# meeting
- take db as cache
  - like when pass the rate limiter, also check if this person is not in block list, if he indeed in block list -> need to block it and write to redis
- every redis write action need to follow up a db write
- try to use redis list
  - get all element from the list -> check element unique -> if element > 3 -> throw error
  - list can check how many memebr in a time frame
- what need to align to PM
  - 3 resent in 10 min and then block 180 min, say he sent again 4th, do we need to extend 180 min?
    - the drawback if he keep resent, we like attack our DB, since we need to update DB
  - do we need to blcok idcard for 3 different phone number?
    - but if I use Josh idcard(or anyone) and try 3 different phone number and he got block -> can be use in this unexpected way

## handle different phone format

- we have below 3 phone foramts (every8d accept)
- need to treat them as the same one by covert to the same phone number and use the coverted one as rate limit input
- simple dirty one is to use regex to match below 3 case can convert to `936675118`
  ref: [every8d notion ticekt](https://www.notion.so/nture4388/SMS-integration-and-webhook-every8d-fa51fac9719b4d01915af0d9cf86bc02?pvs=4)
  "886936675118"
  "0936675118"
  "+886936675118"

sth like below
\+?(886|0)(\d+)
$2

## redis data type choose

- set
  - check 3 different phone for the same sentry token session
  - sentryToken as key
  - get the memeber
  - check if more than 3 different phone
- key-value
  - check for the same phone over 3 times in 10 min
  - phone as key
  - store count(number) as value, begin from 1, increment each round
  - get the key and check each check if value over 3 times in a 10 min time frame (use TTL)
- sorted set
  - can use timestamp as associated score and get element from last X min ([ZRANGE and score arg](https://redis.io/docs/latest/commands/zrange/))
  - but this is set, and we need to count how many different phone we have
  - [[redis sorted set example]]
- list
  - key is sentryToken
  - we can save timestamp and phoneNumber into value
  - check 3 different phone for the same sentry token session
    - get all data, R.uniq(elements).length > 3
  - check for the same phone over 3 times in 10 min
    - get all data, filter to get element within 10 min window (we save timestamp, so we can filter)
    - check if any element have over 3 count
  - con:
    - serialization(de-serialization) cost perf
    - more complicated code
  - pro: use only one redis data strcuture

## meeting josh 2024_06_25

![[IMG-BE - 阻擋與封鎖名單機制 - Plan and Prepare API doc-20241003105001311.png]]

- redis prefix design, use functionaliy, flow and mechnaims as prefix
- redis need to handle all attack, do all job, db is only as a later side
  - redis can handle a lot of traffic, db can not, so db need to handle all mechanism logic
  - redis can ini set 10 min ttl, and then when block, set 200 min ttl
  - why 200 min?
  - use mutli / pipe with tx if you use hset



## josh asked
- check if phone is db is still in block period? why?
	- you need to ensure DB and redis both have consistent data
	- say, for some reason, you redis got data lose, you need to ensure db can backfill it
	- since you use db, so you need to have a mechanism to ensure the data is sync in most of the time
	- db become the final true, the source of truth when you have redis and db, both setup design, so when you redis don't have the record, but db does, this can happen(even rarely), you shall have a mechanism in place to ensure they sync up
- need to use redlock -> will it hit perf?
	- we will only allow one phone check at time, no cocurrent allow
	- say we don't, use how many cocurrent will get limit? no idea?
	- josh think we dont' need to have a redis lock here, maybe too much
	- if you wana use lokc check this code
```ts
  const lock = await setRedisLock(`Hermes:Box:${boxId}:BatSwapEvent`, {
    errorMessage: customErrors.REDIS_LOCK_DUPLICATE.type,
    ttl: 60000
  })

```

  - test case principle
	  - no run multi "fn"(the fn you want to test) multiple time in one test
	  - why?
	  - you will have 負負的正 case
	  - you make test more complicated
	  - if you really need to test co-currently issue, you can violate this rule, but
		  - you need to test all cases before this co-currently test

## try compond key

not a good idea
keys is O(n), n is all keys in redis
if you really need to use `keys`
use `scan`, which is a batch ops of `keys`
but it is still slow since it still search all keys in redis, but scan do it in a batch way and can decrease pressure of redis

```sh

> SET token:123:phone:0931333 1
"OK"

> SET token:123:phone:0931331 1
"OK"

> SET token:123:phone:0931332 1
"OK"

> keys token:123*
> 1) "token:123:phone:0931332"
> 2) "token:123:phone:0931331"
> 3) "token:123:phone:0931333"


> 8) get token:123:phone:0931332 "1"

```
