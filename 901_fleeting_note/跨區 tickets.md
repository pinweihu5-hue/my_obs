
[[_wemo_tickets]]

---


# iOS QAT can not return issue

BE 資料面多了 reginal opertator 的 type
有一個沒注意到的 API
/v24/scooters/AEA-3011/zones/current
會返回 layerType.name
其中IOS 處理的邏輯是，如果他發現這個 layerType不是他認識的，一律算forbion, 不給過

--

目前IOS無法改，改了那沒強更的人一樣無法還車
只能 BE 做相容處理
BE 需要會filter out 這個欄位 return reginal opertator type

->
不然就要強更


```ts
    return {
      type: layer.layerType.name || '',
      title: zone.i18nTitle && zone.i18nTitle[lang] ? zone.i18nTitle[lang] : '',
      description: zone.i18nRentingDescription && zone.i18nRentingDescription[lang] ? zone.i18nRentingDescription[lang] : '',
      shapeName: zone.name || layer.name,
      canReturn: Boolean(types[layer.layerType && layer.layerType.name]),
      requiringPhoto
    }

```




# todo

- [x] add ppl into chat
- [x] test 改為 qat 的脈絡
- [x] qat 測試一下是否可以 work
- [x] check jack MR since my MR reply on his db part
- [x] think about 有哪些可能可以改進的
	- [x] 2 層 forEach
- [ ] think 10 min for use case secerio -> [[take more time to think about use case - this is everything]]
- [ ] ask Jack to check first
- [ ] check jack another MR [[jack 跨區 mr]]
- [ ] 手動 test the feature before merge into the dev



fail test
- [x] FAIL test/api/v2/users/me/rents/return/return.test.js -> need to check err case, can not just fix it
- [x] FAIL test/api/v2/users/service/rents/index.test.js 
- [x] FAIL test/api/v2/users/me/rents.test.js -> need to check err case, can not just fix it
- [x] FAIL test/api/v2/users/me/rents/return/freeDistTimePlan.test.js
- [x] FAIL test/api/v2/users/me/rents/return/vip_plan.test.js
- [x] FAIL test/api/v2/users/me/rents/return/returnPowerOffCheck.test.js
- [x] FAIL test/db/scooter.test.js




