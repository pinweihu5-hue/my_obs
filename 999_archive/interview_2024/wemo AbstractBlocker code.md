
有rate limit 的 endpoint 會 hit loadCache, 確認 redis 記錄等等..如下
```ts fold

  protected async loadCache(force = false): Promise<LockCacheData> {
    
    if (!force) {

      // 去 redis 拿資料
      const redisData = await this.redis.hgetall(this.getRedisKey())
      // 如果可以拿到，就直接返回
      // 這邊的 code 也顯示 redis 在前面檔，不會直接進入 db
      if (!R.isEmpty(redisData)) {
        return {
          ...redisData,
          isBlock: `${redisData.isBlock}` === 'true',
          counter: Number(redisData.counter || 1)
        }
      }
    }

    // 如果 redis 拿不到, 去 db 撈
    const dbData = this.getBlockSeconds() > 0 ? await this.getRecordFromDB() : null


    // 如果 db 可以撈到
    if (!R.isNil(dbData)) {
      const expireTimestamp = R.defaultTo(this.getBlockSeconds() * 1e3 + dbData.beginAt.getTime(), dbData.endAt?.getTime())
      const diffSeconds = Math.ceil((expireTimestamp - moment().utc().toDate().getTime()) * 1e-3)

	  // 且還在 被 block 的時間內
      if (diffSeconds > 0) {

        // 回補 redis 資料, 砍掉 old data, set new data, 設定 上面算出的 expire 時間
        await this.redis
          .multi()
          .del(this.getRedisKey())
          .hmset(this.getRedisKey(), {
            isBlock: true,
            counter: 999
          })
          .expire(this.getRedisKey(), diffSeconds)
          .exec()
        return {
          isBlock: true,
          counter: 999
        }
      }
    }


    // 如果 db 沒資料，那 redis 也 reset/砍掉, set 新的資料, 開始記錄
    await this.redis
      .multi()
      .del(this.getRedisKey())
      .hmset(this.getRedisKey(), {
        isBlock: false,
        counter: 0
      })
      .expire(this.getRedisKey(), this.getTimeWindowInSec())
      .exec()
    return {
      isBlock: false,
      counter: 0
    }
  }

```