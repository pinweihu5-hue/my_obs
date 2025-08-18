[[CS61B Data Structures and Algorithms|cs61b_index]]

[Disjoint Sets Visualization](https://www.cs.usfca.edu/~galles/visualization/DisjointSets.html)

API

連接(x, y)
是否連接(x, y)
![[IMG-cs61b Disjoint Sets, quick union-20241003104933873.png]]

簡化版

- 只放 int
- 全部都先初始化 - like disjoinSet(7) -> 0~6 個，共 7 ele 都先 init - 然後都先 assu 沒連接
  ![[IMG-cs61b Disjoint Sets, quick union-20241003104948806.png]]

設計上需要考慮：
element 可以放很多
呼叫可以很多次
connect 可以在 isConnected 的前面和後面

- 就是 connect(a, b) 後， isConnected(a, b) 會變成 true
  ![[IMG-cs61b Disjoint Sets, quick union-20241003105002977.png|732]]

設計思路：
no need to think abt line
就是一群在一起的 set 就可以 （connected component)
![[IMG-cs61b Disjoint Sets, quick union-20241003105021386.png|737]]

分析 using list of set of Disjoin Set

這裡也說明 why we use big O of N more often than big theta of N
因為 有時候可能會更好，因此 Big O (equal or less than, 表示更好) 就更貼切。theta 只有表示最壞的的情況

idea1
a list of sets of integer
![[IMG-cs61b Disjoint Sets, quick union-20241003105047588.png]]
![[IMG-cs61b Disjoint Sets, quick union-20241003105114077.png]]

一開始底層的資料結構如果選錯..

- 讓 code 更複雜
- 讓 code 更慢

這個很重要!!!
![[IMG-cs61b Disjoint Sets, quick union-20241003105114475.png]]

# Quick find -> introduce parent idx

我們習慣 idx is idx, 然後透過 idx like arr[2], 拿到 idx 2 裡面的 value
但是這邊要頭腦要轉一下，把 idx 當做 value 本身，然後 value 裡面代表的是，這個 value (as idx) 是在那個 set 裡面

idx 作為 value (因為我們已經簡化為，只可以用 int as value)
value 作為 set 的 Id
e.g
下面 0 是 value, 4 是 setId 4
所以 0,1,2,4 都是 setId 4
![[IMG-cs61b Disjoint Sets, quick union-20241003105129486.png]]

isConnected 很快
只需要去看這兩個 id 的 val 是否一樣
`id[p] == id[q]`
O(1)
![[IMG-cs61b Disjoint Sets, quick union-20241003105138180.png]]

connect 邏輯

- 先拿到這兩個 p, q 的 val 的 setId
- loop thru all id
  - 把 id[i] 是 pid 的那些 ids, 都改為 qid
  - 就是全部都換成同一個 set 
  

比較
![[IMG-cs61b Disjoint Sets, quick union-20241003105146486.png]]

要如何讓我們的 connect 更快
目前是 linear

![[IMG-cs61b Disjoint Sets, quick union-20241003105153178.png]]

# Quick union -> use tree!

Use a tree! 

Now we are mapping items to their parents.

-1 表示 沒有，這邊表示 自己就是 root

1 的 parent 是 0
2 的 1
3 是 -1 表示 root
4 0
5 3
6 是 root

value 放的就是自己 parent node 的 idx
當你用
arr[4], get 到的 val 就是 parent 的 idx , 如果是 -1 -> means it's root
![[IMG-cs61b Disjoint Sets, quick union-20241003105153267.png|840]]

connect 機制
![[IMG-cs61b Disjoint Sets, quick union-20241003105158987.png|768]]

worse case 情況 也是都要 N
![[IMG-cs61b Disjoint Sets, quick union-20241003105207039.png|782]]

- ctor
  - init int N element
  - 全部都是 root, 全部 assign -1
- find(或是叫做 root, 因為這個 method，給定 p, 找到 p 的 root)
  - 只要沒有找到 -1 => recursive 找到 -1
  - theta of N (reminder: theta means wrose case)
- isConnnected
  - 看是不是同一個 root
- connect - 找到彼此的 root - 連過去
  ![[IMG-cs61b Disjoint Sets, quick union-20241003105210947.png]]

還比 QuickFind 慢?
nonono~

QuickUnion 用 O(N)的顯示表示可以更快

只要我們可以讓 tree balance!

![[IMG-cs61b Disjoint Sets, quick union-20241003105214838.png|693]]

