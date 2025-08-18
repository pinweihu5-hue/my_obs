
ctx
5千萬比，需要從 rent 塞到 rentConfig
還會涉及到 join



原始版本
```sql

WITH tmp1 AS (
   SELECT
      DISTINCT ON (rt."id") rt."id" AS "rentId",
      rt."user_id" AS "userId",
      4 AS "tenantId",
      vs."vehicleId" AS "vehicleId",
      rt."scooter_id" AS "plateNumber",
      prt.id AS "pricingRuleId",
      prt."role" AS "userRoleId",
      rt."timePlanDetailId" AS "timePlanDetailId",
      rt."userVipStatisticId" AS "vipStatisticsId",
      rt."rebateVoucherId" AS "rebateVoucherId",
      rt."reserveUTCAt" AS "queryTime",
      rt."reserveUTCAt" AS "createdAt",
      prt."createdAt" AS "ordercreatedAt"
   FROM
      "hermes".rent rt
      JOIN "hermes"."vehicle_plate" vs ON rt."scooter_id" = vs."licensePlate"
      JOIN "hermes"."pricing_rule_tmp" prt ON prt.pppid = rt."pricingPlanPriceId"
   WHERE
      rt."id" >= 0
      AND rt."pricingPlanPriceId" IS NOT NULL
      AND vs."vehicleId" IS NOT NULL
      AND prt."createdAt" <= rt."reserveUTCAt"
   ORDER BY
      rt."id",
      prt."createdAt" DESC
   LIMIT
      1000
)
INSERT INTO
   "hermes"."rent_config" (
      "rentId",
      "userId",
      "tenantId",
      "vehicleId",
      "plateNumber",
      "pricingRuleId",
      "userRoleId",
      "timePlanDetailId",
      "vipStatisticsId",
      "rebateVoucherId",
      "queryTime"
   )
SELECT
   "rentId",
   "userId",
   "tenantId",
   "vehicleId",
   "plateNumber",
   "pricingRuleId",
   "userRoleId",
   "timePlanDetailId",
   "vipStatisticsId",
   "rebateVoucherId",
   "queryTime"
FROM
   tmp1;

```



優化1
拿掉 2 step 
原始版本需要建立 tmp 表，如果資料量很大，那會很慢， mem 放不下喔
```sql

insert into
   "hermes"."rent_config" (
      "rentId",
      "userId",
      "tenantId",
      "vehicleId",
      "plateNumber",
      "pricingRuleId",
      "userRoleId",
      "timePlanDetailId",
      "vipStatisticsId",
      "rebateVoucherId",
      "queryTime"
   )
select
   distinct on (rt."id") rt."id" as "rentId",
   rt."user_id" as "userId",
   '4',
   vs."vehicleId" as "vehicleId",
   rt."scooter_id" as "plateNumber",
   prt.id as "pricingRuleId",
   prt."role" as "userRoleId",
   rt."timePlanDetailId" as "timePlanDetailId",
   rt."userVipStatisticId" as "vipStatisticsId",
   rt."rebateVoucherId" as "rebateVoucherId",
   rt."reserveUTCAt" as "queryTime"
from
   "hermes".rent rt
   inner join "hermes"."vehicle_plate" vs on rt."scooter_id" = vs."licensePlate"
   and vs."vehicleId" is not null
   inner join (
      select
         *
      from
         "hermes"."pricing_rule_tmp" jprt
      order by
         jprt.pppid asc,
         jprt."createdAt" desc
   ) prt on prt.pppid = rt."pricingPlanPriceId"
   and prt."createdAt" <= rt."reserveUTCAt"
where
   rt."id" >= 50000
   and rt."pricingPlanPriceId" is not null
order by
   rt."id" asc
limit
   1000

```


優化2


- 把要 join 的表用 sub query 先 select 出必要的資訊後再進行 join -> 可以大幅減少很多 col 的資料量
	- 減少很多 col 的選取 in prt 表 (想一下，join 後會變超級多)
