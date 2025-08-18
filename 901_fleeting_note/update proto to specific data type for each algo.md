

proto 要這樣改
```proto

message PricingConfigsAtQuote {
  string configsId = 1;
  int32 algorithm = 2;
  // string configs = 3;
  CurbBasicConfigs configs = 3;
}


message CurbBasicConfigs {
  string location = 1;
  string rentFlow = 2;
  CurbBasicReturnReturn return = 3;
}

message CurbBasicReturnReturn {
  WeekdayNonSpecialPeriod weekdayNonSpecialPeriod = 1;
}

message WeekdayNonSpecialPeriod {
  int32 initialMin = 1;
  int32 initialMinPrice = 2;
  int32 pricePerMin = 3;
  int32 ratePerKm = 4;
}

```


svc 層要改這兩個
![[IMG-update proto to specific data type for each algo-20241003104934068.png]]

test 修改如下
```ts

  describe.('getPricingConfigsAtQuote', () => {
    let tenantId: string
    let role: number
    let beginAt: Date
    let algorithm: AlgorithmEnum
    let pricing: Pricing
    let pricingId: string

    const now = moment()
    const quoteDate = moment('2024-06-16 08:00:00.000000')

    beforeEach(async () => {
      tenantId = faker.number.int({ min: 1, max: 100 }).toString()
      pricing = await createPricing(dataSource.manager, { tenantId })

      pricingId = pricing.id
      role = faker.helpers.enumValue(Role)
      beginAt = moment().toDate()
      algorithm = faker.helpers.enumValue(AlgorithmEnum)
    })

    test('success', async () => {
      const priceId1 = (await createPricing(dataSource.manager, { tenantId })).id
      const priceId2 = (await createPricing(dataSource.manager, { tenantId })).id
      const priceId3 = (await createPricing(dataSource.manager, { tenantId })).id
      const priceId4 = (await createPricing(dataSource.manager, { tenantId })).id

      const genMockPricingConfigs = () => {
        return {
          algorithm: AlgorithmEnum.curbBasic,
          beginAt: quoteDate.clone().subtract(1, 'day').toDate(),
          configs: {
            location: 1,
            rentFlow: 1,
            return: {
              weekdayNonSpecialPeriod: {
                initialMin: 2,
                initialMinPrice: 2,
                pricePerMin: 0,
                ratePerKm: 0
              }
            }
          }
        }
      }

      const [{ id: configs1Id }, { id: config2Id }, { id: config3Id }, { id: config4Id }, { id: config5Id }] =
        await Promise.all([
          createPricingConfigs(dataSource.manager, {
            tenantId,
            role: 1,
            pricingId: priceId3,
            ...genMockPricingConfigs()
          }),
          createPricingConfigs(dataSource.manager, {
            tenantId,
            role: 2,
            pricingId: priceId2,
            ...genMockPricingConfigs()
          }),
          createPricingConfigs(dataSource.manager, {
            tenantId,
            role: 3,
            pricingId: priceId3,
            ...genMockPricingConfigs()
          }),
          createPricingConfigs(dataSource.manager, {
            tenantId,
            role: 4,
            pricingId: priceId4,
            ...genMockPricingConfigs()
          }),
          createPricingConfigs(dataSource.manager, {
            tenantId,
            role: 2,
            pricingId: priceId2,
            ...genMockPricingConfigs()
          })
        ])

      /**
       * 呼叫:
       * scenario: price id is priceId2, priceId3 and role is 1, 2
       * 結果：(union of above): (priceId2,1), (priceId2,2), (priceId3,1), (priceId3,2)
       */
      const pricingIds = [priceId2, priceId3]
      const useRoleIds = [1, 2]
      const response = await lastValueFrom(
        pricingConfigsService.GetPricingConfigsAtQuote({
          pricingIds,
          useRoleIds,
          ISOStringAtQuote: quoteDate.toISOString()
        })
      )

      expect(response).toStrictEqual({
        result: 'success',
        pricingIdAndPricingConfigs: [
          {
            pricingId: priceId2,
            pricingConfigsAtQuote: [
              {
                configsId: config5Id > config2Id ? config5Id : config2Id, // pick the bigger config
                algorithm: 1,
                configs: {
                  location: '1',
                  rentFlow: '1',
                  return: {
                    weekdayNonSpecialPeriod: {
                      ratePerKm: 0,
                      initialMin: 2,
                      pricePerMin: 0,
                      initialMinPrice: 2
                    }
                  }
                }
              },
              {
                configsId: config2Id < config5Id ? config2Id : config5Id, // pick the smaller config
                algorithm: 1,
                configs: {
                  location: '1',
                  rentFlow: '1',
                  return: {
                    weekdayNonSpecialPeriod: {
                      ratePerKm: 0,
                      initialMin: 2,
                      pricePerMin: 0,
                      initialMinPrice: 2
                    }
                  }
                }
              }
            ]
          },
          {
            pricingId: priceId3,
            pricingConfigsAtQuote: [
              {
                algorithm: 1,
                configs: {
                  location: '1',
                  rentFlow: '1',
                  return: {
                    weekdayNonSpecialPeriod: {
                      ratePerKm: 0,
                      initialMin: 2,
                      pricePerMin: 0,
                      initialMinPrice: 2
                    }
                  }
                },
                configsId: configs1Id
              }
            ]
          }
        ]
      })
    })

  
  })


```