# weighted QuickUnion -> make tree balance

選 A
小樹 link 到大樹
因為我們盡量不要增加樹高
![[IMG-cs61b Disjoint Sets, quick union-20241003105217940.png|727]]

兩個新增規則
- 記錄 size
- connect 的規則是 小樹 connect 到大樹
  ![[IMG-cs61b Disjoint Sets, quick union-20241003105222300.png|764]]

track size has 兩種實作方法
- 另外記錄一個 array for size
- put size in idx, 不用 -1, 用 -size

![[IMG-cs61b Disjoint Sets, quick union-20241003105226154.png|710]]

we can prove
weighted quick union 的 worse case 的樹高 is logN
![[IMG-cs61b Disjoint Sets, quick union-20241003105229719.png|751]]

結論：我們可以用 weighted quick union, logN 足夠應對大數量(in most case)
![[IMG-cs61b Disjoint Sets, quick union-20241003105233672.png|674]]

直覺上來說，你會覺得 記錄樹高，把小樹高 link 到大樹高才合理，為何要用 size?
結論：效能一樣，我們可以 prove ，效能都是 log N
但是 "實作"上， 記錄樹高的實作比較複雜
![[IMG-cs61b Disjoint Sets, quick union-20241003105236658.png|709]]

# weighted QuickUnion with Path compression

還可以更快 -> 路徑壓縮 path compression

當我們 呼叫 `isConnected(15, 10)` 時...

![[IMG-cs61b Disjoint Sets, quick union-20241003105239244.png|803]]

我們把所有的 node 的 parent 都改為 root!
因為你都會經過，這個調整基本上成本很低
![[IMG-cs61b Disjoint Sets, quick union-20241003105241782.png]]

樹會變成這樣, 更平
![[IMG-cs61b Disjoint Sets, quick union-20241003105241916.png]]

try
![[IMG-cs61b Disjoint Sets, quick union-20241003105244472.png|804]]

只有經過的才會才會跟 root 建立連接
![[IMG-cs61b Disjoint Sets, quick union-20241003105246822.png|880]]

最後會變成
![[IMG-cs61b Disjoint Sets, quick union-20241003105249072.png]]

run time 變成 O(N +  M lg$^*$ N)

lg$^*$是一個函數，N 可以代入，算函數值。
當 N 越高， lg$^*$也會越大，**但是增長速度超級超級慢**



![[IMG-cs61b Disjoint Sets, quick union-20241003105253910.png]]

小節
![[IMG-cs61b Disjoint Sets, quick union-20241003105256146.png]]

# percolation

slide: [HW 2: Percolation | CS 61B Spring 2019](https://sp19.datastructur.es/materials/hw/hw2/hw2#runtime-analysis-ungraded)
[[algo_cs61b_percolate.excalidraw]]
code at: `/Users/re4388/project/personal/cs61b/Berkeley-CS61B-Student/hw2`

## backwash probelm

[HW 2: Percolation | CS 61B Spring 2019](https://sp19.datastructur.es/materials/hw/hw2/hw2#runtime-analysis-ungraded)

目前我判斷是否 percolate 是透過 nested for loop 去看 top nodes and bottom nodes 是否有 connected -> 沒有 backwash
課程建議用
virtual_top (1 virtual_node connected to all top nodes) and virtual_bottom(1 virtual_node connected to all bottom nodes)
then we can just check connected (v_top_node, v_bottom_node), and this is the easily way to meet the runtime spec

但是這有一個 side effect called backwash:
![[IMG-cs61b Disjoint Sets, quick union-20241003105256311.png]]

this is due to all bottom nodes are connected together

解法 1:
give up v_bottom nodes

解法 2:
2 UF 結構
一個就是有上下 v_node -> handle all things except check percolate
另一個只有上 v_node -> to check percolate and can aovid bashwash

discussion:

- [滲透問題(Percolation) 中 backwash 的解- d4rkb1ue&#39;s Blog --- 渗透问题 (Percolation) 中 backwash 的解决 - d4rkb1ue's Blog](https://drkbl.com/posts/quote-avoid-backwash-in-percolation/)
- [java - How to handle the backwash problem in percolation without creating an extra WUF object or using a method with complexity >= O(n)? - Stack Overflow](https://stackoverflow.com/questions/61396690/how-to-handle-the-backwash-problem-in-percolation-without-creating-an-extra-wuf)
