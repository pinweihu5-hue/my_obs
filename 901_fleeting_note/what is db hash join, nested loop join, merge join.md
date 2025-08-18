

# hash join

![[IMG-what is db hash join, nested loop join, merge join-20250810210643480.png]]
- 它會建立一個Hash表，使用匹配的鍵來匹配表中的那些列。
- 優點是Hash JOIN 通常相當快速，但缺點是這個表需要先配置記憶體。
- **當結合大型表或 JOIN 鍵上沒有適當索引時，Hash JOIN 特別有效**。它們可以透過利用Hash表的常數時間查找特性來提供高效的效能。但是，它們需要足夠的記憶體來建立和保存Hash表，因此在記憶體資源有限的情況下，它們可能不是最佳選擇。
- Hash Join只能用于等值连接(如`WHERE A.COL3 = B.COL4`)





# 比較

![[IMG-what is db hash join, nested loop join, merge join-20250810210644160.png]]



- for a small table join, planner usually just choose nested loop
- if the data is lack of idx or condition, planner will also opt to use hash join or merge join (planner do it for you)
- =
    - planner may consider hash join
- >, ≥, <, ≤
    - planner may consider merge join (need to to sort first, like merge sort)



# merge join

![[IMG-what is db hash join, nested loop join, merge join-20250810210645095.png]]

通常情况下 hash join 的效果比 merge join 的效果好
如果源数据上有 idx，或者结果已经排过序，在执行顺序 merge join 时就不需要排序了，这时 merge join 的性能会优于 hash join。

`Merge join`的操作步骤：
1 对连接的每个表做table access full
2 对table access full的结果进行排序
3 进行merge join对排序结果进行合并

`Merge Join`可适于于非等值Join（>，<，>=，<=，但是不包含!=，也即<>）



# Nested loop join


- 巢狀迴圈 JOIN 始終較慢，因為它只會逐一迭代一個表中的資料，然後查找另一個表。
- 速度有多慢取決於表的規模，時間複雜度為 O(n)。
- 但是，在某些情況下，巢狀迴圈 JOIN 可能是最佳選擇：
	- 小型表： 如果要結合的一個或兩個表都很小，則建立和維護其他 JOIN 演算法所需的資料結構的額外負擔可能會超過其好處，類似不需要建立 hash table 使用 hash join等。在這種情況下，巢狀迴圈 JOIN 可能更有效率。
	- 具有選擇性的 JOIN： 如果 JOIN 條件具有高度選擇性，也就是說它會篩選掉大部分的列，則巢狀迴圈 JOIN 可以表現良好。它允許在找不到匹配項時提前終止查找過程，從而減少運算次數。
	- 索引 JOIN： 如果 JOIN 欄位上存在適當的索引，則巢狀迴圈 JOIN 可以利用它們來有效地執行查找。



PS: 
- 資料庫系統使用基於成本的查詢最佳化器，根據表大小、統計資料、可用的索引和系統資源等因素來確定最佳的 JOIN 策略。
- 最佳化器會估計不同 JOIN 演算法的成本，並選擇估計成本最低的那一個。

![[IMG-what is db hash join, nested loop join, merge join-20250810210645788.png]]


![[IMG-what is db hash join, nested loop join, merge join-20250810210646624.png]]





# link
- [EXPLAIN分析pgsql的性能 - LiZ的博客](https://boilingfrog.github.io/2020/04/26/pgsql%E4%B8%ADexplain/)