


# 最新發展


觀念
- 一開始存的就是 jsonb 
	- -> 想要彈性
	- 因此透過 proto -> 也讓他有彈性，直接傳 string 即可
	- 所謂的彈性，就是如果新增一個差異很多的東西進來，未來不需要改 old code
- 維度 encode into key -> 好 validate



```js fold


/**
 * proto 的設計似乎也可以跟 type 的設計分開:
 * 只要 有在 algorithm 裡面去 validate, 為了簡化 proto 的設計, 也可以整包 configs 就是一個 string 出去
 * 優點：
 *  - proto 的設計很簡單, 整包 configs 就是一個 string 出去
 *  - 因為存在 db 就是 jsonb, 因此傳出去也可以是一包 string
 *  - 未來新增 algo, proto 不需要改
 *  - 如果選了這個作法，那 pricing config 那邊的設計就可以複雜一點? 因為避開了 proto 的 複雜設計
 * 缺點:
 * - client 那邊要 maintain 每一種 algorithm 的 shape
 * 
 * 
 * 理想的情況：
 * 
 * 如果我需要加上一個全新的，很多東西都不同的 algo, 那我應該只需要去新增一個 class, 跟那些新增東西對應的 type 就好
 * 
 * 因此，proto 那邊直接一個 string 就好, 新增 algo 不需要動 proto
 *
 * 
 * 
 */


const newResponse = {
   result: "success",
   pricingIdAndPricingConfigs: [
     {
      /**
       * 缺點：validate 比較麻煩, 需要去拿到 CharingStep 的 value, 確認是否為 return 或 reserve
       */
       pricingId: "2618",
       pricingConfigsAtQuote: [
         {
           pricingConfigsId: "2118",
           algorithm: 1,
           configs: {
             location: 1,
             rentFlow: 2,
             configDetails: [
              {
                CharingStep: 1,  // 1 is return
                dailyType: 1,   // 1 is weekday
                timeSlot: 1,    // 1 is nonSpecialPeriod 
                initialMin: 1,
                initialMinPrice: 3,
                pricePerMin: 0,
                ratePerKm: 0, 
                // 新增的參數追加 比如四輪車的參數...
                最高收費時數: 10,
              },
              {
                CharingStep: 2,  // 1 is reserve
                dailyType: 1,   // 1 is weekday
                timeSlot: 1,    // 1 is nonSpecialPeriod 
                initialMin: 1,
                initialMinPrice: 3,
                pricePerMin: 0,
                ratePerKm: 0, 
                // 新增的參數追加 比如四輪車的參數...
                最高收費時數: 10,
              }
             ]
           },
         },
       ],
     },
     {
      /**
       * 優點，很好 validate, 因為 差異 encoded in keyName
       */

       pricingId: "2617",
       pricingConfigsAtQuote: [
         {
           pricingConfigsId: "2120",
           algorithm: 1,  
           configs: {  
             location: "1",
             rentFlow: "1",  // 調整為 array?  [1, 2]
             return: {
               weekdayNonSpecialPeriod: {
                 initialMin: 2,
                 initialMinPrice: 2,
                 pricePerMin: 0,
                 ratePerKm: 0,
               },
             },
             reserve: {
              weekdayNonSpecialPeriod: {
                initialMin: 2,
                initialMinPrice: 2,
                pricePerMin: 0,
                ratePerKm: 0,
              },
            },
           },
         },
         {
           pricingConfigsId: "2122",
           algorithm: 1,
           configs: {
             location: "1",
             rentFlow: "1",
             curbBasicReturn: {
               weekdayNonSpecialPeriod: {
                 initialMin: 2,
                 initialMinPrice: 2,
                 pricePerMin: 0,
                 ratePerKm: 0,
               },
             },
           },
         },
       ],
     },
   ],
 };


```



---


# 脈絡

Jack 有舉一個例子
你可以直接依照 biz model 去設計，開表
類似 biker, rider and order table
也可以思考一個抽象層, 然後一個表，可以更方便的切換
類似 dispatch -> type = 1,2,3

要小心偶然複雜度，如果是你自己新增的概念
但是如果只是簡單的依照 biz domain 一比一去設計
這樣 biz 一直擴充，你都要改，這樣彈性就不夠好，因為你的設計無法自動兼容變化


unify 設計
```

 {
   pricingConfigsId: "2118",
   algorithm: 1,
   configs: {
	 location: 1,
	 rentFlow: 2,
	 
	 // 三個維度攤平 -> 這樣設計的好處：如果新增不同的 類別 (不同維度，可以最大程度 reuse 一樣的 proto/interface)
	 CharingStep: 1,
	 dailyType: 3,
	 timeSlot: 1,
	
	 configDetails: {
	   initialMin: 1,
	   initialMinPrice: 3,
	   pricePerMin: 0,
	   ratePerKm: 0, 
	   // 新增的參數追加放在這裡
	 }
   },
 }

```




特化
```ts

 {
   pricingConfigsId: "2122",
   algorithm: 1,
   configs: {
	 location: 1,
	 rentFlow: 1,
	 curbBasicReturn: {
	   weekdayNonSpecialPeriod: {
		 initialMin: 2,
		 initialMinPrice: 2,
		 pricePerMin: 0,
		 ratePerKm: 0,
	   },
	 },
   },
 }

```


| 特徵    | unify         | 特化                                |
| ----- | ------------- | --------------------------------- |
| 變動適應性 | 好，可以共用同一個type | 一個新的 algo/config, 就需要新增一個         |
| 型別保護  | 比較弱，因為要共用     | 專一，型別保護好                          |
| 理解程度  | 看右邊           | 只看一個，比較清楚。但是如果要看整體，複雜度跟 unify 一樣。 |
| 效能    | 這邊的量不大，兩個差不多  | NA                                |
總結：
可以共用，未來如果新增 type, 不需調整，建議用 unify



