

![[IMG-cs61b Minimum Spanning Trees-20241003104933882.png|867]]


- dfs and keep going until you see visited
	- 小心不要算上剛剛出來的那個
![[IMG-cs61b Minimum Spanning Trees-20241003104948812.png]]

另一個方法，用 WQUF
每一個走過的 edge, 看看兩邊的 vertices 是否相連
- 如果沒有，union them
- 如果有相連，表示遇到 cycle
![[IMG-cs61b Minimum Spanning Trees-20241003105003358.png]]


# MST
spanning tree 定義
- undirected graph 中可以找到一個 tree, 這個 tree 符合:
	- all connected together
	- acyclic
	- 包括全部的 vertices


最小生成樹
minimum spanning tree 定義:
- 符合上面定義且 edge label or weight 加總最小
![[IMG-cs61b Minimum Spanning Trees-20241003105022076.png]]



左上不是因為沒有 connected
右上不是因為 cycle
下面是
![[IMG-cs61b Minimum Spanning Trees-20241003105022229.png]]


一些應用
- 前AI時代的手寫辨識
- 醫學辨識
![[IMG-cs61b Minimum Spanning Trees-20241003105048374.png]]

其他應用
1. **網絡設計**：
    - **電腦網絡**：在設計局域網（LAN）或廣域網（WAN）時，MST 可以用來確定如何連接所有的電腦和路由器，以最小化總的電纜長度或成本。
    - **電力網絡**：在設計電力傳輸網絡時，MST 可以用來確定如何連接所有的變電站和發電廠，以最小化總的電纜長度或成本。
2. **交通規劃**：
    - **道路網絡**：在設計城市或區域的道路網絡時，MST 可以用來確定如何連接所有的城市或地區，以最小化總的道路建設成本。
    - **管道網絡**：在設計水管、天然氣管道或石油管道網絡時，MST 可以用來確定如何連接所有的輸送點，以最小化總的管道建設成本。
3. **電信網絡**：
    - **電話網絡**：在設計電話網絡時，MST 可以用來確定如何連接所有的電話交換機，以最小化總的電纜長度或成本。
    - **光纖網絡**：在設計光纖網絡時，MST 可以用來確定如何連接所有的光纖節點，以最小化總的光纖長度或成本。
4. **集群分析**：
    - **數據聚類**：在數據挖掘和機器學習中，MST 可以用來進行層次聚類分析，幫助識別數據中的自然群體或集群。
5. **圖像處理**：
    - **圖像分割**：在圖像處理中，MST 可以用來進行圖像分割，將圖像分割成不同的區域或物體。
6. **基因組學**：
    - **基因序列比對**：在基因組學中，MST 可以用來比對不同的基因序列，找出它們之間的最小差異。

![[IMG-cs61b Minimum Spanning Trees-20241003105048537.png]]



ans
![[IMG-cs61b Minimum Spanning Trees-20241003105115074.png]]



Q: 從哪一個vertex 開始 最短路徑樹(SPT)是最小生成樹(MST)？ 
![[IMG-cs61b Minimum Spanning Trees-20241003105129925.png]]

B 
![[IMG-cs61b Minimum Spanning Trees-20241003105138522.png]]

因此，一個找 MST的作法，先找到 vertex, 用 Dijktra 算法找 SPT, 就是 MST??


==

MST是看整體
SPT 是 一個 source 出發，建立一個到每一個點的最短路徑
![[IMG-cs61b Minimum Spanning Trees-20241003105138632.png]]


問題
1. 找 MST
2. 是否可以透過一個 node to get SPT and then it is MST?
![[IMG-cs61b Minimum Spanning Trees-20241003105146900.png]]

this is MST
![[IMG-cs61b Minimum Spanning Trees-20241003105147018.png]]


PS:另一邊 (flip side) 的 2也可以
![[IMG-cs61b Minimum Spanning Trees-20241003105153477.png]]




答案是 NO
這個圖的 每一個點的 SPT 不會需要繞過後面那個 0 
然後中間的2會一起走，這樣才是最短路徑
![[IMG-cs61b Minimum Spanning Trees-20241003105153606.png]]


