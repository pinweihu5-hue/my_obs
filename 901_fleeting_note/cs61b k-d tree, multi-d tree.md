[cs61b 2019 ds6 lec22 kd trees - Google 簡報](https://docs.google.com/presentation/d/1lsbD88IP3XzrPkWMQ_SfueEgfiUbxdpo-90Xu_mih5U/edit)

sets and maps 的 impl
常見有兩種
tree or hash

特殊情況還可以用

data idxed arr or trie
![[IMG-cs61b k-d tree, multi-d tree-20241003104933899.png]]

## 目標： 找到最近的鄰居, 找特定區域的 objs

如果我們的資料是 2d?
下圖 obj 有 x, y

要如何實作 `nearest(馬)` 和 runtime via hash table?

spolier: hash 的 sudo code already provied
![[IMG-cs61b k-d tree, multi-d tree-20241003104948829.png]]

但是 hash 的 range search 就是一個一個找

![[IMG-cs61b k-d tree, multi-d tree-20241003105004368.png]]

# Uniform Partitioning

平均的切分

![[IMG-cs61b k-d tree, multi-d tree-20241003105023676.png]]

![[IMG-cs61b k-d tree, multi-d tree-20241003105023799.png]]

![[IMG-cs61b k-d tree, multi-d tree-20241003105049774.png]]

![[IMG-cs61b k-d tree, multi-d tree-20241003105116055.png]]

一樣是 O of N, 只是看你切多細，你的速度會有影響
另外這也是 tradeoff, 你切月細, 你也要看越多 bins
![[IMG-cs61b k-d tree, multi-d tree-20241003105116519.png]]

# QuadTrees

search tree 找東西會快，就是因為有排好了
![[IMG-cs61b k-d tree, multi-d tree-20241003105131981.png]]

如果我們用 bst 來 處理
我們需要決定要用哪一個維度來 ordering
![[IMG-cs61b k-d tree, multi-d tree-20241003105132090.png]]

![[IMG-cs61b k-d tree, multi-d tree-20241003105140598.png]]

![[IMG-cs61b k-d tree, multi-d tree-20241003105140682.png]]

![[IMG-cs61b k-d tree, multi-d tree-20241003105148418.png]]

![[IMG-cs61b k-d tree, multi-d tree-20241003105148522.png]]

結論
如果你用 x ordering and ask y quesiton -> you can not have prunning! vice versa
![[IMG-cs61b k-d tree, multi-d tree-20241003105154595.png]]

4 個角度去切
越切越小

demo link [cs61b quadtree insertion demo - Google 簡報](https://docs.google.com/presentation/d/1vqAJkvUxSh-Eq4iIJZevjpY29nagNTjx-4N3HpDi0UQ/pub?start=false&loop=false&delayms=3000)
hint: 插入的時候，每一個 node 一樣都是由最 root 出發，如果那個方位被佔據了，就跳到那個 node, 然後繼續就方位來判斷
![[IMG-cs61b k-d tree, multi-d tree-20241003105154682.png]]

![[IMG-cs61b k-d tree, multi-d tree-20241003105200067.png]]

每一次 search 四個方位，如果綠色框框不在那個方位裡面，就可以 prune 掉
然後最後可以越 prune 越小 subspace
link [cs61b quadtree range search demo - Google 簡報](https://docs.google.com/presentation/d/1ZVvh_Q15Lh2D1_NnzZ4PR_aDsLBwvAU9JYQAwlSuXSM/edit#slide=id.g52a9824549_0_129)
![[IMG-cs61b k-d tree, multi-d tree-20241003105208080.png]]

# Higher Dimensional Data

octree

game dev 很常出現
![[IMG-cs61b k-d tree, multi-d tree-20241003105212502.png]]
google `octree`
![[IMG-cs61b k-d tree, multi-d tree-20241003105212606.png|508]]

多維度也未必都是物理意義上的，多特色尋找都是多維度
![[IMG-cs61b k-d tree, multi-d tree-20241003105216116.png]]

# k-d tree

like BST 的 insert, 一次 用 x 軸切分，一次 y 軸切分..輪流
(多維度很容易 generlize, 類似一次 x 切分，一次 y 切分，一次 z 切分...)

- 如果 x, y 都一樣就取代掉
- 如果 tie, 視為 greater than

insert demo link [cs61b kdtree insertion demo - Google 簡報](https://docs.google.com/presentation/d/1WW56RnFa3g6UJEquuIBymMcu9k2nqLrOE1ZlnTYFebg/edit)
![[IMG-cs61b k-d tree, multi-d tree-20241003105219542.png]]

find nearest pt
[cs61b kdtree nearest demo - Google 簡報](https://docs.google.com/presentation/d/1DNunK22t-4OU_9c-OBgKkMAdly9aZQkWuv_tBkDg1G4/edit)
![[IMG-cs61b k-d tree, multi-d tree-20241003105219629.png]]

如果有經過 "點"(refer to A, B, C...), 就要記錄下來他們的距離
4, 5 和 0, 7 -> 4.5

- x, y 軸互減 加起來, and then squart
  因為這就是我們要找的最短距離
  ![[IMG-cs61b k-d tree, multi-d tree-20241003105223949.png]]

先找 good side then find bad side
是否需要找 bad side 可以看看最近距離是否有比目前的最好距離還低?
類似 E 的下面是 bad side, 但是最短距離是 2, 比 2.2 還低，因此還是可以找一下
雖然下面沒有點可以找了
![[IMG-cs61b k-d tree, multi-d tree-20241003105224050.png]]

左邊都看完了，有需要看右邊(bad side)嗎?
因為這個 bad side 最短可能距離是 2
why?
0 of (0, 7) 和 2 of (2,3) 的距離就是 2 且 2 < 2.2(目前最佳距離) -> 因此要去找
PS: 如果算出來的距離是 2.2, 那就不用找了，因為找到最好也是跟目前的最好的一樣好而已
PSPS: 這個看 spec, 如果要找到的 nearest pt 是要多個點，那就可能要繼續找
![[IMG-cs61b k-d tree, multi-d tree-20241003105227988.png]]![[IMG-cs61b k-d tree, multi-d tree-20241003105228073.png]]

so, it's worthy to go right side of A

and the first encounter is B
![[IMG-cs61b k-d tree, multi-d tree-20241003105231674.png]]


and again, we need to ask, what's good and bad side for B
good side is up, and good side is always the direction we want to check (bad side is not necessary, like above, we need addtional checking)

when we go up, 我們進入 C, dist is 4.5
![[IMG-cs61b k-d tree, multi-d tree-20241003105231759.png]]

然後先 check C's good side
![[IMG-cs61b k-d tree, multi-d tree-20241003105235435.png]]

因此又進入 D 然後算一下，dist is 5
![[IMG-cs61b k-d tree, multi-d tree-20241003105238191.png]]

then check D up/donw
up is good side, let's check...it's null, so just ret
![[IMG-cs61b k-d tree, multi-d tree-20241003105240707.png]]

then we need to check D's down?
no, 因為最好的距離就是從 0,7 到 A, and is worse then currently best dist
![[IMG-cs61b k-d tree, multi-d tree-20241003105240803.png]]

實作上，你也可以不用算那個斜角的長度(purple line)
你可以用 green line, goal.y - d.y 來算，結論不變，但計算簡單
green line 比較短，因此你可能有些時候會去看一些沒必要的 bad side, 但 perf 降低不多，可以忽略
![[IMG-cs61b k-d tree, multi-d tree-20241003105243218.png]]

到目前為止的進度
![[IMG-cs61b k-d tree, multi-d tree-20241003105243313.png]]

有需要看 C's bad side -> no need, we can prune
![[IMG-cs61b k-d tree, multi-d tree-20241003105245794.png]]

繼續往上 return

有需要看 B's down/bad side?
![[IMG-cs61b k-d tree, multi-d tree-20241003105245903.png]]

大於 2.2, no need to check
![[IMG-cs61b k-d tree, multi-d tree-20241003105247948.png]]

最後回到最上面
![[IMG-cs61b k-d tree, multi-d tree-20241003105248041.png]]

![[IMG-cs61b k-d tree, multi-d tree-20241003105253077.png]]
3.3 顯示，如果 dist 跟目前 best dist 一樣，那麼那一邊也不用找
[disc09sol.pdf](https://sp19.datastructur.es/materials/discussion/disc09sol.pdf)

# k-d tree pseudocode



下面說的 n."left"child，以上面的例子，就是 left or down 的那個 branch
因此上面的例子(0, 7), 因此 good side is left one (當 node A is looking at X axis)
![[IMG-cs61b k-d tree, multi-d tree-20241003105255360.png]]
![[IMG-cs61b k-d tree, multi-d tree-20241003105255431.png|564]]



method nearest(現在要visit的node n, goal_point goal, 目前最好的node best)
- if n is null -> return currently best
- if current node have better dist -> use it
- based on current n node comparator -> 決定要要怎麼比大小
	- 決定 good side and bad side
- 遞迴呼叫 nearest(goodSide, goal, best)
- 判斷是否需要 看 badSide: 最好的距離是否比目前 best 的好 -> visit badSide
	- 遞迴呼叫 nearest(badSide, goal, best)
- return best







# Summary and Applications

應用
- 要找有哪些在那個綠框框內 (一開始的問題)
  - 請問有 spotify 哪些歌是長度大於 5 分鐘 (PS: 歌可以有很多維度/feature)
- 請問離某一個點最近的 object 是哪一個
  - 請問 spotify 哪首歌的長度最接近 7:03 秒 且是女歌手，也是華人歌手，且今年賣出的，且銷量大於 3 百萬，且下載量低於 10 萬張，且..

感覺上面都可以用 sql 處理 -> see CS186

- 或是用 hash + partitioning
- 或是用 quadTree, octree and k-dtree - quadTree and octree 是直接切出 4 個和 8 個 subspace - k-d tree 是每個維度輪流，但是一次 prune 一個維度的一半空間
  ![[IMG-cs61b k-d tree, multi-d tree-20241003105257522.png]]

模擬 starling murmuration

what is starling murmuration?
八哥的低語
[How to spot a starling murmuration | Natural History Museum](https://www.nhm.ac.uk/discover/spot-a-starling-murmuration-this-winter.html)
![[IMG-cs61b k-d tree, multi-d tree-20241003105257623.png]]
![[IMG-cs61b k-d tree, multi-d tree-20241003105259702.png]]
![[IMG-cs61b k-d tree, multi-d tree-20241003105259796.png]]
[amazing starlings murmuration (full HD) -www.keepturningleft.co.uk - YouTube](https://www.youtube.com/watch?v=eakKfY5aHmY)

[Murmuration of Starling - Simulation of Flocking Boids in C++ / DirectX 11- 2 - YouTube](https://www.youtube.com/watch?v=nbbd5uby0sY)

- related repo -> [GitHub - GavWood/DOGE-Game-Engine: DOGE Game Engine written in C++ with OpenAL and Open GLES.](https://github.com/GavWood/DOGE-Game-Engine)
  - 裡面用 kd tree 來做 murmuration 的模擬

Ask ai:

k-d 樹 (k-dimensional tree) 可以有效地應用於模擬椋鳥群飛 (starling murmuration) 行為，特別是在處理鄰近搜索和碰撞避免方面。以下是如何使用 k-d 樹來輔助模擬椋鳥群飛：

**1. 椋鳥作為 k-d 樹中的點：**

- 每隻椋鳥都可以被視為三維空間中的一個點，其坐標 (x, y, z) 代表其位置。
- 將所有椋鳥的位置數據插入 k-d 樹中。

**2. 鄰近搜索：**

- 對於每隻椋鳥，利用 k-d 樹快速找到其周圍一定半徑內的鄰居。這些鄰居是影響其飛行決策的關鍵因素。
- k-d 樹可以高效地執行這種鄰近搜索，比暴力搜索所有椋鳥對要快得多。

**3. 規則應用：**

- 一旦確定了每隻椋鳥的鄰居，就可以應用模擬椋鳥群飛行為的規則，例如：
  - **分離:**  避免與鄰居過於接近，防止碰撞。
  - **對齊:**  調整自身速度和方向，與鄰居保持一致。
  - **凝聚:**  向鄰居的平均位置移動，保持群體凝聚力。
- 這些規則的具體實現可以使用各種技術，例如向量計算、勢場方法等。

**4. 更新位置：**

- 根據規則計算出的新速度和方向，更新每隻椋鳥的位置。
- 更新 k-d 樹，反映椋鳥的新位置，以便進行下一輪的鄰近搜索。

**優點：**

- **效率：** k-d 樹可以快速找到鄰居，提高模擬效率，尤其是在處理大量椋鳥時。
- **動態更新：** k-d 樹可以動態更新，適應椋鳥位置的不斷變化。

![[IMG-cs61b k-d tree, multi-d tree-20241003105301746.png|786]]
[Barnes–Hut simulation - Wikipedia](https://en.wikipedia.org/wiki/Barnes%E2%80%93Hut_simulation)

如何使用 Barnes-Hut 演算法來優化 N 體模擬 (N-body simulation)。

**N 體模擬**是一種用於模擬大量粒子在相互引力作用下運動的計算方法。例如，模擬星系中恆星的運動，或是模擬氣體分子的運動。

**問題：** 傳統的 N 體模擬演算法需要計算每對粒子之間的引力，時間複雜度為 O(n^2)，其中 n 是粒子的數量。當粒子數量很大時，計算量會變得非常巨大，導致模擬速度非常慢。

**解決方案：** Barnes-Hut 演算法通過使用四叉樹 (quadtree) 或八叉樹 (octree) 來優化計算，將時間複雜度降低到 O(n log n)。

**Barnes-Hut 演算法的基本思想：**

1. **將空間劃分為樹狀結構：** 將所有粒子所在的空間劃分為一個四叉樹或八叉樹。每個節點代表空間中的一個區域。
2. **將粒子存儲在葉節點：** 每個粒子都被存儲在它所屬的葉節點中。
3. **計算內部節點的質量和質心：** 從葉節點開始，遞歸地計算每個內部節點的總質量和質心。
4. **近似計算遠距離粒子的引力：** 對於一個粒子，如果一個節點距離該粒子足夠遠，則可以將該節點及其所有子節點視為一個單個粒子，用其質心來近似計算引力。這樣就避免了計算該節點中所有粒子對該粒子的引力。

**例子：**

- 仙女座星系距離我們的太陽非常遙遠。
- 在計算太陽所受引力時，可以將整個仙女座星系視為一個單個粒子，用其質心來近似計算引力。
- 這樣就避免了計算仙女座星系中所有恆星對太陽的引力，大大減少了計算量。

**總結：**

Barnes-Hut 演算法通過將空間劃分為樹狀結構，並近似計算遠距離粒子的引力，有效地降低了 N 體模擬的計算量，使其可以用於模擬更大規模的粒子系統。
