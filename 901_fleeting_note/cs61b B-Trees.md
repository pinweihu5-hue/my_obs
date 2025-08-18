
[[CS61B Data Structures and Algorithms|cs61b_index]]

slide: [cs61b 2019 lec17 ds3 2-3 trees, 2-3-4 trees - Google 簡報](https://docs.google.com/presentation/d/1zhQDvbcDZ9RJgJl0bmqwFFlHP8ExbDFo36Q9ZWH9EgU/edit#slide=id.g409413421_0637)

# BST Tree Height

spindly tree 是 linear
![[IMG-cs61b B-Trees-20241003104933868.png|778]]

哪一個是對的?
全是對的
因為 Big O of N 小於等於
雖然你說他小於 N^2 ..有點沒意義..但是就定義上是對的
![[IMG-cs61b B-Trees-20241003104948802.png]]

哪一個 informative?
這邊是說哪一個比較精準的意思
A 會比較精準
![[IMG-cs61b B-Trees-20241003105002838.png|787]]

另外實務上，我們通常用 Big O 來表示 worse case, 就是 theta
![[IMG-cs61b B-Trees-20241003105021230.png|761]]
\

BST 因為 bushy and spindy
![[IMG-cs61b B-Trees-20241003105047391.png]]

# def of Height, Depth -> 決定 worsecase runtime

depth 從 0 開始 root 是 0
每一個 node 有自己的 depth
可以算整個 tree 的 平均 depth
高就是最後一個 depth 的 idx
![[IMG-cs61b B-Trees-20241003105113642.png|734]]

樹高決定 worse case run time
平均深度決定 平均 run time
![[IMG-cs61b B-Trees-20241003105129190.png]]

問題
下面兩個樹要怎麼長?
![[IMG-cs61b B-Trees-20241003105137953.png|763]]

左邊的很好長
右邊的長法需要依照一定順序去 add
![[IMG-cs61b B-Trees-20241003105146041.png]]

現實世界呢?
![[IMG-cs61b B-Trees-20241003105146156.png|778]]

模擬 隨機 inserts
![[IMG-cs61b B-Trees-20241003105152763.png|744]]

contains 操作，平均操作下，是 logN
這可是好消息!
![[IMG-cs61b B-Trees-20241003105158496.png|726]]

壞消息是...
現實中，有些資料就是有序的，會一直往某個 node 加下去....as below...
![[IMG-cs61b B-Trees-20241003105206489.png]]

# B-trees / 2-3 trees /2-3-4 trees

如果有 17, 18 , 19
how to add?
![[IMG-cs61b B-Trees-20241003105210305.png|684]]


一個作法：
就全部塞在同一個 node
![[IMG-cs61b B-Trees-20241003105214246.png|767]]




壞處?
就是那一團變成 Linked list, -> 線性 run time
![[IMG-cs61b B-Trees-20241003105217628.png|772]]

解法?
抓中間 (can round to bottom) 然後推上去
但是這樣順序好像怪怪的
![[IMG-cs61b B-Trees-20241003105222087.png]]



另外我們限定一個 node最多3個 item  , L = 3
超過就要移動

就不需要是2元樹
可以開多個 child

14在15左邊
15, 17中間是 16
17更大是18, 19
![[IMG-cs61b B-Trees-20241003105225919.png]]


這樣到 15, 17 這個 node, 需要比較 O(L)次, 好處是 L is constant
![[IMG-cs61b B-Trees-20241003105229496.png]]




題目，下面這樣的話，要如何推?
![[IMG-cs61b B-Trees-20241003105233497.png]]
tip 
規則： L= 3
表示當你到4個，你就要推上去中間那個 (round to bottom), 因此拉上去19
另外因為19上去， 18 和 20,21就切開兩個 node



如果遇到 even 的總數，像是上面的 18,19,20,21 -> 除以二 take floor -> 19


ans
![[IMG-cs61b B-Trees-20241003105236537.png]]




更多練習
![[IMG-cs61b B-Trees-20241003105239131.png]]



root 滿了?
![[IMG-cs61b B-Trees-20241003105241681.png|731]]

除非 root 滿了，才會多一層
![[IMG-cs61b B-Trees-20241003105244358.png]]



## B-tree naming 的定義

L=2 叫做 2,3 tree
- 2 表示 一個 node 可以放2個，第三個是暫時的, 要推上去
- 因為可以放兩個，因此下面可以放三個  child-> 所以叫做 2,3 tree
- 2,3 是 child 的可能數量


L=3 叫做 2,3,4 tree
- 3 表示 一個 node 可以32個，第4個是暫時的, 要推上去
- 因為可以放3個，因此下面可以放4個  child-> 所以叫做 2,3,4 tree
- 2,3,4 是 child 的可能數量

![[IMG-cs61b B-Trees-20241003105246666.png]]



下面可以看一下L2 and L3 B-tree的圖
L 可以很大 ->  db 的應用
![[IMG-cs61b B-Trees-20241003105248959.png]]



# B-Tree  conti


題目，自己畫畫看

![[IMG-cs61b B-Trees-20241003105253853.png]]

















答案
![[IMG-cs61b B-Trees-20241003105254003.png]]

## b-tree 網頁模擬
[B-Tree Visualization](https://www.cs.usfca.edu/~galles/visualization/BTree.html)




練習
![[IMG-cs61b B-Trees-20241003105256435.png]]




















![[IMG-cs61b B-Trees-20241003105258462.png]]




## 不變性
1. all leaves 都跟 source 一樣距離
	1. 因為我們只會往上長，不會往下加，因此下面都會一樣的 brushy, 只有太 brushy才會往上長
2. 非 leave node 如果有 K個 items, 那 child 一定是 K+1的
	1. 因為我們就是 L+1個後推上去一個，因此這種操作確保了這個不變性
	2. naming 也表示了這個不變性。So L=2 -> 2,3 tree, L=3, 2,3,4 tree

![[IMG-cs61b B-Trees-20241003105258556.png]]



# B-Tree Runtime Analysis

worse case log of l+1 的 N 和  log of 2 的 N 之間，看下面比較清楚
因此就是 LOG  N
這邊的意思應該是說 
tree high 決定了 runtime ，那如果說每一層長出兩個分支(log base 2)，樹高其實長得比一層可以長三個分支(log base3)快

一層可以放三個 branch 也可以放更多 node
另外每一個 node 也可以放更多的 item, 這是另一個維度 (不過這個維度是 constant, 固定的)

下面的變數

N: total number of item
L:  nmber of item per node
H: height of tree

![[IMG-cs61b B-Trees-20241003105300560.png]]




contains ops 的 run time

有 H 個 node 要看
每一個 node 有 L 個 item, L is constant
全部就有 O(HL)

其中 H 的 run time 是height -> O(logN)
因此就是 O( LogN * L)
另外 L is constant -> O(logN)

![[IMG-cs61b B-Trees-20241003105302490.png]]



add 一樣
![[IMG-cs61b B-Trees-20241003105304436.png]]


小節
![[IMG-cs61b B-Trees-20241003105306094.png]]



下面有 del 可以繼續看  (vid 沒提到)
[cs61b 2019 lec17 ds3 2-3 trees, 2-3-4 trees - Google 簡報](https://docs.google.com/presentation/d/1zhQDvbcDZ9RJgJl0bmqwFFlHP8ExbDFo36Q9ZWH9EgU/edit#slide=id.g508ece10b0_1_1305)