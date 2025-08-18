





==


再看一下有沒有什麼疑慮，不然就先整進去 ps 了



array or object?
- are they in order? -> no
- do we need to iterate them? -> no
- so just use js object




# Question
- tp_id pass to is just log?
- scooter_odo or just or tripOdo?

\

- [ ] 到時候要用 algo class 要用 di 注入 or use helper 集中管理, can ask Evan
- 寫入，mgmt 建立資料，可能用 static validate(mgm_input_config), valide necessary input and throw error is not pass -> 寫入 db
	- or pass config via ctor 展開
- 讀取，會展開
	- 展開如下圖，means, each case need to have default value

![[IMG-BE - Defined configuration structure for scooter-20241003104933677.png]]




[asana](https://app.asana.com/0/1206177962100119/1207795412760290/f)
![[IMG-BE - Defined configuration structure for scooter-20241003104948753.png]]








國家
租界模式需要記錄嗎？
![[IMG-BE - Defined configuration structure for scooter-20241003105000662.png]]


![[IMG-BE - Defined configuration structure for scooter-20241003105000785.png]]


![[IMG-BE - Defined configuration structure for scooter-20241003105017435.png]]


![[IMG-BE - Defined configuration structure for scooter-20241003105017687.png]]


![[IMG-BE - Defined configuration structure for scooter-20241003105043727.png]]


![[IMG-BE - Defined configuration structure for scooter-20241003105043992.png]]


\

  weekday = 1, // 工作日
  holiday = 2, // 假日
  regularDayOff = 3, // 例假日
  makeUpWorkday = 4, // 補班日
  bridgeDay = 5 // 彈性放假日





# backup

client code backup
```ts fold
import { Injectable } from '@nestjs/common'
import R from 'ramda'
import { PricingConfigsRepository } from 'src/repositories'
import { AlgorithmService } from './algorithm.service'
import AlgorithmBase from './cases/AlgorithmBase'

/**
 * @description
 * This is the DEMO CODE for how to get pricing algorithm via pricingConfigId
 */
@Injectable()
export class ClientCode {
  constructor(
    private pricingConfigsRepository: PricingConfigsRepository,
    private algorithmService: AlgorithmService
  ) {}

  async getAlgorithms(inputPriceConfigId: string): Promise<AlgorithmBase> {
    const pricingConfig = await this.pricingConfigsRepository.getPricingConfigsById(inputPriceConfigId)
    if (R.isNil(pricingConfig)) {
      throw new Error('pricingConfig is null')
    }

    return this.algorithmService.getAlgorithm(pricingConfig.algorithm, pricingConfig.configs)
  }
}


```

