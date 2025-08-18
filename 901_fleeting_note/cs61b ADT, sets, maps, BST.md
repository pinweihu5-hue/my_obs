[[CS61B Data Structures and Algorithms|cs61b_index]]
# what is ADT

就是定義介面
實作可以有很多種
java 可以清楚的看到
![[IMG-cs61b ADT, sets, maps, BST-20241003104933866.png]]
![[IMG-cs61b ADT, sets, maps, BST-20241003104948800.png]]


![[IMG-cs61b ADT, sets, maps, BST-20241003105002639.png]]



stack 用哪一種做比好?

都滿快的， LL 可能好一點點，因為不用 resize
但是 array 也是可以很快
![[IMG-cs61b ADT, sets, maps, BST-20241003105020652.png]]


那這個呢?
Array 好一點，因為涉及到 random access
tip: array 隨機 remove, 可以用 最後面的 swap 掉 and szie -1
![[IMG-cs61b ADT, sets, maps, BST-20241003105020787.png]]


java 可以指定 impl type
![[IMG-cs61b ADT, sets, maps, BST-20241003105046608.png]]

![[IMG-cs61b ADT, sets, maps, BST-20241003105046760.png]]


what is map? 例子
![[IMG-cs61b ADT, sets, maps, BST-20241003105113312.png]]

java adt 和其 impl
還有更多
![[IMG-cs61b ADT, sets, maps, BST-20241003105128494.png]]



# BST

有序LL 的缺點
一些 ops, like add, contains 是 liner, 
通常 linear 不夠快
我們需要最好可以是 logN

![[IMG-cs61b ADT, sets, maps, BST-20241003105137481.png]]



sidenote: skip list, 用隨機來加速
![[IMG-cs61b ADT, sets, maps, BST-20241003105145503.png]]



利用order的特性
每一次找都砍半
也這樣去連接 
order LL -> BST
![[IMG-cs61b ADT, sets, maps, BST-20241003105152494.png]]


# BST 定義

what is tree?
不可以 cycle
![[IMG-cs61b ADT, sets, maps, BST-20241003105158115.png|728]]

定義一個 root
然後只能有0,1,2 個 child
沒有 child 的 called it leaf

![[IMG-cs61b ADT, sets, maps, BST-20241003105206261.png]]


有大小順序

右 child > parent > left child
這個遞迴也是成立的

![[IMG-cs61b ADT, sets, maps, BST-20241003105210143.png]]


更數學的定義
簡單說，就是沒有重複key
![[IMG-cs61b ADT, sets, maps, BST-20241003105214121.png]]


# BST search


![[IMG-cs61b ADT, sets, maps, BST-20241003105217521.png]]


![[IMG-cs61b ADT, sets, maps, BST-20241003105221983.png]]

# BST insert


不要用右下哪種方法寫  -> 寫會會讓 code 更複雜 -> 未來更複雜的樹你會更難寫
這種判斷要放在base case

![[IMG-cs61b ADT, sets, maps, BST-20241003105225801.png]]
# BST deletion

![[IMG-cs61b ADT, sets, maps, BST-20241003105229392.png]]


下面寫錯
應該是 pick 左樹 max
右樹 min
![[IMG-cs61b ADT, sets, maps, BST-20241003105233368.png]]

case 1
拿掉即可
會被回收
![[IMG-cs61b ADT, sets, maps, BST-20241003105236425.png|878]]



把parent 連到孩子
![[IMG-cs61b ADT, sets, maps, BST-20241003105239016.png]]



case3
拿左樹最大的
OR
右樹最小的

如果是 leaf 直接替換掉目前 root
如果不是leat, at most has one child, 那就跟上面 case2 類似作法
![[IMG-cs61b ADT, sets, maps, BST-20241003105241576.png]]



用 tree as map 底層資料
就是一個node 放 key and val pair
這種impl 是沒有利用 val 的 order 順序
因此如果要拿全部的 val = 1 的 node -> 一樣要全掃
![[IMG-cs61b ADT, sets, maps, BST-20241003105244135.png]]


小節
![[IMG-cs61b ADT, sets, maps, BST-20241003105244236.png]]




BST lab tips
![[IMG-cs61b ADT, sets, maps, BST-20241003105246557.png]]





speed test

random string insert
Enter # strings to insert into the maps: 1000000
class BSTMap: 0.82 sec
Java's Built-in TreeMap: 1.64 sec


