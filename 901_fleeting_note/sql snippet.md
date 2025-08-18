[[db_idx]]


```sql

SELECT s.scooter_id , s."vehicleModelId" , b.pwd, b.box_id FROM scooter s
JOIN box b ON b.box_id = s.box_id
WHERE b.pwd IS NOT NULL
AND s."vehicleModelId" = 2
LIMIT 10;

```