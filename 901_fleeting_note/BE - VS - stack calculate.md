


feat/vehicle-service-get-price-and-rental
"@wemo/proto": "file:../proto",




- [x] complete testing
	- [x] add dif model
	- [x] add validate, sanity check
- [x] chunk 處理
- [x] 整理確認要 testing 的東西
- [x] add cache
	- 你這邊有 query time.. 因此
	- [x] 一分鐘要同一個
	- [x] keep 10~30min
- [x] refactor


- [x] tenetId 不用傳進來嗎? 意思就是全部的 tenet都要拿?



# thought
[[db vs app, 邏輯和loading要放哪裡]]





# BK



## chunked
- 因為我目前用 for-loop 裡面有 continue, 我無法簡單的改為 for-each and keep `continue` sematic
```ts fold

  /**
   * 計算堆疊結果
   *
   * 優先權順序：
   * - 目前有三個 tag 層次， system, vehicle and vehicle model
   * - 優先選擇 system tag 上的 action, 然後是 vehicle tag 上的 action, 最後才是 vehicle model tag 上的 action
   * - 如果優先層次的 tag 齊備所有的資料 (not null for all required data)，就可以 return 結果，不需要後續處理
   * - 如果優先層級的 tag 有無法租借的 相關 action, 就可以 return 對應的結果，不需要後續處理
   * - 如果所有層級的 tag 都沒有設定應該設定的欄位 (目前是指標價) -> 就可以 return 無法租借，不需要後續處理
   *
   *  目前的 scope (2024 Aug):
   * - no handle system tag
   * - no handle zone
   * - no handle 是否可以租借  → isRentable = true ，除非 priceId 都沒有設定
   * - only handle priceId
   */
  async getPricingAndRentalInfo(payload: GetPricingAndRentalInfoRequest): Promise<PricingAndRentalInfo[]> {
    const tag = this.tag + '/getPricingAndRentalInfo'
    this.logger.debug({ tag, payload })
    const { vehicleIds, rentFlow, queryTime } = payload

    const CHUNK_SIZE = 10
    const BATCH_SIZE = 100

    // 每 100 筆為一個 batch
    const vehicleIdsChunked: string[][][] = R.splitEvery(Math.max(1, CHUNK_SIZE), R.splitEvery(BATCH_SIZE, vehicleIds))

    const result: PricingAndRentalInfo[] = []
    for (const vehicleIds of vehicleIdsChunked) {
      // 每次 發送 10 個 chunk
      const batchSentResult = await Promise.all(
        vehicleIds.map((vehicleIds) => this.getPricingAndRentalInfoHelper({ vehicleIds, rentFlow, queryTime }))
      )
      batchSentResult.forEach((sendResult) => {
        result.push(...sendResult)
      })
    }

    return result
  }

  async getPricingAndRentalInfoHelper(payload: { vehicleIds: string[]; rentFlow: RentFlow; queryTime: string }) {
    const tag = this.tag + '/getPricingAndRentalInfoHelper'
    const { vehicleIds, rentFlow, queryTime } = payload
    this.logger.debug({ tag, payload })

    const result = []

    for (const vehicleId of vehicleIds) {
      // 1. 初始化資料
      const vehicleInfo = {
        vehicleId: null,
        pricingId: null,
        isRentable: true // 目前這個階段先寫死為 true, 除非 priceId 都沒有設定, 才會是 false
      }

      vehicleInfo.vehicleId = vehicleId

      // 2. 拉出 vehicle 的 tagActions
      const vehicleTagsPerVehicle = await this.vehicleTagRepository.getByIdsAtQueryTime({
        vehicleId,
        rentFlow,
        queryTime
      })

      if (!R.isEmpty(vehicleTagsPerVehicle)) {
        const vehicleLatestTag = this.getLatestRecord<VehicleTag>(vehicleTagsPerVehicle)
        const tagActionList = await this.tagActionRepository.getByTagId(vehicleLatestTag.tagId)
        for (const tagActionEle of tagActionList) {
          if (tagActionEle.tagAction === TagActionEnum.pricing && R.isNotNil(tagActionEle.pricingId)) {
            vehicleInfo.pricingId = tagActionEle.pricingId
          }
        }

        // 如果資料備齊，該台車不需要進行後續判斷
        if (R.isNotNil(vehicleInfo.pricingId) && R.isNotNil(vehicleInfo.vehicleId)) {
          result.push(vehicleInfo)
          continue // continue to next vehicle
          // return
        }
      } else {
        this.logger.debug({ tag, msg: `no vehicleTags for ${vehicleId}` })
      }

      // 3. 拉出 vehicle Model 的 tagActions
      const vehicleModelId = (await this.vehicleRepository.getByIds([vehicleId])).map(
        (vehicle) => vehicle.vehicleModelId
      )[0]

      const modelTagsList = await this.vehicleModelTagRepository.getByIdsAtQueryTime({
        vehicleModelId: vehicleModelId,
        rentFlow,
        queryTime
      })

      if (!R.isEmpty(modelTagsList)) {
        const modelLatestTag = this.getLatestRecord<VehicleModelTag>(modelTagsList)
        const modelActionList = await this.tagActionRepository.getByTagId(modelLatestTag.tagId)
        for (const modelActionEle of modelActionList) {
          if (modelActionEle.tagAction === TagActionEnum.pricing && R.isNotNil(modelActionEle.pricingId)) {
            vehicleInfo.pricingId = modelActionEle.pricingId
          }
        }
      } else {
        this.logger.debug({ tag, msg: `no vehicleModelTags for ${vehicleModelId}` })
      }

      // 4. 如果全部 tag 看完，還是沒有 false 資料，該台車無法租借
      if (R.isNil(vehicleInfo.pricingId)) {
        vehicleInfo.isRentable = false
      }

      // 5. push to result
      result.push(vehicleInfo)
    }

    return result
  }


```



have below error, so I use ioredis
![[IMG-BE - VS - stack calculate-20241003104933708.png]]




```vs fold

/**
 * TODO: 測試情境
 * 1. 比較晚生效的優先 (enabledAt DESC) , 先到期的優先 (deprecatedAt ASC), 後建立的優先 (id DESC)
 * 2. filter out RentFlow
 * 3. 各種會出現的 user case 
 * 
 * vehicle tags is rentable is true and priceId exist (skip vehicle model setting)-> rentable and pricingId is number string
 * vehicle tags is not rentable is true and priceId exist (skip vehicle model setting) -> not rentable and pricingId is null
 * 
 * vehicle tags does not defined rentable and priceId exist (vehicle model has pricingId and rentable) -> rentable and use vehicle tag pricingId
 * 
 * priceId in both vehicle and model tag is null (even both tag are rentable) -> not rentable and pricingId is null
 * 
 * 
 * no config in vehicle tag, use vehicle model tag (rentable and have pricingId) -> rentable and use vehicle model tag pricingId
 * 
 * 
 * vehicle tag rentable but no pricingId, vehicle model not rentable and pricingId exist -> ??
 * 
 */

```