# link
- pm doc
- rent [rent](https://app.asana.com/0/1206177962100119/1207535528739793/f)
- return [return](https://app.asana.com/0/1206177962100119/1207535528739795/f)
- jack study [CodiMD - Collaborative markdown notes](https://note.wemoscooter.com/wzJXhfaBROmcrp5IxCkpRA?both)
- DB migraiton Jack [migraiotn MR](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5174)
- [import zone/kml](https://docs.google.com/document/d/1V-mdH-RE0aiDa4OURc-LMwMeUpk5zF61KNAutXUEvM0/edit#heading=h.tchnu8vw3she)
- [[import zone kml]]



# perf improve

```ts title:check if the scooter position is in any of the zones


// ver1

let isInZone = false

const regionalOperatorZones = regionalOperatorLayers?.flatMap((layer) => layer.zones).filter((zone) => zoneIds.includes(zone.id))

 regionalOperatorZones.forEach((zone) => {
   zone.polygons.forEach((polygon) => {
	 const position = turfPoint([lng, lat])
	 const targetPolygon = turfPolygon(polygon)
	 if (booleanPointInPolygon(position, targetPolygon)) {
	   isInZone = true
	 }
   })
 })

return isInZone




// ver2

let isInZone = false
const regionalOperatorZones = regionalOperatorLayers?.flatMap((layer) => layer.zones)
const zonesByIdMap = R.groupBy((zone) => String(zone.id), regionalOperatorZones)
zoneIds.forEach((zoneId) => {
  const polygons = R.pluck('polygons', zonesByIdMap[zoneId]).flat(2)
  const position = turfPoint([lng, lat])
  const targetPolygon = turfPolygon(polygons)
  if (booleanPointInPolygon(position, targetPolygon)) {
	isInZone = true
  }
})

return isInZone


// ver3

const regionalOperatorZones = regionalOperatorLayers?.flatMap((layer) => layer.zones)
const zonesByIdMap = R.groupBy((zone) => String(zone.id), regionalOperatorZones)
const isInZone = zoneIds.some((zoneId) => {
  const polygons = R.pluck('polygons', zonesByIdMap[zoneId]).flat(2)
  const position = turfPoint([lng, lat])
  const targetPolygon = turfPolygon(polygons)
  return booleanPointInPolygon(position, targetPolygon)
})

return isInZone








```




# current DB
- 綠色是這次需求新建立的
- 虛線表示關聯需要透過 application level 去自己 mapping
```plantuml
  package Hermes {
    entity Manager {
      email: string
      user_id: number
      isAdmin: boolean
      idDeleted: boolean
      tenantId: number
    }
	entity regional_operator_zone #lightgreen {
	  id: number
	  regionalOperatorId: number
	  zoneId: number
	}

    entity ManagerGroup {
      id: number
      name: string
      description: string
    }

    entity RegionalOperator #lightyellow {
      id: number
      name: string
      description: string
    }

    entity MapLayerConfig {
      id: number
      layerId: number
      requirePhoto: boolean
    }

    entity scooter {
      scooterId: string
      scooter_group_id: number
    }

    entity ScooterGroup {
      id: number
      name: string
      description: string
      styleId: number
    }

    entity ScooterGroupZone {
      id: number
      scooterGroupId: number
      zoneId: number
      layerId: number
    }

    entity user {
      id: number
      userCategoryId: number
    }

    entity RidingPlan{
      userCategoryId: number
      scooterGroupId: number
    }

    Manager ||-o| ManagerGroup
    ScooterGroup ||-u-o{ scooter
    ScooterGroup ||--o{ ScooterGroupZone
    RegionalOperator ||--o{ scooter
    user ||--o{ RidingPlan
    RidingPlan }o--|| ScooterGroup
  }

  package Atlas {
    entity point #pink {
      id: number
      zoneId: number
    }
    note bottom
    not in-use
    end note

    entity zone {
      id: number
      desc: string
      polygons: json,
      polygonsBorderColor: string
      polygonsFilledColor: string
    }

    entity layer {
      id: number
      name: string
      desc: string
      typeId: number
      mapId: number
      zIndex: number
      beginAt: datetime
      endAt: datetime
      isDeleted: boolean
      ...
    }

    entity layerType #lightyellow {
      id: number
      name: string
    }

    entity map {
      id: number
      name: string
      desc: string
      identity: string
      isDeleted: boolean
      ...
    }

    layerType ||--o{ layer
    map ||--o{ layer
    layer ||--o{ zone
    zone ||--o{ point
  }

  package Mnemosyne {
    entity ManagerGroupMappingOfficeAndRegionalOperator {
      id: number
      managerGroupId: number
      officeId: number
      regionalOperatorId: number
    }

    entity office_location {
      id: number
      name: string
      city: string
      district: string
      address: string
      country: string
      tz: string
      lat: number
      lng: number
    }

    office_location ||--o{ ManagerGroupMappingOfficeAndRegionalOperator
  }

  layer ||-o{ MapLayerConfig
  zone ||-o{ ScooterGroupZone
  layer ||-o{ ScooterGroupZone
  RegionalOperator ||-u-o{ ManagerGroupMappingOfficeAndRegionalOperator
  ManagerGroup ||-u-o{ ManagerGroupMappingOfficeAndRegionalOperator
  RegionalOperator ||--o{ regional_operator_zone
  regional_operator_zone ||..|| zone
}
```




這個men上的表，把三個表連在一起
把 regionalOperator, ManagerGroup and officeId 三個表連在一起
![[IMG-跨區 tickets-20241003104934126.webp]]




這邊有一個由內往外的關連性

![[IMG-跨區 tickets-20241003104948965.webp]]


這邊有另一個表 scooter group zone 把 SG, layer and zone 關連起來
![[IMG-跨區 tickets-20241003105013272.png]]



map -> 比較像是用 biz group 區分，但也有例外
- 跟layer 比，感覺就是更抽象的 (make sense)
- ex
	- 正式營運地圖
	- tern
	- wemo..
- 類似用不同 biz group 區分
- 停車場也在這裡是一個map
- 紅標車也是一個map

![[IMG-跨區 tickets-20241003105013372.png]]

Layer
- 感覺就是分區域
- 也不是依照行政區分
- 也有街，也有路
- 也有特定區域，類似總統府禁停
![[IMG-跨區 tickets-20241003105039706.png]]

layerType
類似表示這個layer 的權限
```
id| name               |
--|---------------------|
 1|OPERATION            |
 2|FORBIDDEN            |
 3|WARNING              |
 4|WARNING_PRIVATE      |
 5|WARNING_PARKING_LOT  |
 6|WARNING_ILLEGAL      |
 7|FORBIDDEN_RENT       |
 8|FORBIDDEN_RETURN     |
 9|NON_ALERT            |
10|ADVERTISEMENT        |
11|FORBIDDEN_RENT_RETURN|
12|PARKING_LOT          |
13|TOW_YARD             |
14|DISTRICT             |
15|RED_TAG_VEHICLE      |
16|ANNOUNCEMENT         |
17|REGION               |
```


不懂為何這邊叫做 ZoneType 但是 db 是layerType?

```ts
export enum ZoneType {
  OPERATION = 'OPERATION',
  FORBIDDEN = 'FORBIDDEN',
  WARNING = 'WARNING',
  WARNING_PRIVATE = 'WARNING_PRIVATE',
  WARNING_PARKING_LOT = 'WARNING_PARKING_LOT',
  WARNING_ILLEGAL = 'WARNING_ILLEGAL',
  FORBIDDEN_RENT = 'FORBIDDEN_RENT',
  FORBIDDEN_RETURN = 'FORBIDDEN_RETURN',
  NON_ALERT = 'NON_ALERT',
  ADVERTISEMENT = 'ADVERTISEMENT',
  OUTSIDE = 'OUTSIDE',
  FORBIDDEN_RENT_RETURN = 'FORBIDDEN_RENT_RETURN',
  PARKING_LOT = 'PARKING_LOT',
  TOWING_YARD = 'TOW_YARD',
  DISTRICT = 'DISTRICT',
  RED_TAG_VEHICLE = 'RED_TAG_VEHICLE',
  ANNOUNCEMENT = 'ANNOUNCEMENT'
}

```


zone - 跟layer 比較，更細，到區 level
- 有 polygon了, 還有fill and border 的 color
![[IMG-跨區 tickets-20241003105103743.png]]


scooterGroupZone定義了
SG, 和 layer and zone 的關係
我猜，某個 sg 只可以在特定的 layer and zone操作
![[IMG-跨區 tickets-20241003105124522.webp]]


rideing plan 定義了 user and sg 的關係
![[IMG-跨區 tickets-20241003105134586.webp]]



scooter 一定屬於某個 RO and SG
![[IMG-跨區 tickets-20241003105143406.webp]]







==

 
## Related Features
  - 1 regional operators
    - 1.1 CRUD APIs
      - able to mark deleted only if no scooters
      - Actions:
        - check if relationship to zone exists before deleting


    - 1.2 scooter has a property of regional operator
      - filterable in apollo
      - Actions:
        - new data (new regional operators)

    - 1.3 manager to regional operator ACL (by group)
      - work order
      - dispatch
      - Questions:
        - Is manager ACL for regions still required?
          - if yes, data setup required
          - if no, update logic (sunset ManagerGroupMappingOfficeAndRegionalOperator)
      - Actions
        - Ans: no manager ACL required, should be removed.

    - 1.4 GR monitor
      - support regional operator 1 and 2 only
      - Questions:
        - Is GR Monitor required for new regions?
          - if yes, as Taipei city or other new requirements?
      - Actions:
        - Ans: remain unchanged


    - 1.5 battery configurations
      - eventConfig.type: batmanConfig
      - regional operator id = eventConfig.name
      - Questions:
        - Will Candy be deployed to new regions?
          - if yes, batMan configs are required
      - Actions:
        - Ans: remain unchanged, but notify RD before deploying Candy to new regions.
´
  
  - 2 maps (map/layer/zone)
    - 2.1 appConfig
      - type = MapAnnouncement (地圖蓋板)
      - layerType: ANNOUNCEMENT



    - 2.2 operation (營運區)
      - Questions:
        - show all regional operator at once? or only by location?
      - Actions:
        - add zone to regional mappings for layer types (might have performance issues with more regional operators)



    - **2.3 forbidden rent/return**
      - **Actions:**
        - **add zone to regional mappings for layer types (might have performance issues with more regional operators)**
        - **Ans: no likely to be able to return vehicle in other region. Automatically charging for dispatch will not be considered nor implemented.** 




- 2.4 parking lots
  - layerType: PARKING_LOT
  - Actions:
	- add zone to regional mappings for layer types (might have performance issues with more regional operators)
	- Ans: PM will check with CM/BD. Generally regional operator constrain is higher than parking lot constrain. vehicle can return to parking lots which are within the regional operator of the vehicle. Parking lots out of vehicle regional operator will not be able to return.


- 2.5 tow yards
  - not migrated yet, using hermes.Zone
  - layerType: TOW_YARD (after migration)
  - Actions:
	- migration
	- add zone to regional mappings for layer types (might have performance issues with more regional operators)
	- Ans: provides all tow yards to box. PM check with OPS for usage. migration is not 1st priority but nice to have.


- 2.6 return photo requirement
  - layerType: WARNING
  - MapLayerConfig.requirePhoto: true
  - Actions:
	- add zone to regional mappings for layer types (might have performance issues with more regional operators)

- 2.7 user app
  - 2.7.1 get scooter current zone: api/v2/scooters/service/maps.ts#7
	- for user app return validation
	  - fetch layers by position: api/v2/users/service/maps.ts#125
            
	  ![[IMG-跨區 tickets-20241003105143509.png]]
	  
  - 2.7.2 scooterGroupZone
	- map.identity: WeMo
	- layerId & groupId exist in table



  - **2.7.3 is in renting zone**
	- **call fetch layer by position (2.7.1)**
	  - **if no layers return true**
	  - **if layerType != FORBIDDEN_RENT or FORBIDDEN_RENT_RETURN return true**
	  **(this is the reason why you can rent across regions)**
	- **Actions:**
	  - **check regional operator with zones** 



  - **2.7.4 is in return zone**
	- **call fetch layer by position (2.7.1)**
	  - **if no layers return false**
	  - **if layerType != FORBIDDEN, FORBIDDEN_RETURN or FORBIDDEN_RENT_RETURN return true**
	  **(this is the reason why you can return across regions)**



  - 2.7.5 filter out scooter not for rent
	- map.identity: WeMo
	- layerType: FORBIDDEN_RENT & FORBIDDEN_RENT_RETURN
  - 2.7.6 get maps for users
	- user.userCategory -> ridindPlan.userCategory
	- ridindPlan.scooterGroup -> scooterGroupZone.scooterGroup
	- scooterGroupZone.layer -> atlas.layer
	- Questions:
	  - still show all regional operators?
  - Actions:
	- **rent/return validation change required.**
	- Ans: map layer should be loaded by location. BE check with app for usage.



- 2.8 apollo (service console)
  - show location district
	- layerType: DISTRICT
- 2.9 Tern (bike) regions
  - map.identity: Tenant Name + 'Regions'
  - layerType: REGION
- 2.10 GR
  - layerType: DISTRICT
  - see 1.4
- 2.11 RedTagVehicle
  - layerType: RED_TAG_VEHICLE
  - Questions:
	- candy in new regional operators?
	- red tag for i-one?
	- sunset?
  - Actions:
	- Ans: PM will check and provide final decision.

## APIs

  - api/v2/regionalOperators/index.ts
    - GET /v2/regionalOperators
    - POST /v2/regionalOperators
    - PATCH /v2/regionalOperators/:id
    - *DELETE /v2/regionalOperators/:id

  - layerType
    - GET /v3/layerTypes


  - layer
    - GET /v3/layers
    - GET /v3/layers/:id
    - POST /v3/layers
    - PATCH /v3/layers/:id
    - DELETE /v3/layers/:id

  
  - map
    - GET /v3/maps
    - GET /v3/maps/:id
    - POST /v3/maps
    - PATCH /v3/maps/:id
    - DELETE /v3/maps/:id


  - zone
    - GET /v3/zones/:id (+regional operator ids)
    - POST /v3/zones (+regional operator ids)
    - PATCH /v3/zones/:id (+regional operator ids)
    - DELETE /v3/zones/:id

  - ?GET /v2/users/me/maps
  - GET /v2/scooters/:id/current/zone
  - POST /v2/users/me/rents
  - PATCH /v2/users/me/rents
  

## New Regional Operators
  - Taichung
  - Tainan

## Solutions
  - With a new layer type 
    - layerType: REGIONAL_OPERATOR



## tables

atlas.zones: polygons from cities (Taipei (雙北), KH, Taichung, and Tainan)

| zoneId | name     | layerType         | Polygons          |     |
| ------ | -------- | ----------------- | ----------------- | --- |
| 1      | Taipei   | REGIONAL_OPERATOR | geojson of Taipei |     |
| 2      | KH       | REGIONAL_OPERATOR | ..                |     |
| 3      | Taichung | REGIONAL_OPERATOR | ..                |     |
| 4      | Tainan   | REGIONAL_OPERATOR | ..                |     |
|        |          |                   |                   |     |

- hermes.regional_operator

| id  | name   |
| --- | ------ |
| 1   | Taipei |
|2|KH
|3|Taichung
|4|Tainan



- hermes.regional_operator_zone

| id  | regionalOperatorId | zoneId |
| --- | ------------------ | :----- |
| 1   | 1                  | 1      |
| 2   | 2                  | 2      |
| 3   | 3                  | 3      |
| 4   | 4                  | 4      |



apple | test2 | 
--| -
dd | dd | dd


| 表頭1 | 表頭2 | 表頭3 |
| --- | --- | --- |
| 1-1 | 1-2 | 1-3 |
| 2-1 | 2-2 | 2-3 |
| 3-1 | 3-2 | 3-3 |




regional_operator 可以撈到 多筆zoneId
然後就可以用 zoneId 去 atlas 拿到


  ```plantuml


  package hermes{
	entity regional_operator {
	  id: number
	  name: string
	}

	entity regional_operator_zone #lightgreen {
	  id: number
	  regionalOperatorId: number
	  zoneId: number
	}
  }

  regional_operator ||--o{ regional_operator_zone
  ```
- Is Vehicle in its regional operator?

  ```plantuml
  start
  : get layers with 
	identity WeMo
	and 
	layerType REGIONAL_OPERATOR
	(caching);
  : get regional operator zones of vehicle;
  : check if vehicle is in any zones;
  end
  ```








- Check if a vehicle is in its regional operator before renting and returning
  - 2.7.1
	- provide regional operator check result
  - 2.7.3
	- return false if not in regional operator zones
  - 2.7.4
	- return false if not in regional operator zones
        

## Tasks
  - 0 Polygons for Regional Operations (2)
  - 1 DB migration (2)
    - hermes
      - mapping table between regional operators and zones
    - atlas
      - new layer type
  - 2 updates
    - 2.1 zones: Atlas+Hermes (2 + 2 + 2)
      - 2.1.1 GET /v3/zones/:id (+regional operator ids)
      - 2.1.2 POST /v3/zones (+regional operator ids)
      - 2.1.3 PATCH /v3/zones/:id (+regional operator ids)
    - 2.2 rents: Hermes (3 + 2)
      - 2.2.1 POST /v2/users/me/rents (check for regional operator, reserve)
        - *check regional operator
      - 2.2.2 PATCH /v2/users/me/rents (check for regional operator, return)
        - *check regional operator
  - 3 migrate tow yard to atlas (nice to have) (3 + 3)
    - 3.1 data migration
    - 3.2 api update






# old note
## reserve 的租車zone
api/v2/users/service/rents/reserve.ts:113
const isInZone = await isScooterInRentingZone({ lng, lat }, groupId)

## resnt isZone 相關的
api/v2/users/service/rents/rent.ts:125
const isInZone = await this.isScooterInRentingZone({ lng, lat }, groupId)
## validateReturn 
api/v2/users/service/rents/return.ts:437
const isInZone = await isScooterInReturnZone({ lng, lat }, groupId)

==


# how to do?
==
![[IMG-跨區 tickets-20241003105150708.png|568]]



idenity WeMo (mapId = 1)
還沒 delete
已經開始
現在還沒結束
![[IMG-跨區 tickets-20241003105150818.png]]








# to covert to geojson
[geojson.io | powered by Mapbox](https://geojson.io/#map=14.52/22.58426/120.32441)
example
```json fold
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          [
            [
              120.3300172,
              22.5865305
            ],
            [
              120.3261011,
              22.5838658
            ],
            [
              120.3290838,
              22.5803688
            ],
            [
              120.3331929,
              22.5831822
            ],
            [
              120.3300172,
              22.5865305
            ]
          ]
        ]
      },
      "properties": {}
    }
  ]
}

```




# confuse 的 polygon 結構

3 層 arr
![[IMG-跨區 tickets-20241003105156734.png]]

對應到 schema
也是三個 arr
api/v2/zones/schema/index.ts:7
![[IMG-跨區 tickets-20241003105156866.png]]

不過 DB 會顯示像是 4個
因為多一個 jsonB 那一層
![[IMG-跨區 tickets-20241003105201954.png]]



# qat DB + local test 


local 要連上 atalas
hermes 的 development.js 要加上
```sh

  atlas: {
    origin: 'localhost:10001',
    useSsl: false
  },


```
atals 要打開
```sh

│atlas on  develop [$?⇣] via  orbstack is  v1.11.0 via 🤖 v12.22.12
at 16:07:46 ➜
└─> npm run start:dev

```


預約
POST {{hermes}}/v23/users/me/rents
{
  "scooterId": "ALL-5029",
  "pricingPlanId": "558"
}


res
{
  "result": "success",
  "data": {
    "scooterId": "ALL-5029",
    "rentId": "2464068"
  }
}


租車
(帶上 rentID)
PATCH {{hermes}}/v23/users/me/rents/2464068
{
  "type": 3,
  "rebateVoucherId": "null",
  "pricingPlanId": "558"
}

res
{
  "result": "success",
  "data": {
    "scooterId": "ALL-5029"
  }
}


還車  
這一行要 comment 掉 api/v2/users/controller/rents.ts:204  
validateUserEvent(event)
(帶上 rentID)
PATCH 
{{hermes}}/v23/users/me/rents/2464068

{
  "type": 4,
  "userEvents": [
    {
      "lng": 121.5461966248174,
      "type": "RESERVE",
      "lat": 25.05241788080209,
      "eventAt": "2024-04-22T03:17:59.334Z"
    },
    {
      "lng": 121.54612141611979,
      "lat": 25.052467903717172,
      "type": "RETURN",
      "eventAt": "2024-04-22T03:22:57.968Z"
    }
  ]
}