```sql

insert into
   "hermes"."rent_config" (
      "rentId",
      "userId",
      "tenantId",
      "vehicleId",
      "plateNumber",
      "pricingRuleId",
      "userRoleId",
      "timePlanDetailId",
      "vipStatisticsId",
      "rebateVoucherId",
      "queryTime"
   )
select
   distinct on (rt."id") rt."id" as "rentId",
   rt."user_id" as "userId",
   '4',
   vs."vehicleId" as "vehicleId",
   rt."scooter_id" as "plateNumber",
   prt.id as "pricingRuleId",
   prt."role" as "userRoleId",
   rt."timePlanDetailId" as "timePlanDetailId",
   rt."userVipStatisticId" as "vipStatisticsId",
   rt."rebateVoucherId" as "rebateVoucherId",
   rt."reserveUTCAt" as "queryTime"
from
   "hermes".rent rt
   inner join "hermes"."vehicle_plate" vs on rt."scooter_id" = vs."licensePlate"
   and vs."vehicleId" is not null
   inner join (
      select
         jprt.id,
         jprt.role,
         jprt."createdAt",
         jprt.pppid
      from
         "hermes"."pricing_rule_tmp" jprt
      order by
         jprt.pppid asc,
         jprt."createdAt" desc
   ) prt on prt.pppid = rt."pricingPlanPriceId"
   and prt."createdAt" <= rt."reserveUTCAt"
where
   rt."id" >= 0
   and rt."pricingPlanPriceId" is not null
order by
   rt."id" asc
limit
   1000

```




優化3

partition rent, 1000, 1000 個拉出來處理
```sql


insert into
   "hermes"."rent_config" (
      "rentId",
      "userId",
      "tenantId",
      "vehicleId",
      "plateNumber",
      "pricingRuleId",
      "userRoleId",
      "timePlanDetailId",
      "vipStatisticsId",
      "rebateVoucherId",
      "queryTime"
   )
with rentPartition as (
  select 
      frt."id", 
      frt."user_id", 
      frt."scooter_id", 
      frt."timePlanDetailId", 
      frt."userVipStatisticId", 
      frt."rebateVoucherId", 
      frt."pricingPlanPriceId",
      frt."reserveUTCAt"
  from "hermes".rent frt 
  where frt."id" >= 0
  and frt."pricingPlanPriceId" is not null 
  order by frt."id" asc 
  limit 1000
)
select distinct on (rt."id") 
    rt."id" as "rentId",
    rt."user_id" as "userId",
    '4',
    vs."vehicleId" as "vehicleId",
    rt."scooter_id" as "plateNumber",
    prt.id as "pricingRuleId",
    prt."role" as "userRoleId",
    rt."timePlanDetailId" as "timePlanDetailId",
    rt."userVipStatisticId" as "vipStatisticsId",
    rt."rebateVoucherId" as "rebateVoucherId",
    rt."reserveUTCAt" as "queryTime"
from
    rentPartition rt
    inner join "hermes"."vehicle_plate" vs on rt."scooter_id" = vs."licensePlate"
    inner join (
        select
            jprt.id, jprt.role, jprt."createdAt", jprt.pppid
        from
            "hermes"."pricing_rule_tmp" jprt
        order by
            jprt.pppid asc,
            jprt."createdAt" desc
    ) prt on prt.pppid = rt."pricingPlanPriceId"
  where prt."createdAt" <= rt."reserveUTCAt"
order by
    rt."id" asc
limit
    1000

```




優化4  tmp 表 上 idx
```sql


insert into
   "hermes"."rent_config" (
      "rentId",
      "userId",
      "tenantId",
      "vehicleId",
      "plateNumber",
      "pricingRuleId",
      "userRoleId",
      "timePlanDetailId",
      "vipStatisticsId",
      "rebateVoucherId",
      "queryTime"
   )
with rentPartition as (
  select 
      frt."id", 
      frt."user_id", 
      frt."scooter_id", 
      frt."timePlanDetailId", 
      frt."userVipStatisticId", 
      frt."rebateVoucherId", 
      frt."pricingPlanPriceId",
      frt."reserveUTCAt"
  from "hermes".rent frt 
  where frt."id" >= 0
  and frt."pricingPlanPriceId" is not null 
  order by frt."id" asc 
  limit 1000
)
select distinct on (rt."id") 
    rt."id" as "rentId",
    rt."user_id" as "userId",
    '4',
    vs."vehicleId" as "vehicleId",
    rt."scooter_id" as "plateNumber",
    prt.id as "pricingRuleId",
    prt."role" as "userRoleId",
    rt."timePlanDetailId" as "timePlanDetailId",
    rt."userVipStatisticId" as "vipStatisticsId",
    rt."rebateVoucherId" as "rebateVoucherId",
    rt."reserveUTCAt" as "queryTime"
from
    rentPartition rt
    inner join "hermes"."vehicle_plate" vs on rt."scooter_id" = vs."licensePlate"
    inner join "hermes"."pricing_rule_tmp" prt on prt.pppid = rt."pricingPlanPriceId" and prt."createdAt" <= rt."reserveUTCAt"
  where prt."createdAt" <= rt."reserveUTCAt"
order by
    rt."id", prt."createdAt" desc





CREATE INDEX pricing_rule_tmp_pppid_idx ON hermes.pricing_rule_tmp USING btree (pppid, "createdAt" DESC);


```