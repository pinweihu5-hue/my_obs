
like this
```sql

SELECT * FROM item WHERE id IN (1, 2, 3)  --- IN operator 
SELECT * FROM item WHERE id = 1 OR id = 2 OR id = 3   --- OR operator 

```
上面的比較 (predicate) 越多，速度差異越大
IN 比較快

why?

IN use **idx scan** 
OR use **bitmap indx scan**, 還需要建立 bitmap (每一個 predicate都需要去建立) 才去 idx scan, 因此花的比較久。






ref: [Query best practices: When should you use the IN instead of the OR operator? | OtterTune](https://bohanzhang.me/assets/blogs/in_or_query/in_or_query.html)