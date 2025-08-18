
recap
![[IMG-cs61b Graph Traversal and impl-20241003104933876.png]]




# 最短路徑 with BFS
![[IMG-cs61b Graph Traversal and impl-20241003104948808.png|963]]


## BFS
use BFS, queue
demo: [cs61b Breadth First Paths Demo (2019) - Google 簡報](https://docs.google.com/presentation/d/1JoYCelH4YE6IkSMq_LfTJMzJ00WxDj7rEa49gYmAtc4/edit)
![[IMG-cs61b Graph Traversal and impl-20241003105003172.png]]



找最近關連的應用
兩個電影明星最短的距離
[SymbolGraph.java](https://algs4.cs.princeton.edu/41graph/SymbolGraph.java.html)
![[IMG-cs61b Graph Traversal and impl-20241003105021608.png]]

not suitable for google map, just for now, no label on edge
![[IMG-cs61b Graph Traversal and impl-20241003105047767.png]]



# Graph API

很重要 

impl 影響runtime and mem usage
API 設計也影響 how difficult/ez client use it
![[IMG-cs61b Graph Traversal and impl-20241003105114302.png]]




this class, we make it simple, use integer first
![[IMG-cs61b Graph Traversal and impl-20241003105129334.png]]



![[IMG-cs61b Graph Traversal and impl-20241003105138066.png]]

howt client use it to get degress
![[IMG-cs61b Graph Traversal and impl-20241003105146321.png]]



how to print?
![[IMG-cs61b Graph Traversal and impl-20241003105153007.png]]


![[IMG-cs61b Graph Traversal and impl-20241003105153080.png]]



API design 決定 how to impl DFS, BFS, print...等等等
![[IMG-cs61b Graph Traversal and impl-20241003105158876.png]]


# Graph Representation and Graph Algorithm Runtimes



先看impl
![[IMG-cs61b Graph Traversal and impl-20241003105206783.png]]


來看看有哪幾種可能
![[IMG-cs61b Graph Traversal and impl-20241003105210636.png]]


like tree, fixed link
![[IMG-cs61b Graph Traversal and impl-20241003105214461.png]]


use arr to put i, like PQ
but this only work for complete tree
![[IMG-cs61b Graph Traversal and impl-20241003105217771.png]]


鄰近矩陣
上圖
左邊是 source
上面是 taregt
如果有 0 -> 1 裡面就是1 (or use T/F)

下圖是 undirected graph, 兩邊都要 1
![[IMG-cs61b Graph Traversal and impl-20241003105222194.png]]

找 neight  是 O of V
iterate thru one row
![[IMG-cs61b Graph Traversal and impl-20241003105226033.png]]




pint all 是 v^2 runtime
![[IMG-cs61b Graph Traversal and impl-20241003105229613.png]]


另一種表示法 -> 少用
![[IMG-cs61b Graph Traversal and impl-20241003105233624.png]]


最常見的表示法 鄰近list

一個 list, idx 為 vetex number
每一個idx 連出去一個 link
![[IMG-cs61b Graph Traversal and impl-20241003105233803.png]]



![[IMG-cs61b Graph Traversal and impl-20241003105236783.png]]
為何是 O (V + E)? 可以用比較 intutive 的方式跟我說明嗎？

**想像一下你正在參觀一個城市：**

* **頂點 (V) 是景點：**每個頂點代表城市中的一個景點。
* **邊 (E) 是道路：**每條邊代表連接兩個景點的道路。

**你的目標是參觀所有景點並拍下它們的照片。**

**圖形打印演算法就像這樣：**

1. **選擇一個起始景點。**
2. **參觀該景點並拍照。**
3. **對於與當前景點相連的每條道路：**
   - **走過那條道路到達下一個景點。**
   - **參觀該景點並拍照。**
4. **重複步驟 3，直到參觀完所有景點。**

**現在，讓我們分析一下執行時間：**

* **參觀每個景點需要一定的時間：**這對應於 `V`，因為你需要參觀所有 `V` 個景點。
* **走過每條道路也需要一定的時間：**這對應於 `E`，因為你需要走過所有 `E` 條道路。

**因此，總執行時間與參觀景點的時間和走過道路的時間之和成正比。** 

**這就是為什麼圖形打印的執行時間是 O(V + E)。**

**更直觀的例子：**

* **稀疏圖（道路很少）：**如果你在一個道路很少的城市，你大部分時間都花在參觀景點上，而不是在路上。所以執行時間更接近於 O(V)。
* **密集圖（道路很多）：**如果你在一個道路錯綜複雜的城市，你可能會花很多時間在路上。在這種情況下，執行時間更接近於 O(E)，因為 E 比 V 大很多。

**總之，O(V + E) 表示無論圖的形狀如何，執行時間總是會隨著景點數量和道路數量的增加而線性增長。** 


![[IMG-cs61b Graph Traversal and impl-20241003105236922.png]]


鄰近matrix 缺點是
- 稀疏matrix, 很多都用不到
- print 和 space used 都是 V^2
![[IMG-cs61b Graph Traversal and impl-20241003105239459.png]]


undirected graph 鄰近list 參考
![[IMG-cs61b Graph Traversal and impl-20241003105242123.png]]



# Graph Traversal Implementations and Runtime


# Paths
一般來說，我們會把 graph data type 跟處理，分開處理
![[IMG-cs61b Graph Traversal and impl-20241003105244589.png]]


new 一個算 path 的, arg 是 G 和 某一個 點(int)
然後可以看這個點是否可以連到3
或是看這個點到3的路徑
![[IMG-cs61b Graph Traversal and impl-20241003105246985.png]]



看 demo link, on how to 走
[61B depth first paths demo - Google 簡報](https://docs.google.com/presentation/d/1lTo8LZUGi3XQ1VlOmBUF9KkJTW_JWsw_DOPq8VBiI3A/edit#slide=id.g76e536eb1_0_294)
![[IMG-cs61b Graph Traversal and impl-20241003105249187.png]]

like below, see more for above link
![[IMG-cs61b Graph Traversal and impl-20241003105254159.png]]


will run all logic and get all the result ready in ctor
![[IMG-cs61b Graph Traversal and impl-20241003105254249.png]]


pathTo 和 hasPathTo的實作
![[IMG-cs61b Graph Traversal and impl-20241003105256551.png]]

![[IMG-cs61b Graph Traversal and impl-20241003105258668.png]]



![[IMG-cs61b Graph Traversal and impl-20241003105300713.png]]


how about BFS
use queue
其他邏輯都一樣
![[IMG-cs61b Graph Traversal and impl-20241003105300895.png]]


run time more or less the same
![[IMG-cs61b Graph Traversal and impl-20241003105302713.png]]



# Layers of Abstraction

再次提醒

我的設計出來的 API 影響 client 是否好用
![[IMG-cs61b Graph Traversal and impl-20241003105304860.png]]


實作影響了run time and mem usage
![[IMG-cs61b Graph Traversal and impl-20241003105306333.png]]



鄰近矩陣的 impl
![[IMG-cs61b Graph Traversal and impl-20241003105307862.png]]



![[IMG-cs61b Graph Traversal and impl-20241003105309316.png]]



# Summary
![[IMG-cs61b Graph Traversal and impl-20241003105310643.png]]


![[IMG-cs61b Graph Traversal and impl-20241003105312698.png]]