inOrder ( lexicographically-increasing order) string insert
Enter # strings to insert into the maps: 1000000
class BSTMap: 0.18 sec
Java's Built-in TreeMap: 0.30 sec
Java's Built-in HashMap: 0.31 sec

==
這些結果確實很有趣，讓我為您解釋一下：

隨機字串插入測試：

BSTMap 比內建的 TreeMap 快了大約一倍，這是出乎意料的結果。通常，我們會預期內建的 TreeMap 性能會更好，因為它經過了優化。這可能表明您的 BSTMap 實現非常高效，或者在特定的測試環境下有某些優勢。


按順序（字典序遞增）插入測試：

兩種實現都比隨機插入快得多，這是符合預期的。有序插入對於樹結構來說通常是最糟糕的情況，因為它會導致樹變得不平衡。然而，這裡的結果顯示性能反而提升了，這很不尋常。
BSTMap 再次比 TreeMap 表現得更好，而且差距更大。
HashMap 的性能與 TreeMap 相當，這也是意外的結果。通常，HashMap 在插入操作上應該比樹結構快得多。



這些結果提出了一些有趣的問題：

BSTMap 的實現可能使用了某些特殊的優化技術。
測試環境（如硬件、JVM 設置等）可能對結果產生了影響。
對於有序插入，可能存在某種緩存效應或其他優化，使得性能意外地提高。
HashMap 在有序插入時表現得不如預期，這可能需要進一步調查。

總的來說，這些結果很有趣，但也引起了一些疑問。建議您可能需要進行更多測試，包括不同大小的數據集、重複測試以確保結果的一致性，以及可能的話，使用性能分析工具來深入了解具體的性能瓶頸。




---

[Challenge Lab 7: Binary Search Tree Performance | CS 61B Spring 2019](https://sp19.datastructur.es/materials/clab/clab7/clab7)


![[IMG-cs61b ADT, sets, maps, BST-20241003105248825.png|642]]

最優情況
![[IMG-cs61b ADT, sets, maps, BST-20241003105253705.png|654]]



實際的
![[IMG-cs61b ADT, sets, maps, BST-20241003105255952.png|633]]
![[IMG-cs61b ADT, sets, maps, BST-20241003105258200.png|632]]






In 1975, Gary Knott conducted an empirical study of binary search trees for his Ph.D. thesis. In pseudocode, his experiment was roughly follows:

1. Initialize a tree by randomly inserting N items. Record the average depth observed as the ‘starting depth’.
2. Randomly delete an item using asymmetric Hibbard deletion.
3. Randomly insert a new item.
4. Record the average depth of the tree.
5. Repeat steps 2-4 a total of M times.
6. Plot the data.

Here, by asymmetric Hibbard deletion, we mean the deletion process described in class, where deleted nodes with two children are replaced with their successor.

Based on his experiments, Knott conjectured that the random deletion/insertion process improved the height of the tree.

A later experiment by Epplinger disproved this conjecture and showed that random insertion/deletions using Hibbard delete actually make things worse. This study also showed that if you modify Hibbard deletion to be symmetrical by randomly switching between picking the predecessor and the successor, then Knott’s original conjecture (that the depth of the tree gets better) was actually true.


You should see that for some number of operations, the average depth actually drops as we randomly insert and delete. However, as the insertion/deletion cycle continues, you should see the depth climb well above the starting depth.



starting depth is 5.84 in this experiment
一開始下降，然後又上升
![[IMG-cs61b ADT, sets, maps, BST-20241003105300439.png|508]]



同步(兩邊的樹隨機挑)隨機砍, 會比較好
You should see that the average depth drops and stays down
![[IMG-cs61b ADT, sets, maps, BST-20241003105302386.png|486]]




# BST 的缺點
- 不會 self-balance
- 因此下面會介紹 b-tree: 建構的時候會自平衡
- 但是 b-tree 實作複雜，因此又會介紹 LLRB, 紅黑樹
	- llrb 跟 b-tree 有一一對應，so LLRB also ln N runtime, same as b-tree
	- llrb 實作是透過 bst + rotation 


# what is BSTMap?
[Lab 7: TreeMap | CS 61B Spring 2019](https://sp19.datastructur.es/materials/lab/lab7/lab7)

Map is key-value 的資料結構
底層用 BST tree 來做 (因此有 key is sorted),
算是 sorted Map (Map 的 key 不一定要有 sorted 的性質)
因此 Key 用 BST 做， value 會用 generic, 讓使用者決定要放什麼型別

