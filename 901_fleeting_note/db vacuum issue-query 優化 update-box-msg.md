# 解法

- 目前懷疑是 scooter 表 and boxMsg 表過度 freq write → dead tunple too much → vacuum → lock
- query 優化 update-box-msg
    - 不要多打
    - 優化方法
        - 寫入 db 前先進行差異分析，一樣就不寫入db， 只update有差異的欄位
            - update-box-msg 有很多欄位
        - 用 redis 記錄上次的欄位數字，如果不同再寫入，且也只寫入差異的
- 短時間內的多次資料變動，應該有一個肉店 -> redis or kafka(in the next stage)


```js fold

  async updateGPSandTotalOdo(scooterId: string, originalEntity: Scooter, newEntity: UpdateGPSAndOdoEntity): Promise<void> {
    const tag = this.tag + '/updateGPSandTotalOdo'

    try {
      const changesToUpdate = this.getEntityChangesForUpdate<Scooter, UpdateGPSAndOdoEntity>(originalEntity, newEntity)

      if (R.isNil(changesToUpdate)) {
        return
      }
      logger.debug({ tag, msg: { changesToUpdate: JSON.stringify(changesToUpdate) } })

      await this.client.getRepository(Scooter).update({ id: scooterId }, changesToUpdate)
    } catch (error) {
      logger.error({ tag, error })
      throw error
    }
  }


  getEntityChangesForUpdate<OriginalEntity, NewEntity>(originalEntity: OriginalEntity, newEntity: NewEntity): NewEntity | null {
    const tag = this.tag + '/getEntityChangesForUpdate'

    const getChangedKeys = (original: OriginalEntity, updated: NewEntity): string[] => {
      return Object.keys(updated).filter((key) => (original[key as keyof OriginalEntity] as unknown) !== (updated[key as keyof NewEntity] as unknown))
    }

    const changedKeys = getChangedKeys(originalEntity, newEntity)

    if (changedKeys.length === 0) {
      logger.debug({ tag, msg: 'No changes detected, skipping update.' })

      return null
    }

    // Create an object with only the changed keys and their new values
    return changedKeys.reduce((acc, key) => {
      acc[key as keyof NewEntity] = newEntity[key as keyof NewEntity]
      return acc
    }, {} as NewEntity)
  }


```