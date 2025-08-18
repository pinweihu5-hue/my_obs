
related:
- [[pricing 主表 -> pricing timeline -> pricing config]]
- [[core-GET-v24 users me renting-compatible]]
- [[design doc on Lock quotes time in redis]]
- [車輛計價與設定流程 的副本](https://docs.google.com/document/d/1uKJ7eYIcVkavO9m-eh9YpArih3kgpRCu1d8lY4l44Ao/edit?tab=t.0#heading=h.wcsw6u51ymzo)
- [[wemo pricing 關鍵測試例子]]

link:
- [release plan](https://docs.google.com/document/d/1bhDFHXSyfV1z4OYfksfisqFqFGfPnjwKwW5BqoU9OMQ/edit?tab=t.0)
- [releae plan bk on my doc](https://docs.google.com/document/d/1a3LdYOm2LoxYtyx9sm2yX7tVVqog2jfctpQIaxzeN2c/edit?tab=t.0)



# lessson learned (ll)
- 不要模擬兩可，要確定，要確定，要確定
- 不要用name join, 不要拿任何使用者決定的欄位去 join -> 用id
- 還要是花時間搞懂邏輯，不然你無法接續下去
- 如果覺得不妥還是得說，不是覺得"先這樣好了"
- join a lot of tables, 自己要知道一對一，一對多，展開，那你要如何處理？因為假設你最後要 select 出來，塞到一個表上
	- 類似 rent Config 的 unique 是 rentID, 從 rent 表塞
	- 然後 rent join other table, 會造成 很多 rent ID, 那請問你要怎麼對應到一個 rent id 然後再塞到rentConfig 上
	- 這是你要思考的
- [[rentConfig 拉出的表的優化寫法]]




release note
- [ ] 會先上，但是停機那天，要確保 pricing 沒人新增
	- [ ] 或是先跟 mkt 說好某個區間不要新增 pricing




# uc -> rp -> sg
![[IMG-wm-1078-core pricing migration-20250810201310958.png|679]]

UC
- 1 對 1 for useRole this 階段
- 69個 role
![[IMG-wm-1078-core pricing migration-20250810201312967.png|961]]

RP
算是 UC and SG 的 join 表
![[IMG-wm-1078-core pricing migration-20250810201314891.png]]


SG
綁在 scooter 上，控制 scooter
![[IMG-wm-1078-core pricing migration-20250810201315905.png]]




---

# UC -> pp -> PPP -> PG

![[IMG-wm-1078-core pricing migration-20250810201316772.png|674]]


```sql fold

SELECT 
pg.id AS PG_id,

pp.id AS PP_id,
pp.name AS pricing_title,


ppp.id AS PPP_id,
ppp."isActive" AS PPP_isActive,

uc.id AS UC_id,
uc.description AS UserRole_tbd, 

ppp."startPrice" AS pr_config1,
ppp."freeMinutes" AS pr_config2,
ppp."pricePerMinute" AS pr_config3,
ppp."i18nDescription" AS pr_i18nName

FROM hermes."UserCategory" uc
JOIN hermes."PricingPlan" pp ON pp."userCategoryId"  = uc.id 
JOIN hermes."PricingPlanPrice" ppp ON ppp."pricingPlanId"  = pp.id 
JOIN hermes."PricingGroup" pg ON pg.id = ppp."pricingGroupId" 
WHERE uc.id IN (1, 2)



```



# 整合

[Figma](https://www.figma.com/board/4MD4T4IegKTKPRN3RdJGE5/my?node-id=0-1&node-type=canvas&t=vlqRXPAjuCEq8Uv3-0)

![[IMG-wm-1078-core pricing migration-20250810201317570.png]]


- [ ] cv
- [ ] gmail

先建立 pricing

pricing
- pricingPlan:title -> pricing:title
- pricingPlan:createdAt ->  pricing:createdAt
- pricingPlan:updatedAt -> pricing:updatedAt
- other, no mapping
	- pricing:tenantId:4
	- pricing:locaiton:1 (taiwan)


pricing_timeline
- pricing_timeline:pricingId -> 上面要建立出來，然後才可以用上面的 pricing ID 填入
- pricing_timeline:beginAt -> pricingPlan:createdAt
- pricing_timeline:updatedAt -> pricingPlan:createdAt


pricing_rule
- pricing_rule:pricingTimelineId -> 上面 ptl 要建立出來，然後才可以用上面的 ptl ID 填入
- ==pricing_rule:role -> 看 hermes 那邊定義的 role ID ->  ut -> pp 下的 ut 的 name
- pricing_rule:algorithm -> 1 (curbBasic)
- pricing_rule:i18nName -> ppp 的 i18nDescription
- pricing_rule:configs (JSONB) -> PPP 內的 startPrice, freeMinutes, pricePerMinute
- pricing_rule:created:pricingPlan:createdAt


---


PP
價格主表
![[IMG-wm-1078-core pricing migration-20250810201317691.png]]

PPP 表
PP -> 多個 PPP
價格參數
![[IMG-wm-1078-core pricing migration-20250810201318116.png]]


PG
綁在 scooter 上
![[IMG-wm-1078-core pricing migration-20250810201318236.png]]


ut -> pp (除了一筆資料例外，剩下都是 1 -> 1)
pp -> 多筆 pg



---

上面這個會變成
-  ??? 個 pricing ID
- 一個是 for normal prcing
	- pricingRule
		- role1: normal user
		- role2: wemo 員工
- 一個是 for redtag
	- pririncRule
		- role1: normal user
		- role2: wemo 員工
- 改 hermes 的 config, 寫死 redTag 在 hermes


流程：
user 會帶上 role, scooter -> 去跟 VS 拿到 pricingID
pricingID 會去 PS 拿到 pricingRule






==

目前系統的表

PP -> 多個 PPP
另外 一個 UserCategory -> 多的 PP
1 UC -> 1 TP

原本系統是如何拿一台車的標價?
- pp join ppp.priceGroupId on scooter.priceGroupId
- pp 是 ppp 的主表
![[IMG-wm-1078-core pricing migration-20250810201318771.png]]
scooter.priceGroupId
![[IMG-wm-1078-core pricing migration-20250810201318935.png]]



# PS pricing 表
![[IMG-wm-1078-core pricing migration-20250810201319163.png]]

# tag -> tag_action, tag -> vehicle_tag
![[IMG-wm-1078-core pricing migration-20250810201319339.png|667]]
![[IMG-wm-1078-core pricing migration-20250810201319538.png|670]]


# useRole 關連表
![[IMG-wm-1078-core pricing migration-20250810201319802.png]]




api/v2/scooters/utils/pricingRuleTransformer.ts
```js
  try {
    const pricingRules = vehicleIdToPricingRules[vehicleId].map((pr) => {
      const pricingRule = pr as unknown as PricingRuleBasicType

      return {
        id: pricingRule.id,
        startPrice: pricingRule.configs?.initialMinPrice || defaultParam.startPrice,
        freeMinutes: pricingRule.configs?.initialMin || defaultParam.freeMinutes,
        pricePerMinute: pricingRule.configs?.pricePerMin || defaultParam.pricePerMinute,
        name: mapI18nToLangString(convertProtoToI18n(pricingRule.i18nName), lang),
        role: pricingRule.role
        isDefault: false,
        isBasic: true,
      }
    })
    return pricingRules
  } catch (error) {
    logger.error({ tag, msg: `arg: ${JSON.stringify(arg)}`, error })
    throw error
  }

```

# mr on pricing hemres compatible
[mr_Feat/core module get pricing](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5272)
[mr_compatible rent](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5287)





# DML
```sql fold


CREATE TABLE pricing (
  "id" BIGSERIAL PRIMARY KEY,
  "tenantId" BIGINT NOT NULL,
  "title" VARCHAR NOT NULL,
  "location" INTEGER NOT NULL,
  "createdAt" TIMESTAMP WITHOUT TIME ZONE NOT NULL DEFAULT now(),
  "updatedAt" TIMESTAMP WITHOUT TIME ZONE NOT NULL DEFAULT now()
);
COMMENT ON COLUMN pricing."location" is 'taiwan = 1, malaysia = 2';
CREATE INDEX idx_pricing_tenantid_location ON pricing("tenantId", "location");



CREATE TABLE pricing_timeline (
  "id" BIGSERIAL PRIMARY KEY,
  "pricingId" BIGINT REFERENCES pricing NOT NULL,
  "beginAt" TIMESTAMP WITHOUT TIME ZONE NOT NULL,
  "createdAt" TIMESTAMP WITHOUT TIME ZONE NOT NULL DEFAULT now()
);
CREATE INDEX idx_pricing_timeline_pricingid ON pricing_timeline("pricingId");




CREATE TABLE pricing_rule (
  "id" BIGSERIAL PRIMARY KEY,
  "i18nName" jsonb NOT NULL,
  "pricingTimelineId" BIGINT REFERENCES pricing_timeline NOT NULL,
  "role" INTEGER NOT NULL,
  "algorithm" INTEGER NOT NULL,
  "configs" JSONB NOT NULL,
  "createdAt" TIMESTAMP WITHOUT TIME ZONE NOT NULL DEFAULT now()
);
CREATE INDEX idx_pricing_rule_pricingtimelineid_role ON pricing_rule("pricingTimelineId", "role");




CREATE TABLE pricing_timeline_log (
  "pricingTimelineId" BIGINT PRIMARY KEY REFERENCES pricing_timeline,
  "pricingId" BIGINT NOT NULL REFERENCES pricing,
  "managerId" BIGINT NOT NULL,
  "name" VARCHAR NOT NULL,
  "email" VARCHAR NOT NULL,
  "createdAt" TIMESTAMP WITHOUT TIME ZONE NOT NULL DEFAULT now()
);
CREATE INDEX pricing_timeline_log_pricing_id_idx ON pricing_timeline_log ("pricingId");

-- DOWN --
DROP TABLE pricing_timeline_log;
































CREATE TABLE daily_type_settings (
  "id" BIGSERIAL PRIMARY KEY,
  "tenantId" BIGINT NOT NULL,
  "location" INTEGER NOT NULL,
  "localDate" TIMESTAMP WITHOUT TIME ZONE NOT NULL,
  "dayType" INTEGER NOT NULL,
  "createdAt" TIMESTAMP WITHOUT TIME ZONE NOT NULL DEFAULT now()
);
COMMENT ON COLUMN daily_type_settings."dayType" is 'weekday = 1, holiday = 2, regularDayOff = 3, makeUpWorkday = 4, bridgeDay = 5';
CREATE INDEX idx_daily_type_settings_tenantid_location_localdate ON daily_type_settings("tenantId", "location", "localDate");

CREATE TABLE time_slots_settings (
  "id" BIGSERIAL PRIMARY KEY,
  "tenantId" BIGINT NOT NULL,
  "location" INTEGER NOT NULL,
  "slotType" INTEGER NOT NULL,
  "beginAt" TIMESTAMP WITHOUT TIME ZONE NOT NULL,
  "endAt" TIMESTAMP WITHOUT TIME ZONE NOT NULL,
  "createdAt" TIMESTAMP WITHOUT TIME ZONE NOT NULL DEFAULT now()
);
COMMENT ON COLUMN time_slots_settings."slotType" is 'nonSpecialPeriod (default) = 1, peakPeriod = 2, offPeakPeriod = 3, subPeakPeriod = 4';
CREATE INDEX idx_time_slots_settings_tenantid_location ON time_slots_settings("tenantId", "location");


```


# bk
```sql fold

SET search_path = 'hermes';

--
--SELECT *
--FROM 'prcingPlanPrice


--SELECT id, "pricingPlanId", "pricingGroupId", 
--"startPrice", "freeMinutes", "pricePerMinute", 
--"isActive", "i18nDescription" 
--FROM hermes."PricingPlanPrice" 
--GROUP BY "pricingGroupId"
--WHERE id IN (40);

--SELECT count(*), "pricingGroupId"
--FROM hermes."PricingPlanPrice" 
--GROUP BY "pricingGroupId"


--SELECT count("isActive"), "pricingPlanId"
--FROM hermes."PricingPlanPrice" 
--GROUP BY "pricingPlanId"

--SELECT count(*), "pricingGroupId", "pricingPlanId"
--FROM hermes."PricingPlanPrice"
--WHERE "isActive" = true
--GROUP BY "pricingGroupId", "pricingPlanId";
--
--
--
--
--SELECT count(*), "pricingGroupId", "pricingPlanId"
--FROM hermes."PricingPlanPrice"
--HAVING 
--GROUP BY "pricingGroupId", "pricingPlanId";



--SELECT 
--    pp."name" AS "pricing_title", 
--    pp."createdAt" AS "pricing_createdAt", 
--    pp."updatedAt"  AS "pricing_updatedAt", 
--    ppp."i18nDescription" AS "pricingRule_i18n", 
--    pp."createdAt" AS "pricingTimeLine_createdAt", 
--    pp."updatedAt"  AS "pricingTimeLine_beginAt", 
--    
--    
--    *
--FROM hermes."PricingPlanPrice" ppp 
--JOIN hermes."PricingPlan" pp ON ppp."pricingPlanId" = pp.id 
--JOIN hermes."UserCategory" uc ON ppp."pricingPlanId" = pp.id 



SELECT 
uc.description AS UserRole, 
sg.name AS sg_name,
pp.name AS pricing_title,
pp.id AS pricing_timeline_pricingId,

*
FROM hermes."UserCategory" uc
JOIN hermes."RidingPlan" rp ON uc.id  = rp."userCategoryId" 
JOIN hermes."ScooterGroup" sg ON sg.id  = rp."scooterGroupId" 
JOIN hermes."PricingPlan" pp ON pp."userCategoryId"  = uc.id 
JOIN hermes."PricingPlanPrice" ppp ON ppp."pricingPlanId"  = pp.id 
JOIN hermes."PricingGroup" pg ON pg.id = ppp."pricingGroupId" 


--UC -> RP -> SG

SELECT  *
FROM hermes."UserCategory" uc
JOIN hermes."RidingPlan" rp ON uc.id  = rp."userCategoryId" 
JOIN hermes."ScooterGroup" sg ON sg.id  = rp."scooterGroupId" 

```