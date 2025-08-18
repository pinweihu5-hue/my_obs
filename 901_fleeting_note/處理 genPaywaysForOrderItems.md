[[wemo_code_note]]




- 盤點真實資料 for order item, charging detail, orderreceipt
	
	===

	![[CleanShot 2024-05-11 at 11.26.10.png]]

	===
	
	20240101190537890 2
	
	cancel case
	
	amount 57
	
	apply pay
	
	item only one 57
	
	20240101212821072 2
	
	as above
	
	20240101223213692 2
	
	amt 39
	
	3-20
	
	6-19
	
	orderItem → rent amt39, invoceAmt19, paid:19
	
	→ 有 covert 了
	
	20240101224532906 2
	
	as above
	
	20240101230935995 2
	
	amt 3
	
	detail: payway1 for 3
	
	===
	
	amt 18
	
	1 orderItem
	
	recept 1
	
	===
	
	orderItem2筆:
	
	3 insurance
	
	0 ride
	
	- [ ] 有可能會出現 orderItem like
	
	3 insurance
	
	0 ride
	
	==

![[CleanShot 2024-05-11 at 11.26.40.png | 500]]

==

price 3

3 筆 orderItem

![[CleanShot 2024-05-11 at 11.27.04.png]]

detail?

![[CleanShot 2024-05-11 at 11.27.13.png]]
	
==

user insurance 會撈出3筆

會加總?

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/130666a0-0d5b-486c-a639-d570380467d4/fac9204b-958d-4f54-ab10-2d02876a5c76/Untitled.png)

很怪，有四筆 detail for the same rent

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/130666a0-0d5b-486c-a639-d570380467d4/e3e37168-1353-4bf6-91ea-b3d8ed474934/Untitled.png)

20240102034853259 2



----



- check 重複prod order receipt
	
	```markdown
	
	撈出 OrderReceipts 中 orderNo 重複的那些 record, 就是一個 orderNo 會對應的兩筆以上的detail
	SELECT or2."OrderNo", COUNT(*) AS count
	FROM postgres.ceres."OrderReceipts" or2 
	WHERE or2."TransactionTime"  >= '2024-01-01 12:26:12.650'
	GROUP BY or2."OrderNo" 
	HAVING COUNT(*) >= 2;
	
	===
	20200427115427580	2 
	-> why 沒有 orderItem?
	apple pay
	-> 2020年， josh 說的沒有 orderItem的情況
	
	20201023113231160	3
	no orderItem
	apple pay
	
	20240101084701159	2
	
	2 detial, one is credit 8 another one is wallet 10
	1 orderItem ride  18
	
	20240101190537890	2
	20240101212821072	2
	20240101223213692	2
	20240101224532906	2
	20240101230935995	2
	20240102034853259	2
	
	20240102071842814	2
	20240102074020253	2
	20240102094749654	2
	20240102100255546	2
	20240102104919497	2
	20240102113132913	2
	20240102121431871	2
	20240102122050578	2
	20240102142534895	2
	20240102151005394	2
	20240102152149555	2
	20240102152959749	2
	20240102161630734	2
	20240102170905386	2
	20240102174116936	2
	20240102182301069	2
	20240102184921554	2
	20240102185649657	2
	20240102200052014	2
	20240102200056490	2
	20240102200059443	2
	20240102200115260	2
	20240102200117948	2
	20240102200154277	2
	20240102200557800	2
	20240102200833112	2
	20240102201502941	2
	20240102204413377	2
	20240102205520801	2
	20240102210238113	2
	20240102211020814	2
	20240102215011132	2
	20240103000347364	2
	20240103002625796	2
	20240103044600196	2
	20240103103353400	2
	20240103115655443	2
	20240103125241480	2
	20240103133240728	2
	20240103133314165	2
	20240103140056491	2
	20240103150357026	2
	20240103150915105	2
	20240103184817935	2
	20240103185609962	2
	20240103190138374	2
	20240103195952993	2
	20240103200100777	2
	20240103200121617	2
	20240103200735742	2
	20240103202328366	2
	20240103202749364	2
	20240103215648811	2
	20240104003004590	2
	20240104082629712	2
	20240104084126240	2
	20240104090932394	2
	20240104094538825	2
	20240104095033381	2
	20240104110310469	2
	20240104120752849	2
	20240104121037352	2
	20240104143648592	2
	20240104161016350	2
	20240104162116871	2
	20240104172200313	2
	20240104200052516	2
	20240104200124369	2
	20240104200140082	2
	20240104200147885	2
	20240104200440284	2
	20240104200637362	2
	20240104200650035	2
	20240104200652614	2
	20240104210906359	2
	20240105072730912	2
	20240105073558905	2
	20240105084935903	2
	20240105093734435	2
	20240105093949646	2
	20240105094339545	2
	20240105095343706	2
	20240105101405034	2
	20240105102358989	2
	20240105110436834	2
	20240105130231237	2
	20240105130622774	2
	20240105133650609	2
	20240105134146588	2
	20240105141633836	2
	```