下圖英文是說，這個圖的 SPT 都會是這個 pattern
at least 3 of the 2 weight edges
![[IMG-cs61b Minimum Spanning Trees-20241003105159205.png]]


![[IMG-cs61b Minimum Spanning Trees-20241003105159310.png]]

這這個圖證明：有些圖，你不會找到 SPT 剛好會是 MST


# cut property
- 把 vertex 切兩組
- crossing edge: 就是這個 edge 連接的兩個 vertices是不同組
- cut property: 任何 cut, 最小weight 的那個 crossing edge 會是在 MST裡面
	- PS: given any cut, the maximum-weight crossing edge is also in the maximum spanning tree
- 先假設 edge weights 是 unique
![[IMG-cs61b Minimum Spanning Trees-20241003105207243.png]]



找 最小 weight crossing edge
右邊的已經排好序
![[IMG-cs61b Minimum Spanning Trees-20241003105207341.png]]

![[IMG-cs61b Minimum Spanning Trees-20241003105211399.png]]


反證法

**假設：**
- e 是跨越一個 cut 的最小權重邊，但 e 不在 MST 中。
**證明：**
1. **加入 e 到 MST 會形成環：** 由於 MST 已經連通所有節點，加入任何一條邊都會形成環。
2. **環中必存在另一條跨越相同 cut 的邊 f：** 因為 e 跨越 cut，而加入 e 後形成的環必須再次跨越這個 cut 才能回到原本的子圖，因此環中必有另一條邊 f 也跨越這個 cut。
3. **移除 f 並加入 e 會形成一個權重更小的生成樹：** 由於 e 是跨越 cut 的最小權重邊，f 的權重必定大於等於 e。因此，移除 f 並加入 e 會得到一個總權重更小的生成樹。
4. **矛盾：** 這與 MST 的定義矛盾，因為 MST 的定義是權重最小的生成樹。
    
**結論：**
因此，跨越一個 cut 的最小權重邊 e 必定屬於 MST。


![[IMG-cs61b Minimum Spanning Trees-20241003105211524.png]]
圖示說明：
黑色粗線： 代表 MST 中的邊。
紅色線： 代表邊 f，它在 MST 中且跨越相同的 cut。
紅色虛線： 代表邊 e，它是跨越 cut 的最小權重邊，但不在 MST 中。
灰色線： 代表圖中其他邊。
灰色圓圈： 代表圖中的節點。
白色圓圈： 代表被 e 連接的兩個子圖。



**演算法步驟：**
1. **從一個空的 MST 開始：** 一開始 MST 中沒有任何邊。
2. **尋找一個沒有任何邊在 MST 中的 cut：** cut 是將圖的節點分成兩個不相交子集的劃分。
3. **將跨越這個 cut 的最小權重邊加入 MST：** 確保加入的邊不會形成環。
4. **重複步驟 2 和 3 直到 MST 中有 V-1 條邊：** V 是圖中節點的數量。
![[IMG-cs61b Minimum Spanning Trees-20241003105215345.png]]


# Prim’s Algorithm

