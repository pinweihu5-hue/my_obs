
# 實驗 repeatable_read_level vs serializable level

when we run below:
```sql

-- begin tx1


begin;

select id, name
form t1
where score = (select Max(score) from t1)

-- but not commit yet

-- begin tx2:
do something that will change the max score


-- run tx1 again
--we will see different result of tx1


```


this is what we will see in repeatable_read_level
repeatable_read_levelonly help us to make the `row` repeatable
but in this case, we change something else, and have the side effect to make the tx1 change the query result

if we want to let tx1 maintain the same result during the tx
need to use pg `serializable` level
