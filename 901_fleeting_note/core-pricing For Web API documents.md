
- [ ] validate algo -> new card













ask amber
![[IMG-core-pricing For Web API documents-20241003104933840.png]]
ans: 這個最後會顯示在 user app 上，因此需要 i18n name
如果以後多更多語言，的確是需要讓manager 填入更多語言，只是ui 可能會調整




josh
回給 web **先回 i18n** 還是回 1? -> 回 1  -> web 自己處理
然後 core module 處理?
==
![[IMG-core-pricing For Web API documents-20241003104948780.png|720]]


algo 需要 i18n -> 回 1?  => web harcode -> 反正都本來加上一個 algo 都會需要加
rentFlow 需要i18n -> 回 1? -> web harcode -> 前端一開始就分兩頁了
role 回 1? 
回1 就好
另外開 API 讓 web 去問
需要 i18n?  -> 不需要 i18n -> 因為是內部使用
回 enum 但web要如何顯示 -> hermes(userRole 表在hermes) 開 API 去讓 web 問 
![[IMG-core-pricing For Web API documents-20241003105001839.png|502]]



==

如果目前只有一個租借模式，只是顯示用




如何把一個 price ID 綁在一台車上
- for each vehicle
	- 建立 tag and tag action
	- 建立 vehicleTag

ref:
![[IMG-core-pricing For Web API documents-20241003105019049.png]]
![[IMG-core-pricing For Web API documents-20241003105045026.png]]


- 需要建立 vehicleTag





# note

- 不同 tenent 要有自己的 price, 類似威翔和威摩


- 價格不需要一個生效時間(這個目前不做，實際上控制是透過tag的時間控制)  
	- 目前 timeline 是當做 系統控制歷程，未來如果想要透過 pricing 預排，再說，目前車子用那個價格的時間區段，會由vehicleTag 上的 enabledAt 和 deprecatedAt 控制
	- 一開始新增，就是押上 enabledAt and deprecatedAt
	- 當我們編輯時解綁，就是 update deprecatedAt
	- 圖 -> [[enabledAtAndDeprecatedAt.excalidraw]]



- [ ] tenent id 的機制?
- [x] 問一下，會有 生效時間 **beginAt**
- [x] 想一下 patch
- [x] **pricingTimelines** 不需要給一個 arr 吧?

如何綁車?
給定 priceId -> 綁在一個車子上



![[IMG-core-pricing For Web API documents-20241003105111095.png]]

 ![[IMG-core-pricing For Web API documents-20241003105127619.png]]

1. fleet => nexus backend => hermes => ps/vs

- [ ] check nexus backend -> goal, how to add below API

- 用 restful 去想，add 就是 add, 不要一個操作做很多事情

1. tag
   1. list
   2. bind
   3. unbind
2. pricing (PS)
   1. add
   2. list

- pricingService

  - AddPricing
  - GetPricing
  - ListPricing
  - GetPricingRulesAtQuote
  -

- hermes::coreModules::vehicleService
  - addVehicle
  - getVehicleWithTags
  - getVehicleByPlateWithTags
  - updateVehicleLicensePlateById
  - updateVehicleLicensePlateByPlate

[Figma](https://www.figma.com/design/vemT4ioLLPe8uU6Yj8bArh/%F0%9F%93%82-Nexus-%E5%BE%8C%E5%8F%B0?node-id=32-579&node-type=CANVAS&t=U5kUnFZHAKio8qXP-0)

---

note:

- 加車(theBrandNew), patch 車(patchScooterById), 換車牌(changePlate) 會先暫時活在 apollo