demo link -> [CS61B Prim's Demo (Conceptual) - Google 簡報](https://docs.google.com/presentation/d/1NFLbVeCuhhaZAM1z3s9zIYGGnhT4M4PWwAc-TLmCJjc/edit#slide=id.g9a60b2f52_0_0)
![[IMG-cs61b Minimum Spanning Trees-20241003105218629.png]]



## prime demo (conceptual)
![[IMG-cs61b Minimum Spanning Trees-20241003105218719.png]]


random pick a node, say we choose 0
0 is white, other 是other, 這邊就是一個 cut

![[IMG-cs61b Minimum Spanning Trees-20241003105223160.png]]

0->2 這個 edge 就是一個最小 edge, 加入 MST
![[IMG-cs61b Minimum Spanning Trees-20241003105227039.png]]


另外0,2 形成另一個 組，其他是另外一組
下面標出所有的 crossing edge, 基本上就是 0-2往外 1 一個 edge的所有 edge
![[IMG-cs61b Minimum Spanning Trees-20241003105227140.png]]


一樣找出最小 weight, 就是 2-4的 weight 1 的那個 edge
加入上，下圖標黑色
update edgeTo
![[IMG-cs61b Minimum Spanning Trees-20241003105230556.png]]


0,2,4一組
1,3,5,6 是另一組
下面粉紅線是 crossing edge
哪一個要加入 MST?
![[IMG-cs61b Minimum Spanning Trees-20241003105230650.png]]

有兩個ans?
加入其中一個都會 work, but 不要兩個都加入
![[IMG-cs61b Minimum Spanning Trees-20241003105234410.png]]
補充：以這個例子，最後兩個 2 都會是在 MST 中。如果你把 1-3的11改為1, 你會發現過程中，這兩個2不會都在一個 MST中，會在2個 MST中（如果 edge 不是 unique的話, MST不一定只有一個）



下面粉紅線是 crossing edge, 找出最小的
![[IMG-cs61b Minimum Spanning Trees-20241003105234525.png]]

pick edge 3-6, minmal which is 1
update edgeTo
![[IMG-cs61b Minimum Spanning Trees-20241003105237262.png]]

下面粉紅線是 crossing edge, 找出最小的
![[IMG-cs61b Minimum Spanning Trees-20241003105239676.png]]



最小是 5-6 的那個 1
update edgeTo
![[IMG-cs61b Minimum Spanning Trees-20241003105239762.png]]


下一輪
![[IMG-cs61b Minimum Spanning Trees-20241003105242342.png]]

use 2
![[IMG-cs61b Minimum Spanning Trees-20241003105242450.png]]

![[IMG-cs61b Minimum Spanning Trees-20241003105244824.png]]


上面介紹的概念性的 Prim 每一次都要走過一很多 purple edges，太慢了

demo link: [CS61B Prim's Demo - Google 簡報](https://docs.google.com/presentation/d/1GPizbySYMsUhnXSXKvbqV4UhPCvrt750MiqPPgU-eCY/edit#slide=id.g9a60b2f52_0_0)
![[IMG-cs61b Minimum Spanning Trees-20241003105244925.png]]


## prime demo (impl)

start from 0
all vertices into PQ, sort by dist from tree
(從哪裡開始都沒差)
![[IMG-cs61b Minimum Spanning Trees-20241003105247194.png]]


init
![[IMG-cs61b Minimum Spanning Trees-20241003105249422.png]]




relax from 0
- 1 > 無限大? -> update to 1
- 2 > 無限大? -> update to 2
- PS: 這邊不需要從 source 去算(Dijkstra會從 source 去算)，只需要update to path 的 weight

udpate 無窮大 in pq, and update distTo and edgeTo 
also, need to ensure pq order need to in order by distTo after u update the distTo
![[IMG-cs61b Minimum Spanning Trees-20241003105249554.png]]

這一段變成 code 就是
```java

private void scan(EdgeWeightedGraph G, int v) {  
    marked[v] = true;  
  
    for (Edge e : G.adj(v)) {  
        // 這邊用 G.adj 不是拿到 vertex, 是拿到 Edge, 但是我們要的是 另一邊的 vertex        
        // 因此我們又用 e.other 拿另一邊的 vertex        int w = e.other(v);  
        if (marked[w]) {  
            continue;  
        }  
  
        // relax, 跟 Dijkstra 不同，這裡不需要從 source 開始，只需要從 跟目前的 edge weight 比  
        if (distTo[w] > e.weight()) {  
            distTo[w] = e.weight();  
            edgeTo[w] = e;  
            if (pq.contains(w)) pq.decreaseKey(w, distTo[w]);  
            else                pq.insert(w, distTo[w]);  
        }  
    }  
}

```
code: `/Users/re4388/project/personal/CS61B/cs61b_re4388/graph/PrimMST.java`
  





dotted line 表示 considering all out going edge from this vertex, not commit to MST yet
這邊還沒 commit to mst
![[IMG-cs61b Minimum Spanning Trees-20241003105254466.png]]


從 pq 推出 2:1, 這邊才 commit 進入 mst (透過 PQ 幫我們 sort shortest dist了)
(下面用 mark bold 表示)
另外也把接下來的 crossing line color 出來
![[IMG-cs61b Minimum Spanning Trees-20241003105254557.png]]


relax 還沒 relax的
update to 1, 15 in queue and distTo and edgeTo

PS: 下面的pq 的 order 也會一直改變 since we update the distTo
![[IMG-cs61b Minimum Spanning Trees-20241003105256764.png]]

![[IMG-cs61b Minimum Spanning Trees-20241003105256858.png]]



請問下一步, distTo, edgeTo and fringe 會如何改變
![[IMG-cs61b Minimum Spanning Trees-20241003105258893.png]]


ans:
pq 推出 4:1, commit to MST
![[IMG-cs61b Minimum Spanning Trees-20241003105301073.png]]


ans:
relax 3,5 6 vertex to 2,4,3
distTo and edgeTo 變化如下
update order in pq
![[IMG-cs61b Minimum Spanning Trees-20241003105301188.png]]


next, dotted line, 這次選左邊的 2 (0-1)
(也是 PQ 第一個)
![[IMG-cs61b Minimum Spanning Trees-20241003105302815.png]]


這邊可以發現，purple line 只有1條, and since 11 > 2 -> no need update
另外不用考慮 line 5 and 3, since 對應的另外一邊的 edge 已經被marked
這個算法就是可以避免一堆 purple line
![[IMG-cs61b Minimum Spanning Trees-20241003105302907.png]]

![[IMG-cs61b Minimum Spanning Trees-20241003105304976.png]]


commit (3,2)
![[IMG-cs61b Minimum Spanning Trees-20241003105305069.png]]

relax
![[IMG-cs61b Minimum Spanning Trees-20241003105306483.png|950]]



2 dotted line
![[IMG-cs61b Minimum Spanning Trees-20241003105306591.png]]

choose one (the one at the top of pq)
![[IMG-cs61b Minimum Spanning Trees-20241003105307996.png]]


last one, dotted line
![[IMG-cs61b Minimum Spanning Trees-20241003105308101.png]]


commited the last one (get from PQ) and done
![[IMG-cs61b Minimum Spanning Trees-20241003105309435.png]]


 主要差異是
 dijkstra 是 dist from souce
 prim 是 dist from tree
![[IMG-cs61b Minimum Spanning Trees-20241003105309536.png]]


## prim pseudocode
![[IMG-cs61b Minimum Spanning Trees-20241003105310760.png]]

![[IMG-cs61b Minimum Spanning Trees-20241003105310845.png]]


run time
![[IMG-cs61b Minimum Spanning Trees-20241003105312844.png]]


runtime 分析 跟 Dijkstra algo 一樣!
- insert and delete min
	- PQ ops -> log of (number of stuff in queue, here is vertex)
- update or decrease priority
	- PQ ops, 牽涉到你 relax 的數目，worse case, one node collect too all node, that's all edges -> so O(E), and each relax take logV time
且  in most case, edge > vertex -> so overall  `O(E * logV)`
![[IMG-cs61b Minimum Spanning Trees-20241003105314788.png]]



# Kruskal’s Algorithm
概念 demo [kruskals conceptual demo - Google 簡報](https://docs.google.com/presentation/d/1RhRSYs9Jbc335P24p7vR-6PLXZUl-1EmeDtqieL9ad8/edit)
實作 demo [kruskals realistic implementation demo - Google 簡報](https://docs.google.com/presentation/d/1KpNiR7aLIEG9sm7HgX29nvf3yLD8_vdQEPa0ktQfuYc/edit)
![[IMG-cs61b Minimum Spanning Trees-20241003105314948.png]]


## Kruskal  demo (conceptual)

sorted all edges (like 下面左邊的排序)
add egde one by one, as long as it does not cause a cycle
![[IMG-cs61b Minimum Spanning Trees-20241003105316815.png]]

cause MST cycle?
![[IMG-cs61b Minimum Spanning Trees-20241003105317707.png]]

first one, of-course no, add it
![[IMG-cs61b Minimum Spanning Trees-20241003105317822.png]]


conti
![[IMG-cs61b Minimum Spanning Trees-20241003105318807.png]]


no cycle, conti add it
![[IMG-cs61b Minimum Spanning Trees-20241003105318919.png]]


next one
![[IMG-cs61b Minimum Spanning Trees-20241003105319825.png]]


add it since no cycle
建立的過程，will not always connected all time
![[IMG-cs61b Minimum Spanning Trees-20241003105319966.png]]



conti
![[IMG-cs61b Minimum Spanning Trees-20241003105321064.png]]


add it
![[IMG-cs61b Minimum Spanning Trees-20241003105321192.png]]



conti and add it
![[IMG-cs61b Minimum Spanning Trees-20241003105322312.png]]


![[IMG-cs61b Minimum Spanning Trees-20241003105322394.png]]



conti 4-1
![[IMG-cs61b Minimum Spanning Trees-20241003105326702.png]]


not add it since it cause cycle
![[IMG-cs61b Minimum Spanning Trees-20241003105326844.png]]

conti
![[IMG-cs61b Minimum Spanning Trees-20241003105328324.png]]



add it
![[IMG-cs61b Minimum Spanning Trees-20241003105328460.png]]

done!
since we are finding MST, connect all vertex and at most need V-1 edge (V is the number of vertex), so it's done.



## Kruskal  demo (impl)


- need PQ to make each edge and their weight in order so we can process them in order
- use weight quick union to detect cycle
- finally, we have a MST to record what edge get added to mst and this is the result
![[IMG-cs61b Minimum Spanning Trees-20241003105329838.png]]



get from pq,  the first one, 
![[IMG-cs61b Minimum Spanning Trees-20241003105329932.png]]


no cycle, is 0,2 connected? no 
make 0,2 conncted and add to mst
![[IMG-cs61b Minimum Spanning Trees-20241003105330863.png]]



next one, 2-4
![[IMG-cs61b Minimum Spanning Trees-20241003105331377.png]]

check isConnected to detect cycle
add to mst and conncted(2,4)
![[IMG-cs61b Minimum Spanning Trees-20241003105331860.png]]

next one,the same
![[IMG-cs61b Minimum Spanning Trees-20241003105331970.png]]
![[IMG-cs61b Minimum Spanning Trees-20241003105332614.png]]



nxt one, the same
![[IMG-cs61b Minimum Spanning Trees-20241003105332697.png]]
![[IMG-cs61b Minimum Spanning Trees-20241003105333786.png]]


nxt, the same
![[IMG-cs61b Minimum Spanning Trees-20241003105333945.png]]

![[IMG-cs61b Minimum Spanning Trees-20241003105334453.png]]



get 1-4
![[IMG-cs61b Minimum Spanning Trees-20241003105334593.png]]

check isConnected to detect cycle
it is connected, -> do nothing
![[IMG-cs61b Minimum Spanning Trees-20241003105335306.png]]


nxt one, no connected, union them and add to mst
also, we get v-1 edge, done!
![[IMG-cs61b Minimum Spanning Trees-20241003105335467.png]]
![[IMG-cs61b Minimum Spanning Trees-20241003105335832.png]]


一個連在一起往外長
一個是想是草根運動一下，每個地方都開始長，最後匯聚起來
結果一樣
![[IMG-cs61b Minimum Spanning Trees-20241003105335957.png]]


- new mst, pq, uf
- pq 塞好 edges, sorted
- 只要 pq 還有東西 && mst size 還沒到 V-1
	- get edge from pq
	- 如果 v, w (v-w is 連著edge的兩個 vertex) 沒 conneted
		- union  v, w
		- add edge to mst
![[IMG-cs61b Minimum Spanning Trees-20241003105336371.png|973]]

- 下面的 `log*` V 的 `log*` 是 uf 那章說的 "一種 log 函數"，當 V 超級大，也不會大於 5
- isConnected 因為要看的是 v,w , 因此有 edge 才算，因此更要跑  num of edge 次，每一次要花 log* V, 因為有 V個的 vertex
![[IMG-cs61b Minimum Spanning Trees-20241003105336471.png]]


小節
![[IMG-cs61b Minimum Spanning Trees-20241003105336743.png|936]]


目前理論進展
![[IMG-cs61b Minimum Spanning Trees-20241003105336864.png]]