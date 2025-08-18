---  
alias:  
creation_date: 2024-06-06 14:39  
tags: 
related:
- [[_wemo_tickets]]

---  



![[IMG-wemo_保險費計算方式-20241003104934092.png]]
```ts


export class FubonInsurancePolicy implements InsurancePolicy {
  private readonly tag = 'v2/insurance/service/agent/policyStrategy/Fubon'
  private readonly LOW_INSURANCE_TIME_MIN = 15
  private readonly POLICY_DETAILS: PolicyDetail[] = [
    // {
    //   name: '第三人責任險',
    //   reservePrice: 0.225,
    //   pricePerMinutes: 0.017
    // },
    {
      name: '限額車對車碰撞險',
      reservePrice: 1.545,
      pricePerMinutes: 0.119,
      dangerousCoefficient: 0.65
    }
    // {
    //   name: '營業損失補償保險',
    //   reservePrice: 0.223,
    //   pricePerMinutes: 0.015
    // }
  ]
  //...
}



  private calculateAlgorithm({ reservePrice, pricePerMinutes, dangerousCoefficient }: PolicyDETAIL, rentTimeInSec: number): number {
    let subtotal = rentTimeInSec > 0 ? reservePrice : 0

    // if over 15 minutes (LOW_INSURANCE_TIME_MIN)
    const extraTimeInMinute = rentTimeInSec / 60 - this.LOW_INSURANCE_TIME_MIN

    if (extraTimeInMinute > 0) {
      // 超過的部分，需要使用 pricePerMinutes 來計費
      subtotal += pricePerMinutes * extraTimeInMinute

      const subtotalOperation = mathChain(subtotal)

      // 然後再把這個費用除以危險係數, 危險係數越高，費用越低
      subtotal = subtotalOperation.divide(dangerousCoefficient).done()
    }
    return subtotal
  }
```

