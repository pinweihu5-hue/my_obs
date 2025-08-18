



- [ ] see clarance MR [Sign in · GitLab](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5317)

編輯價格的 grpc is ready?

給定 pricing ID
可以編輯 給定 pricing ID 下的 pricingRules

那個時候是不是有說過
 update pricing rule
 其實就是 新增



cntroller 設計要弄成 create pricing time line
還是 patch


discuss with Clarance
- [x] add pricing time line
- [x] add insgle pricing API ->  建立卡
- [x] 拉卡

### bk
```ts bk

message AddPricingTimelineRequest {
  string pricingId = 1;
  string beginAt = 2;
  repeated PricingRule pricingRules  = 3;

  message PricingRule {
    int32 role = 1;
    wemo.common.I18n i18nName = 2;
    int32 algorithm = 3;
    Configs configs = 4;
  }

  message Configs {
    int32 initialMin = 1;
    int32 initialMinPrice = 2;
    int32 pricePerMin = 3;
  }
}


message AddPricingTimelineResponse {
  string result = 1;
  common.PricingTimeline data = 2;
}

```




### 可能todo
- [ ] 如果我們想要把最新的一筆放前面來顯示，這邊應該要調整一下 sort 的邏輯
return Array.from(pricingIdToTimeLineMap.values()).sort((a: {


## study nexus code



todo for create price
- end point to let nexus call
	- check jack mr to see what is looks lik
- core moduel to call PS / VS
	- call PS to create pricing into PS
	- `@GrpcMethod('PricingService', 'AddPricing')`
- call VS to create tag and related tag_action
	- what is the name for this tag?
		- 感覺需要用一個 convension 去建立, like `pricingId_${pricingId}_$(tenantId)`ogle.com/document/d/1OKaolJucGfUljCSL27Sk1MhYUFcw7r-4q7EvQwdL_go/edit?tab=t.0#heading=h.1oem2u5w22a3)



- [ ] 可以改為用 enum 的改為 enum, in interface and zod



## planning


### hermes code

Hihi KB,
我們預計星期二 release vehicle Service
Josh 說之前有跟你提到我們下星期二想要上VS, 看看有哪些我們要提供，哪些工作要做
- like vehicle service 的 prod DB, DNS (wemo infra), LB, ingress 等設定, argoCD?等

- VS release work
	- data migration
		- tag 相關的都先不用上，先上 vehicle 相關的就好
	- 建車
	- 改車
	- 跟KB 說，我們下星期二想要上VS, 看看有哪些我們要提供，哪些工作要做
		- VS service DB
		- DNS (wemo infra), LB, ingress 等設定
		- argoCD?等


- tag and tagAction 都有 -> 直接拿 (拿同一個pricdeId, 綁第二次)

- tag deleted 但 tagAction 還在-> 建立新的 tag and tag Action
- tag and tagAction 都沒有 -> 建立新的
- tagAction deleted 但 tag 還在
	- tx 失效
	- 錯誤呼叫 grpc 只呼叫 tagActions deleted
	- 這些車子已經沒有 tag了
	- 兩個方法:
		- 建立新的? -> this
		- 只重建 tagActions?




先跟 KB 說下星期二我們想要上 VS
- production DB need to create
- argoCD




== discuss with J


- system tag
- 新建立一個欄位來區別 system-gen and managerCreated
	- 應該要建立
	- 欄位類型
		- isSystemCreated: true or false
	- 還是要更有擴充性
		- tagType: enum
			- systemCreated: 0
			- manager: 1
			- 未來還會有其他用途才會需要這樣搞
- 系統建立的話，是否需要填上 i18n 欄位?
	- 未來系統建立(code-gen的需要會顯示在 UI 上)
		- 如果有需要 manager 去管理才要，但是如果一開始就是系統自動建立的，manager 不應該去管理，應該要切開會比較單純吧?
	- 應該不需要吧? -> set nullable
	- 







- about 2pc
	- 最理想的情況下是，我們有一個機制，如果整包沒有做完，那應該就不要 commit
		- 整包這邊是說 addPricing and addTagAndTagAction
		- 不過這邊需要某個機制, like 2PC
	- 目前還沒有這個機制，因此我們就需要手動去處理
	- 這邊會讓資料不一致的情況是
		- add pricing 成功但是 addTagAndTagAction 失敗
		- 這邊要讓整個噴掉
			- 因此使用者需要重新操作，建立新的 pricing and 建立新的 tag and tag action
		- 需要判斷這個資料不一致的情況會不會影響系統運作
			- 會多一個 pricingID
			- 不會影響系統運作
			- 因此如果機率不高，就可以後續再慢慢清理










branchName:
feat/core-nexus-create-pricing

proto 要用我目前開發的那個  (除非我 merge 就可以用 feat/core-pricing)
feat/create-tag-and-tagAction-for-pricing


要呼叫兩個 grpc
- ps:
	- `@GrpcMethod('PricingService', 'AddPricing')`
- vs

```
	- @GrpcMethod('TagActionService', 'AddTagAndTagAction')
		- async AddTagAndTagAction(request: AddTagAndTagActionRequest): Promise<AddTagAndTagActionResponse> {
	使用jack 的 logger, error handleing

```




- will use this middleware
	- `import { requireManagerTenant } from '../utils/middlewares/requireManagerTenant'`
	- how to use, put it into pre controller
		registerAuthEndpoint(table, {
		  version: 24,
		  method: 'delete',
		  endpoint: '/',
		  preController: [requireManagerTenant],
		  controller: { manager: controller.deleteVehicleTags }
		})
	- service level to call coreModule
- 測試可以參考
	- test/api/v2/vehicleTags.test.ts:69







### create tag and tag action




```ts

    await this.tagRepo.manager.transaction(async (manager) => {
      const updates = []
      if (!R.isEmpty(targetTagIds.tagActionIds)) {
        updates.push(this.tagActionRepo.dropTagActions(tenantId, targetTagIds.tagActionIds, manager))
      }
      if (!R.isEmpty(targetTagIds.vehicleModelTagIds)) {
        updates.push(this.vehicleModelTagRepo.dropVehicleModelTags(tenantId, targetTagIds.vehicleModelTagIds, manager))
      }
      if (!R.isEmpty(targetTagIds.vehicleTagIds)) {
        updates.push(this.vehicleTagRepo.dropVehicleTags(tenantId, targetTagIds.vehicleTagIds, manager))
      }
      if (!R.isEmpty(targetTagIds.tagIds)) {
        updates.push(this.tagRepo.dropTags(tenantId, targetTagIds.tagIds, manager))
      }
      await Promise.all(updates)
    })

```


一定要有哪些 req


==

hermes 的 core 會呼叫 vs 的這個我新建的 grpc

系統自己建立一個 tag
- i18nName (hermes 那邊提供)
- tenatId -> hermes provide
- 和這個 tag 對應的 tagAction, 應該要支援可以建立多個，只是我這邊只需要建立一個，這個也是 hemres 那邊建立的時候，塞一個就好
	- tagAction欄位包括
		- tenatId -> hermes provide 
		- zoneId, pricingId, styleId -> 可選
		- pri


哪些是required
tenantId
i18nName
tagActions (arr)



branch name: create-tag-and-tagAction-for-pricing 


Q: tenentId 要如何處理? at hermes side
- nexus 那邊會幫我們處理好，hermes 解掉可以拿到 mgm 那邊的 token
Q: algo 是選單
- 需要 api 可以拿 algo list? -> 先 web hard code

- [ ] 是否要多一個欄位可以幫助 filter out auto-gen 的 tag



![[IMG-WM-608-nexus BE hermes code create and update price-20250810201310945.png]]





## related mr



ben
- createTagAndAction
	- [vs](https://athena.wemoscooter.com/wemo/vehicle-service/-/merge_requests/26)
	- [proto](https://athena.wemoscooter.com/wemo/proto/-/merge_requests/136)
- api renting
	- [hermes](## core-renting-api-compatible)
- core module get pricing
	- [hermes](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5272)


clarance
- get tag 
	- [hermes](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5298)
	- [vs](https://athena.wemoscooter.com/wemo/vehicle-service/-/merge_requests/25)
	- [nexus](https://athena.wemoscooter.com/wemo/nexus-backend/-/merge_requests/82)

jack
- unbind vehicle tag
	- [hermes](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5290)
	- [nexus](https://athena.wemoscooter.com/wemo/nexus-backend/-/merge_requests/81)
- get use role and get use role list
	- [hermes](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5309)
	- [nexus](https://athena.wemoscooter.com/wemo/nexus-backend/-/merge_requests/84)