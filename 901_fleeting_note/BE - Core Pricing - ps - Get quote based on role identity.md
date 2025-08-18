[Asana](https://app.asana.com/0/1206177962100119/1207795409156791/f)


也包括 
BE - Core Pricing-ps - Endpoint of get pricing
[Asana](https://app.asana.com/0/1206177962100119/1207795409156788/f)



- [x] app side 是否可以透過一個var 動態調整另一個 var
```ts fold

const response0 = {
  pricingConfigName: "a",
  configParam: {
    location: "taiwan",
    rentFlow: "curb",
    return: {
      weekdayNonSpecialPeriod: {
        initialMin: 3,
        initialMinPrice: 3,
        pricePerMin: 3,
      },
    },
  },
};

const response2 = {
  pricingConfigName: "b",
  configParam: {
    location: "taiwan",
    rentFlow: "curb",
    reserve: {
      holidaySpecialPeriod: {
        ratePerKm: 3,
      },
    },
  },
};


```





- task
	- `m*n` 回應
	- db need to cache, use redis
	- 不同的algo需要處理顯示邏輯
		- 目前從 db 撈出來是 jsonB
		- 不同的algo 有不同的 jsonB
		- 不同的algo 要自己 format 好
			- 看看是否要加上幣別處理
		- 但是不同的 algo 丟出去的 format 好的東西，你要如何用不同的 proto response 定義然後傳出去?
		- 理想情況是，hermes 拿到就是很多不同形狀定義好的 config
			- like config_id1 -> shape1
			- like config_id2 -> shape2
			- like config_id3 -> shape3





---

proto
pricing_config_id_list
quote_timestamp
roles

go to PS to get algo, currency, pricing Config..etc


- [ ] setup proto dev
- [ ] define proto and ensure everything I need to calculate the algo is available





# bk





這一個 commit 是沒有使用 generic 處理 algo base 的版本
008b6bc2aaabd87555e2fef5cebd3b96bfa4ae72


```json

"@wemo/proto": "git+https://gitlab+deploy-token-5:ViN3JeipoLGsYqx2pcAR@athena.wemoscooter.com/wemo/proto.git#feat/pricing-service-add-pricing-pricingConfigs",

```



## station/四輪 code bk 



testing for algo
```ts fold

    it('should return stationBasic', async () => {
      const configsJsonForStationBasic: ConfigsJsonForStationBasic = {
        location: 'malaysia' as const,
        rentFlow: 'curb' as const,
        reserve: {
          weekdayNonSpecialPeriod: {
            MaxNumOfConsecutiveChargingHours: 1,
            originalRate: 1,
            overtimeRate: 1,
            ratePerKm: 1
          }
        },
        return: {
          weekdayNonSpecialPeriod: {
            MaxNumOfConsecutiveChargingHours: 2,
            originalRate: 2,
            overtimeRate: 2,
            ratePerKm: 2
          }
        }
      }
      const result = service.getAlgorithm(AlgorithmEnum.stationBasic, configsJsonForStationBasic)
      expect(result).toBeInstanceOf(AlgorithmStationBasic)
    })

```

algorithm.interface.ts
```ts fold




/**
 * @example
 *  {
 *    location: 'taiwan',
 *    rentFlow: 'station',
 *    reserve: {
 *      weekdayNonSpecialPeriod: {
 *        MaxNumOfConsecutiveChargingHours: 1,
 *        originalRate: 1,
 *        overtimeRate: 1,
 *        ratePerKm: 1
 *      }
 *    },
 *    return: {
 *      weekdayNonSpecialPeriod: {
 *        ratePerKm: 2
 *      }
 *    }
 *  }
 */
export interface ConfigsJsonForStationBasic extends ConfigsJsonBase {
  reserve: ConfigDetailForStation
  return: ConfigDetailForStation
}



export interface stationSettingForWeekdayNonSpecialPeriod {
  /**
   * 連續收費上限時數
   */
  MaxNumOfConsecutiveChargingHours: number
  /**
   * 原價費率
   */
  originalRate: number
  /**
   * 逾時費率
   */
  overtimeRate: number
  /**
   * 里程費
   */
  ratePerKm: number
}


/**
 * 站點租還
 *  一般日 - 非特殊時段
 */
export interface ConfigDetailForStation {
  weekdayNonSpecialPeriod: stationSettingForWeekdayNonSpecialPeriod
}


```

class AlgorithmStationBasic.ts
```ts fold
import { Injectable } from '@nestjs/common'
import AlgorithmBase from './AlgorithmBase'
import { ConfigsJsonForStationBasic } from '../algorithm.interface'

@Injectable()
export default class AlgorithmStationBasic extends AlgorithmBase {
  constructor() {
    super()
  }

  protected validateConfig() {}
  protected unpackConfig() {}
  protected packConfig() {}
  public saveConfig() {}
  public calculatePrice() {
    return 0
  }

  public loadConfig(config: ConfigsJsonForStationBasic) {
    // validate maybe via Zod
    this.validateConfig()
    // unpack
    this.unpackConfig()

    return this
  }
}

```

proto
```ts fold

message StationBasic {

BaseConfigParams baseConfigParams = 1;

StationBasicReserve stationBasicReserve = 2;

StationBasicReturn stationBasicReturn = 3;

}

  
  

message StationBasicReserve {

message WeekdayNonSpecialPeriod {

int32 MaxNumOfConsecutiveChargingHours = 1;

int32 originalRate = 2;

int32 overtimeRate = 3;

}

WeekdayNonSpecialPeriod weekdayNonSpecialPeriod = 1;

}

  

message StationBasicReturn {

message WeekdayNonSpecialPeriod{

int32 ratePerKm = 1;

}

WeekdayNonSpecialPeriod weekdayNonSpecialPeriod = 1;

}

```