


# true, null, false 的 真值表
只要有一個是 null 結果就是 null
![[IMG-null in pg-20250810210636027.png|527]]




# 表示 "不知道"(or unknown), 不是 "沒有值"

We can think of null as meaning I don’t know what than rather than no value here.

因此 `true/false = 不知道`  => 不知道

因為如果 null 表示沒有值，那 null = null -> 應該會是 true




# 替欄位加上 not null 限制
表示這個值一定要有, required

ex
```sql

create table test(
	id serial, 
	f1 text not null default 'unknown'
	);

```




# outter join 會 introduce null

- outer joins are meant to preserve rows from your reference relation and add to it columns from the outer relation when the join condition is satisfed. 
- When the join condition is not satisfed, the outer joins then fill the columns from the outer relation with `null` values.


source: 上面內容主要來自 book, art of pg, SQL toolbox, understanding Nulls



# where x "is" null

 <--  because null = null is null and then it won’t be selected in your resultset:

```sql

select a, b
from XXX
where a = null;  --  上面提到的，因為 null =  null 是 ? -> null, 所以你這樣寫，不會有東西 returned 



-- 你要拿到這些 rows -> 要用
where a is null

```