-----


- try gpt
	
	- prompt3
		
		````markdown fold
		
		```ts
		export enum paywayType {
		  creditCard = 'creditCard',
		  credit = 'credit',
		  wallet = 'wallet',
		  linePay = 'linePay',
		  applePay = 'applePay',
		  uuPay = 'uuPay',
		  rebateVoucher = 'rebateVoucher',
		  plusPay = 'plusPay'
		}
		
		export interface ChargingDetailPayway {
		  payWays: {
			payWay: string
			amount: number
		  }[]
		}
		
		export enum DefaultRentChargingDescription {
		  ride = '騎乘費',
		  insurance = '安心保障服務費',
		  hourlyPlanMileageFee = '小時租里程費'
		}
		
		const mockOrder1 = {
		  amount: 100,  
		  details: [   
			{ payway: paywayType.credit, amount: 90 }, 
			{ payway: paywayType.creditCard, amount: 10 } 
		  ],
		  orderItems: [ 
			{ chargingDetailId: '1', amount: 20, description: '騎乘費', allowedPayway: { credit: true } },
			{ chargingDetailId: '2', amount: 70, description: '小時租里程費', allowedPayway: { credit: true } },
			{ chargingDetailId: '3', amount: 10, description: '安心保障服務費', allowedPayway: { credit: false } }
		  ]
		}
		
		const result1 = genPaywaysForRentAndInsurance(mockOrder1)
		console.log('=====> result1: ', result1)
		const expectedResult1 = {
		  rent: {
			payWays: [{ payWay: 'credit', amount: 20 }]
		  },
		  insurance: {
			payWays: [{ payWay: 'creditCard', amount: 10 }]
		  },
		  mileage: {
			payWays: [{ payWay: 'credit', amount: 70 }]
		  }
		}
		assert.deepStrictEqual(result1, expectedResult1, 'The objects should be deeply equal')
		
		function genPaywaysForRentAndInsurance(
		  order: {
			amount: number
			details: {
			  payway: string
			  amount: number
			}[]
			orderItems?: {
			  chargingDetailId: string
			  amount: number
			  description: string
			  allowedPayway: { credit: boolean }
			}[]
		  }
		
		) {
		
		// to do
		
		}
		
		````
		
		help me complete genPaywaysForRentAndInsurance
		
	- prompt2
		
		````markdown fold
		請看下面的 typescript 程式碼：
		
		```ts
		export enum paywayType {
		  creditCard = 'creditCard',
		  credit = 'credit',
		  wallet = 'wallet',
		  linePay = 'linePay',
		  applePay = 'applePay',
		  uuPay = 'uuPay',
		  rebateVoucher = 'rebateVoucher',
		  plusPay = 'plusPay'
		}
		
		export interface ChargingDetailPayway {
		  payWays: {
			payWay: string
			amount: number
		  }[]
		}
		
		export enum DefaultRentChargingDescription {
		  ride = '騎乘費',
		  insurance = '安心保障服務費',
		  hourlyPlanMileageFee = '小時租里程費'
		}
		
		const mockOrder1 = {
		  amount: 100,  
		  details: [   
			{ payway: 'credit', amount: 90 }, 
			{ payway: 'creditCards', amount: 10 } 
		  ],
		  orderItems: [ 
			{ chargingDetailId: '1', amount: 20, description: '騎乘費', allowedPayway: { credit: true } },
			{ chargingDetailId: '2', amount: 70, description: '小時租里程費', allowedPayway: { credit: true } },
			{ chargingDetailId: '3', amount: 10, description: '安心保障服務費', allowedPayway: { credit: false } }
		  ]
		}
		
		const result1 = genPaywaysForRentAndInsurance(mockOrder1)
		console.log('=====> result1: ', result1)
		// result 1 shall be equal to expectedResult11
		const expectedResult1 = {
		  rent: {
			payWays: [{ payWay: 'credit', amount: 20 }]
		  },
		  insurance: {
			payWays: [{ payWay: 'creditCard', amount: 10 }]
		  },
		  mileage: {
			payWays: [{ payWay: 'credit', amount: 70 }]
		  }
		}
		assert.deepStrictEqual(result1, expectedResult1, 'The objects should be deeply equal')
		
		const mockOrder2 = {
		  amount: 110,  
		  details: [
			{ payway: 'credit', amount: 100 }, 
			{ payway: 'creditCards', amount: 10 }, 
		  ],
		  orderItems: [
			{ chargingDetailId: '1', amount: 100, description: '騎乘費', allowedPayway: { credit: true } },
			{ chargingDetailId: '2', amount: 10, description: '安心保障服務費', allowedPayway: { credit: false } },
		  ]
		}
		
		const result2 = genPaywaysForRentAndInsurance(mockOrder2)
		console.log('=====> result2: ', result2)
		assert.deepStrictEqual(obj1, obj2, 'The objects should be deeply equal')
		const expectedResult2 = {
		  rent: {
			payWays: [{ payWay: 'credit', amount: 100 }]
		  },
		  insurance: {
			payWays: [{ payWay: 'creditCard', amount: 10 }]
		  }
		}
		assert.deepStrictEqual(result2, expectedResult2, 'The objects should be deeply equal')
		
		const mockOrder3 = {
		  amount: 100, 
		  details: [
			{ payway: 'creditCards', amount: 100 }, 
		  ],
		  orderItems: [
			{ chargingDetailId: '1', amount: 20, description: '騎乘費', allowedPayway: { credit: true } },
			{ chargingDetailId: '2', amount: 70, description: '小時租里程費', allowedPayway: { credit: true } },
			 { chargingDetailId: '3', amount: 10, description: '安心保障服務費', allowedPayway: { credit: false } },
		  ]
		}
		
		const result3 = genPaywaysForRentAndInsurance(mockOrder3)
		console.log('=====> result3: ', result3)
		const expectedResult3 = {
		  rent: {
			payWays: [{ payWay: 'creditCard', amount: 20 }]
		  },
		  insurance: {
			payWays: [{ payWay: 'creditCard', amount: 10 }]
		  },
		  mileage: {
			payWays: [{ payWay: 'creditCard', amount: 70 }]
		  }
		}
		assert.deepStrictEqual(result3, expectedResult3, 'The objects should be deeply equal')
		
		const mockOrder4 = {
		  amount: 100,
		  details: [
			{ payway: 'creditCards', amount: 100 }, 
		  ],
		  orderItems: [
			{ chargingDetailId: '1', amount: 90, description: '騎乘費', allowedPayway: { credit: true } },
			{ chargingDetailId: '2', amount: 10, description: '安心保障服務費', allowedPayway: { credit: false } },
		  ]
		}
		
		const result4 = genPaywaysForRentAndInsurance(mockOrder4)
		console.log('=====> result4: ', result4)
		const expectedResult4 = {
		  rent: {
			payWays: [{ payWay: 'creditCard', amount: 90 }]
		  },
		  insurance: {
			payWays: [{ payWay: 'creditCard', amount: 10 }]
		  }
		}
		assert.deepStrictEqual(result4, expectedResult4, 'The objects should be deeply equal')
		
		const mockOrder5 = {
		  amount: 100, 
		  details: [
			{ payway: 'applePay', amount: 100 }, 
		  ],
		  orderItems: [
			{ chargingDetailId: '1', amount: 90, description: '騎乘費', allowedPayway: { credit: true } },
			{ chargingDetailId: '2', amount: 10, description: '安心保障服務費', allowedPayway: { credit: false } },
		  ]
		}
		
		const result5 = genPaywaysForRentAndInsurance(mockOrder5)
		console.log('=====> result5: ', result5)
		const expectedResult4 = {
		  rent: {
			payWays: [{ payWay: 'applePay', amount: 90 }]
		  },
		  insurance: {
			payWays: [{ payWay: 'applePay', amount: 10 }]
		  }
		}
		assert.deepStrictEqual(result5, expectedResult5, 'The objects should be deeply equal')
		
		const mockOrder6 = {
		  amount: 100,  // 訂單總金額
		  details: [
			{ payway: 'applePay', amount: 100 },
		  ]
		}
		
		const result6 = genPaywaysForRentAndInsurance(mockOrder6)
		console.log('=====> result6: ', result6)
		const expectedResult4 = {
		  rent: {
			payWays: [{ payWay: 'applePay', amount: 100 }]
		  }
		}
		assert.deepStrictEqual(result5, expectedResult5, 'The objects should be deeply equal')
		
		function genPaywaysForRentAndInsurance(
		  order: {
			amount: number
			details: {
			  payway: string
			  amount: number
			}[]
			orderItems?: {
			  chargingDetailId: string
			  amount: number
			  description: string
			  allowedPayway: { credit: boolean }
			}[]
		  }
		
		) {
		
		//... to complete
		
		}
		
		````
		
		符合以下條件，請根據上面的資訊，幫我完成genPaywaysForRentAndInsurance 函數。
		
		- 訂單總金額 amount 會優先使用 credit 這個 payway 支付, 然後才會去一個一個檢查其他 payway 是否有對應的支付
		- orderItems 可以透過 description 知道是哪一種費用, 裡面的 allowedPayway 的 credit 需要是 true, 才可以用 credit 這個 payway
		- 預設 credit 會先支付，然後才會用其他的 payway, 用哪一種payway, 看 details
		- 如果 argument input 沒有 orderItems 資料 -> 全部都歸於 rent
		- details.payway 可能包含的 paywayType 參考 paywayType interface
		
	- prompt1
		
		````markdown fold
		請看下面的 typescript 程式碼：
		
		```ts
		export enum paywayType {
		  creditCard = 'creditCard',
		  credit = 'credit',
		  wallet = 'wallet',
		  linePay = 'linePay',
		  applePay = 'applePay',
		  uuPay = 'uuPay',
		  rebateVoucher = 'rebateVoucher',
		  plusPay = 'plusPay'
		}
		
		export interface ChargingDetailPayway {
		  payWays: {
			payWay: string
			amount: number
		  }[]
		}
		
		export enum DefaultRentChargingDescription {
		  ride = '騎乘費',
		  insurance = '安心保障服務費',
		  hourlyPlanMileageFee = '小時租里程費'
		}
		
		// 情境1
		// 訂單總金額 100
		// 有 credit 90
		// 有3種orderItem: 騎乘費20, 小時租里程費70, 安心保障服務費10
		// 用 credit 付了騎乘費80 and 小時租里程費10
		// 保險費 10, 無法用 credit payway (因為 credit: false), 使用 details 可以找到的 creditCard payway
		const mockOrder1 = {
		  amount: 100,  // 訂單總金額
		  details: [  // 不同的 payway 有不同element
			{ payway: 'credit', amount: 90 }, // 騎乘費 + 小時租里程費
			{ payway: 'creditCards', amount: 10 } // 安心保障服務費
		  ],
		  orderItems: [ // 不同的 description 有不同 element
			{ chargingDetailId: '1', amount: 20, description: '騎乘費', allowedPayway: { credit: true } },
			{ chargingDetailId: '2', amount: 70, description: '小時租里程費', allowedPayway: { credit: true } },
			{ chargingDetailId: '3', amount: 10, description: '安心保障服務費', allowedPayway: { credit: false } }
		  ]
		}
		
		const result1 = genPaywaysForRentAndInsurance(mockOrder1)
		console.log('=====> result1: ', result1)
		// result 1 shall be equal to expectedResult11
		const expectedResult1 = {
		  rent: {
			payWays: [{ payWay: 'credit', amount: 20 }]
		  },
		  insurance: {
			payWays: [{ payWay: 'creditCard', amount: 10 }]
		  },
		  mileage: {
			payWays: [{ payWay: 'credit', amount: 70 }]
		  }
		}
		assert.deepStrictEqual(result1, expectedResult1, 'The objects should be deeply equal')
		
		// 情境2
		// 訂單總金額 110
		// 有 credit 100
		// 有2種orderItem: 騎乘費100, 安心保障服務費10
		// credit 優先付，因此付了騎乘費
		const mockOrder2 = {
		  amount: 110,  // 訂單總金額
		  details: [
			{ payway: 'credit', amount: 100 }, // 騎乘費
			{ payway: 'creditCards', amount: 10 }, // 安心保障服務費
		  ],
		  orderItems: [
			{ chargingDetailId: '1', amount: 100, description: '騎乘費', allowedPayway: { credit: true } },
			{ chargingDetailId: '2', amount: 10, description: '安心保障服務費', allowedPayway: { credit: false } },
		  ]
		}
		
		const result2 = genPaywaysForRentAndInsurance(mockOrder2)
		console.log('=====> result2: ', result2)
		assert.deepStrictEqual(obj1, obj2, 'The objects should be deeply equal')
		const expectedResult2 = {
		  rent: {
			payWays: [{ payWay: 'credit', amount: 100 }]
		  },
		  insurance: {
			payWays: [{ payWay: 'creditCard', amount: 10 }]
		  }
		}
		assert.deepStrictEqual(result2, expectedResult2, 'The objects should be deeply equal')
		
		// 情境3
		// 訂單總金額 100
		// 沒有 credit
		// 有3種orderItem: 騎乘費20, 小時租里程費70, 安心保障服務費10
		const mockOrder3 = {
		  amount: 100,  // 訂單總金額
		  details: [
			{ payway: 'creditCards', amount: 100 }, // 騎乘費 + 時租里程費 + 安心保障服務費
		  ],
		  orderItems: [
			{ chargingDetailId: '1', amount: 20, description: '騎乘費', allowedPayway: { credit: true } },
			{ chargingDetailId: '2', amount: 70, description: '小時租里程費', allowedPayway: { credit: true } },
			 { chargingDetailId: '3', amount: 10, description: '安心保障服務費', allowedPayway: { credit: false } },
		  ]
		}
		
		const result3 = genPaywaysForRentAndInsurance(mockOrder3)
		console.log('=====> result3: ', result3)
		const expectedResult3 = {
		  rent: {
			payWays: [{ payWay: 'creditCard', amount: 20 }]
		  },
		  insurance: {
			payWays: [{ payWay: 'creditCard', amount: 10 }]
		  },
		  mileage: {
			payWays: [{ payWay: 'creditCard', amount: 70 }]
		  }
		}
		assert.deepStrictEqual(result3, expectedResult3, 'The objects should be deeply equal')
		
		// 情境4
		// 訂單總金額 100
		// 沒有 credit
		// 有2種orderItem: 騎乘費90, 安心保障服務費10
		const mockOrder4 = {
		  amount: 100,  // 訂單總金額
		  details: [
			{ payway: 'creditCards', amount: 100 }, // 騎乘費 + 安心保障服務費
		  ],
		  orderItems: [
			{ chargingDetailId: '1', amount: 90, description: '騎乘費', allowedPayway: { credit: true } },
			{ chargingDetailId: '2', amount: 10, description: '安心保障服務費', allowedPayway: { credit: false } },
		  ]
		}
		
		const result4 = genPaywaysForRentAndInsurance(mockOrder4)
		console.log('=====> result4: ', result4)
		const expectedResult4 = {
		  rent: {
			payWays: [{ payWay: 'creditCard', amount: 90 }]
		  },
		  insurance: {
			payWays: [{ payWay: 'creditCard', amount: 10 }]
		  }
		}
		assert.deepStrictEqual(result4, expectedResult4, 'The objects should be deeply equal')
		
		// 情境5
		// 訂單總金額 100
		// 沒有 credit
		// 有2種orderItem: 騎乘費90, 安心保障服務費10
		// 跟上面不同，用 applePay
		const mockOrder5 = {
		  amount: 100,  // 訂單總金額
		  details: [
			{ payway: 'applePay', amount: 100 }, // 騎乘費 + 安心保障服務費
		  ],
		  orderItems: [
			{ chargingDetailId: '1', amount: 90, description: '騎乘費', allowedPayway: { credit: true } },
			{ chargingDetailId: '2', amount: 10, description: '安心保障服務費', allowedPayway: { credit: false } },
		  ]
		}
		
		const result5 = genPaywaysForRentAndInsurance(mockOrder5)
		console.log('=====> result5: ', result5)
		const expectedResult4 = {
		  rent: {
			payWays: [{ payWay: 'applePay', amount: 90 }]
		  },
		  insurance: {
			payWays: [{ payWay: 'applePay', amount: 10 }]
		  }
		}
		assert.deepStrictEqual(result5, expectedResult5, 'The objects should be deeply equal')
		
		// 情境6
		// 訂單總金額 100
		// 沒有 credit
		// 沒有     
		// 跟上面不同，用 applePay
		const mockOrder6 = {
		  amount: 100,  // 訂單總金額
		  details: [
			{ payway: 'applePay', amount: 100 },
		  ]
		}
		
		const result6 = genPaywaysForRentAndInsurance(mockOrder6)
		console.log('=====> result6: ', result6)
		const expectedResult4 = {
		  rent: {
			payWays: [{ payWay: 'applePay', amount: 100 }]
		  }
		}
		assert.deepStrictEqual(result5, expectedResult5, 'The objects should be deeply equal')
		
		function genPaywaysForRentAndInsurance(
		  order: {
			amount: number
			details: {
			  payway: string
			  amount: number
			}[]
			orderItems?: {
			  chargingDetailId: string
			  amount: number
			  description: string
			  allowedPayway: { credit: boolean }
			}[]
		  }
		
		) {
		
		//... to complete
		
		}
		
		````
		
		符合以下條件，請根據上面的資訊，幫我完成genPaywaysForRentAndInsurance 函數。
		
		- 訂單總金額 amount 會優先使用 credit 這個 payway 支付, 然後才會去一個一個檢查其他 payway 是否有對應的支付
		- orderItems 可以透過 description 知道是哪一種費用, 裡面的 allowedPayway 的 credit 需要是 true, 才可以用 credit 這個 payway
		- 預設 credit 會先支付，然後才會用其他的 payway, 用哪一種payway, 看 details
		- 如果 argument input 沒有 orderItems 資料 -> 全部都歸於 rent
		- details.payway 可能包含的 paywayType 參考 paywayType interface
		
	
	```tsx fold
	
	for payway in payways:
		payway=credit:
			add rentAmt to rentPayway
			add amt-rentAmtt to milagePayway
		payway=other
			add insuranceAmt to insurancePayway
			add amt-insuranceAmt to rentPayway
	
	```



-----


- 如果我有 credit 100, 那我租車 120, 會強迫我用掉100還是我可以選?
	
	- 沒有看到 user 有地方可以選 → 應該是app自動幫你先扣掉騎乘金
		
	- 類似如果18元的騎乘費用 ⇒ 會先扣掉 100-82 → 18
		
	- 會建立一個order
		
			![[CleanShot 2024-05-11 at 11.29.30.png | 300]]
		
	- order receipt 有兩個
			![[CleanShot 2024-05-11 at 11.29.51.png]]



----

一個 order 會有多個 receipt?
- yes, 不少！ → 根據不同payway, 就有幾個
- 目前只會有 一個 credit + 其他


```sql fold
SELECT or2."OrderNo", COUNT(*) AS count
            FROM postgres.ceres."OrderReceipts" or2 
            WHERE or2."TransactionTime"  >= '2024-01-01 12:26:12.650'
            GROUP BY or2."OrderNo" 
            HAVING COUNT(*) >= 2;
```


3 is credit
6 is apply pay
![[CleanShot 2024-05-11 at 11.31.56.png]]

全部 amt = 33
        
兩個 invoice
        
21 and 12
        
21 是 apply pay
        
12 是 credit
        
orderType 0 是租車
![[CleanShot 2024-05-11 at 11.32.17.png | 300]]
        
        所以我可以選擇套用 credit



----

    - [x] 如果沒有 orderItems 資料 ->訂單金額等於rent金額