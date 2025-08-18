
[[CS61B Data Structures and Algorithms|cs61b_index]]

B-tree 哪裡不好
- 實作複雜
- performanc-wise, 有更好的選擇
  ![[IMG-cs61b Red Black Trees or LLRB-20241003104933887.png]]

# BST Structure and Tree Rotation

下面的 bst 都是1,2,3, 都合法的 valid state
we can use rotation to any state we want
![[IMG-cs61b Red Black Trees or LLRB-20241003104948817.png]]
for N 的 item in BST, 有多少可能的 state? 這又叫做 catalan number
[Catalan number - Wikipedia](https://en.wikipedia.org/wiki/Catalan_number)
[cs.cmu.edu/\~sleator/papers/rotation-distance.pdf](https://www.cs.cmu.edu/~sleator/papers/rotation-distance.pdf)



## How to rotate
定義
rotate left 定義 -> let right child be the boss

左轉 G
找出 right child -> P

**let P be the boss, 然後 g 要變成 left child**
另一個 approach 是
**right child 要上去先 merge, 然後 G 下去左邊**

![[IMG-cs61b Red Black Trees or LLRB-20241003105003677.png]]

try it!
![[IMG-cs61b Red Black Trees or LLRB-20241003105003834.png]]

![[IMG-cs61b Red Black Trees or LLRB-20241003105022857.png]]

try!
![[IMG-cs61b Red Black Trees or LLRB-20241003105023008.png]]

![[IMG-cs61b Red Black Trees or LLRB-20241003105049140.png]]

what is rotate?
- 保持 tree 特性 下 來 讓樹更平衡

可以互通
上面已經說到, tree can go to different state (maintain its invariants) via rotation
![[IMG-cs61b Red Black Trees or LLRB-20241003105049278.png]]

請看這個 10 slide 有一個 rotation demo -> [rotation balancing demo - Google 簡報](https://docs.google.com/presentation/d/1pfkQENfIBwiThGGFVO5xvlVp7XAUONI2BwBqYxib0A4/edit#slide=id.g465b5392c_00)

how to balance a tree using rotation

this
![[IMG-cs61b Red Black Trees or LLRB-20241003105115486.png]]

![[IMG-cs61b Red Black Trees or LLRB-20241003105130609.png|533]]

![[IMG-cs61b Red Black Trees or LLRB-20241003105130728.png]]

I think this operation is to allow left side more "stright", 打平
![[IMG-cs61b Red Black Trees or LLRB-20241003105139472.png]]

![[IMG-cs61b Red Black Trees or LLRB-20241003105139592.png]]

result
![[IMG-cs61b Red Black Trees or LLRB-20241003105147412.png]]

# Red-Black Trees or LLRB

回顧
BST -> 可透過 rotate 來平衡
2-3 tree -> 建立有規則，會一直確保平衡，不需 rotate
![[IMG-cs61b Red Black Trees or LLRB-20241003105147514.png|860]]

## 把 2-3 tree 用 LLRB 來呈現

一般來說:
- LLRB 對應到 2-3 tree
- red black tree 對應到 2-3-4 tree

1 item per node  -> 其實就是 BST
2 item per node  -> 如何呈現?

![[IMG-cs61b Red Black Trees or LLRB-20241003105154004.png|842]]

**把左邊那個 item 放到 left child, 多一層，然後那條線我們用 red 代表**
![[IMG-cs61b Red Black Trees or LLRB-20241003105154094.png]]

這就叫做 LLRB
LLRB 可以和 BST 有 1:1 mapping 的結構
red link 沒有特殊功能，就是拿來表示和 BST 對應的一個方便識別的記號而已
![[IMG-cs61b Red Black Trees or LLRB-20241003105159635.png|898]]

try
請把下面變成 對應的 BST
![[IMG-cs61b Red Black Trees or LLRB-20241003105159736.png]]

ans
![[IMG-cs61b Red Black Trees or LLRB-20241003105207679.png]]

try!
下面有很多 LLRB
但是，是否可以找到對應的 "合法" 的 2-3 tree??

hint: 寫成 2-3 tree, 看看是否是 valid 的 2-3 tree
![[IMG-cs61b Red Black Trees or LLRB-20241003105207758.png]]

ans:
2-3 tree 只能允許2, 3 個就要移上去，因此嚴格來說，下面第一個 case, 如果不是一個暫時狀態，就是 invalid
2, 3 case 不是，因為沒 balance, balance 的定義? -> all leaf have the same height
![[IMG-cs61b Red Black Trees or LLRB-20241003105212131.png]]

try
下面這個 LLRB 如果轉為 BST, 請問 height?
![[IMG-cs61b Red Black Trees or LLRB-20241003105215758.png|835]]

ans
肉眼辨識：

- left tree got more red link, so left tree will decide the height -> checl left tree is enough
- left tree got 0,1,2,3 -> 3 height
- but 2 red link, each red link add 1 height, so we add 2 height
- ans: 3 + 2 = 5
- 最多就是就一層都是 red link, 都會 多 1
- 因此最多 llrb 對應的 height 最多比 2-3 tree 多一倍 => ~2x
  ![[IMG-cs61b Red Black Trees or LLRB-20241003105219054.png]]

2-3 tree 如果高 is H
那 LLRB 是 2H+1
![[IMG-cs61b Red Black Trees or LLRB-20241003105219155.png]]

example:
![[IMG-cs61b Red Black Trees or LLRB-20241003105223561.png]]



LLRB 特性：

- 一個 node 不可以有兩個 red link
	- 因為這樣就變成 4 個 node ->  not valid 2-3 tree
- 一個 node 只能有一個 red link
- 除非是暫時狀態，那就要整理為一個 red link
- 從 root 到 leaf 經過的 "black link" 的數量，2-3 tree 和 LLRB 一樣
  ![[IMG-cs61b Red Black Trees or LLRB-20241003105227556.png]]

下一個問題
## 如何建立 LLRB?

- 上面概念解說，我們是透過從 2-3 tree 去轉為 llrb
- 實作也是這樣嗎? NO
- 2-3 tree 就是 hard to impl 才想要找新方法, 因此我們不可能先建立一個 2-3 tree 出來
- 因此我們要... 
	- insert operation just like BST 
	- 建立 llrb 時，透過 rotate 來跟 llrb 一一對應
  ![[IMG-cs61b Red Black Trees or LLRB-20241003105227676.png]]

## 用旋轉來和 2-3 tree  對應

impl llrb, 對照 2-3 tree
用 rotate 和 color flip 確保對應
![[IMG-cs61b Red Black Trees or LLRB-20241003105231171.png|839]]

how to add/insert node?



insert left (insert smaller one)
![[IMG-cs61b Red Black Trees or LLRB-20241003105231275.png|576]]

ans
因為 2-3 tree 就是 變成 es (e is smaller than s)
因此 llrb 就是拉一個 red link to the smaller element to the left child
![[IMG-cs61b Red Black Trees or LLRB-20241003105234871.png]]

insert at right?  (when insert a bigger one)
try
![[IMG-cs61b Red Black Trees or LLRB-20241003105234966.png]]

LLRB is NOT allow right-red-link
-> E 左旋 來解
![[IMG-cs61b Red Black Trees or LLRB-20241003105237757.png]]

before we discuss the next operation....
我們先允許可以存在一個 node 兩個紅線 的暫時狀態
2-3 tree 的概念下，就是允許 4 個 node（2-3 tree means, 2-3 child）, 暫時的，因為我們利用這個暫時狀態可以讓 LLRB 轉到這狀態，然後可以繼續轉
![[IMG-cs61b Red Black Trees or LLRB-20241003105237833.png]]

有了這個暫時狀態， we can conti. to discuss the next operation...

try 如果連續插入兩個小的，會有兩個 連續的 red link

但是這是不可以的，因為這就是會出現 esz 連一起, not valid 2-3 tree
![[IMG-cs61b Red Black Trees or LLRB-20241003105240270.png]]

ANS

如果上面提到的，我們先允許 esz 這個暫時狀態
llrb -> Z 右轉，進入 單 node 雙紅線狀態
![[IMG-cs61b Red Black Trees or LLRB-20241003105240375.png]]

那我們繼續處理"單 node 雙紅線狀態"
![[IMG-cs61b Red Black Trees or LLRB-20241003105242797.png]]

color flip!
如果你可以從 2-3 tree 的結構來反推，也只有這招
![[IMG-cs61b Red Black Trees or LLRB-20241003105242881.png]]

## LLRM -> 2-3 tree 對應的所有操作

- insert smaller item -> smaller go left child with red link at left
- insert bigger item -> 出現右 red link -> 左旋 -> red link turn to left
- 連續兩個 red link at 左下-> 右旋 最上面那個-> 一個 node 上出現雙 red link -> color flip
- 最後就是可能要需要連續操作以上來達到一對一對應
  ![[IMG-cs61b Red Black Trees or LLRB-20241003105245355.png|743]]

連續例子
try

雙 node -> flip color 後，變成右 red link
![[IMG-cs61b Red Black Trees or LLRB-20241003105245469.png|712]]

B 左旋
![[IMG-cs61b Red Black Trees or LLRB-20241003105247517.png]]

try this 來證明你懂
![[IMG-cs61b Red Black Trees or LLRB-20241003105247620.png]]

[LLRB Insertion Demo - Google 簡報](https://docs.google.com/presentation/d/1jgOgvx8tyu_LQ5Y21k4wYLffwp84putW8iD7_EerQmI/edit)
跑完學到幾招

- flip 是那一個 node 連接的 red link, 類似下面兩個 red link 變成 non-red link, 上面連著 node 的 black link change to red link
- flip 一個 node 的下面兩個 red link, 如果沒有上面的 red link (aka, this nood is root) -> 那這個 red link 就不見了
- rotation 後， red link 需要帶著走，不是就消失了

# LLRB Runtime and Implementation

contains and insert 操作都是 logN
since rotate and flip at worse is also logN since you do this along the tree height
![[IMG-cs61b Red Black Trees or LLRB-20241003105249867.png|840]]

helper 寫法，切好， code is not that comlicated, like below
only 3 lines for roatation compared to BST
分別處理三種情境

如果右 red link -> 左轉
如果 left and left.left -> 右轉
如果 left and right 都是紅 -> flip
![[IMG-cs61b Red Black Trees or LLRB-20241003105249969.png]]

# Search Tree Summary

- BST easier to impl 但是 不會 balance -> worse case is O(N)
- 2-3 tree / b-tree -> pain to impl -> O(lnN)
- LLRB -> bst + rotaion, a balanced tree -> O(lnN)
- Java TreeMap 底層就是 紅黑樹的變種
  ![[IMG-cs61b Red Black Trees or LLRB-20241003105254864.png|918]]

1. 有更多的樹算法
2. map and set 的底層除了用 tree 來做，另一個常見的作法是用 hash fn!
   ![[IMG-cs61b Red Black Trees or LLRB-20241003105254952.png]]

LLRB recap in discussion
![[IMG-cs61b Red Black Trees or LLRB-20241003105257195.png]]
