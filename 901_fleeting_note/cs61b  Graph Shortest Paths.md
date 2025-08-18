recap
- s-t connectivity
- s-t shortest path
  ![[IMG-cs61b  Graph Shortest Paths-20241003104933849.png]]

bfs 可以讓你有最短路徑 (not considering label for now)
![[IMG-cs61b  Graph Shortest Paths-20241003104948787.png]]

DFS and BFS 各有好壞

if graph 非常稀疏 -> DFS 需要 O(V) 的 mem 來做 遞迴呼叫的 stack
if graph 非常 brushy, BFS 會需要很大的 queue , 最多 O(V)

BTW, 不過我們目前實作會記錄 distTo, edgeTo, 因此已經會使用 O(V)
![[IMG-cs61b  Graph Shortest Paths-20241003105002176.png]]

之前討論過 google map
BFS 不是最短距離，是最少 edges
![[IMG-cs61b  Graph Shortest Paths-20241003105019684.png]]

因此如果用 BFS, 會用右下，距離更長
![[IMG-cs61b  Graph Shortest Paths-20241003105045258.png]]

解法?
要考慮 edge 的 label

# Dijkstra’s Algorithm

目視法找一下 0 到 5 最短路徑
![[IMG-cs61b  Graph Shortest Paths-20241003105111434.png]]

![[IMG-cs61b  Graph Shortest Paths-20241003105127755.png]]

先 assum
1. no cycle
2. no negative weight
![[IMG-cs61b  Graph Shortest Paths-20241003105136863.png]]


## SPT (最短路徑樹)

a _shortest-paths tree_ (SPT) is a subgraph containing s and all the vertices reachable from s that forms a directed tree rooted at s such that every tree path is a shortest path in the digraph.

找 0 到 all vertex 最短路徑
![[IMG-cs61b  Graph Shortest Paths-20241003105136952.png]]

你會發現，一個點通往所有點的最短路徑，會是一個樹(下面深黑色的線)!!! -> SPT
這個樹是到所有點的最短路徑的 union
![[IMG-cs61b  Graph Shortest Paths-20241003105144899.png]]

SPT 的有多少 edge?

![[IMG-cs61b  Graph Shortest Paths-20241003105152101.png]]

ans: V-1
-1 是減掉 source 自己
![[IMG-cs61b  Graph Shortest Paths-20241003105157672.png]]

找出 SPT
![[IMG-cs61b  Graph Shortest Paths-20241003105202735.png]]

ans: 下面粗黑線
另外紅色記號是 total dist from source to that point
![[IMG-cs61b  Graph Shortest Paths-20241003105209834.png]]

let's find a algo
and we can find SPT
![[IMG-cs61b  Graph Shortest Paths-20241003105213883.png]]

algo1: 原本的 dfs
dfs 找
先 a, b, c
已經 marked 就不找了
-> 結果， 沒有找到正確的 spt
PS: 這邊先找 B 是因為我們先找 alpha order, 但都可以
![[IMG-cs61b  Graph Shortest Paths-20241003105217272.png]]

algo2
引入 relaxation

what is relaxation?
去 v 的鄰居w(s)，update each w dist 的意思

when init source 到每個 node 都是無限大

一開始
a is source, -> 無限大 update to 0

so dfs(a)
a->b, update to 5
a->c, update to 1

到這邊 SPT 是 a->b, a->c

dfs(b)
b->a, update to 7
b->d, no update, 因為 5 + 3 = 8 （表示 a->b->a） 還大於本來的 0

到這邊 SPT 是 a->b, a->c, b->d

dfs(c)
c->b, from 5, update to 2 (1+1=2, 這邊距離是從 a 開始算喔), update SPT 到 b 變成 c->b
c->d, from 7, update to 6 , update SPT 到 b 變成 c->d

到這邊 SPT 是 a->c, c->b, c->d
=> 結果：不會得到正確的 spt


![[IMG-cs61b  Graph Shortest Paths-20241003105221651.png]]



---

algo 3, Dijkstra algo -> 先挑 best 的路

so dfs(a)
一開始, a is source, -> 無限大 update to 0
a->b, update to 5, a->c, update to 1
到這邊 SPT 是 a->b, a->c


"這邊開始跟 algo2不同"
因為 a->c 的距離是 1, 小於 a->b 的距離 5 -> 走 c

dfs(c)
c->b, from 5, update to 2 (1+1=2, 這邊距離是從 a 開始算), update SPT 到 b 變成 c->b
c->d, from 無限大, update to 6 , update SPT 到 b 變成 c->d

到這邊 SPT 是 a->c, c->b, c->d

這邊一樣選 best
c->b label 是1 -> 選這個， c->d 是 5, 比較大

dfs(b)
b->a, 2+3 > 0 -> 不 update
b->d,  1+1+2=4 < 6 -> update to 4, SPT update to b->d

到這邊 SPT 是 a->c, c->b, b->d
這就是正確的SPT

走法關鍵是：每一個 dfs 呼叫都會挑 lowest 的 node 去走
![[IMG-cs61b  Graph Shortest Paths-20241003105221730.png]]

## Dijkstra demo
[demo](https://www.google.com/url?q=https://docs.google.com/presentation/d/1_bw2z1ggUkquPdhl7gwdVBoTaoJmaZdpkV6MoAgxlJc/pub?start%3Dfalse%26loop%3Dfalse%26delayms%3D3000&sa=D&source=editors&ust=1724158259585381&usg=AOvVaw3aQefbwj0EBDz77piNXe2I)
![[IMG-cs61b  Graph Shortest Paths-20241003105225559.png]]

會用一個 minPq
dist 最小放前面
dequeue 出來的先走

一開始 at 0,
開始走
distTo 壓上 0
distTo init as 無限大

![[IMG-cs61b  Graph Shortest Paths-20241003105229046.png]]


看 0 的鄰居, 1,2
udpate their dist in pq, pq 因此也會調整順序
也要 update distTo(relax) and edgeTo

![[IMG-cs61b  Graph Shortest Paths-20241003105229158.png]]


edge 1比較短， 因此往 vertex 2走
![[IMG-cs61b  Graph Shortest Paths-20241003105232908.png]]


看 2 可以走的鄰居, 只有 5
udpate  dist in pq, pq 因此也會調整順序
也要 update distTo and edgeTo
![[IMG-cs61b  Graph Shortest Paths-20241003105233019.png]]


然後沒有往外面的箭頭了， 一路 ret 到 0
往 vertex 1 走
看 1  可以走的鄰居, 有 2,3,4, 

![[IMG-cs61b  Graph Shortest Paths-20241003105236198.png]]


1->2不會 update, 因為更遠了 (2+5 > 1), 只會 update 1->3  and 1->4
udpate  dist in pq, pq 因此也會調整順序
也要 update distTo and edgeTo

![[IMG-cs61b  Graph Shortest Paths-20241003105238844.png]]


然後這三個中，只有 vertex 3 and 4 還沒走，其中 weight 3 的路徑是 5 is best (或是直接從 pq 拿出來就是 4: 5, node4, weight5)
因此往node 4走

4一樣遇到三個, 2, 5, 6
![[IMG-cs61b  Graph Shortest Paths-20241003105241448.png]]


for, 2,5,6:
2 不用 update, 繞遠路(5+1 > 1)
6 update to 10 (from 無限大)
5 update to 9(5+4, 不用從 src 開始算, 5 看 distTo 的數值就是了, 4 是label)
![[IMG-cs61b  Graph Shortest Paths-20241003105244012.png]]


兩個還沒走過的5,6, 要選那一個?
5比較近 (since edge 4<5), 因此下一個是 vertex 5
![[IMG-cs61b  Graph Shortest Paths-20241003105246441.png]]


5 沒有outbound arrow, ret to 4
4 的 話，剩下6沒走，走6 (get from PQ)
6一樣做 relax to its 鄰居 3, update 3 的 dist to 11
![[IMG-cs61b  Graph Shortest Paths-20241003105248598.png]]


最後就是 處理 3
![[IMG-cs61b  Graph Shortest Paths-20241003105253588.png]]


done!
![[IMG-cs61b  Graph Shortest Paths-20241003105255847.png]]
# Dijkstra’s Correctness and Runtime

- 關鍵 invariants
  - edgeTo 裡面記錄著到 v 最好路徑的上一個 node
  - distTo 記錄著 從 source 到  v 的最好路徑距離
  - pq 記錄所有還沒 visit 的 vertices 的順序, 順序為 distTo
- 重要屬性 
	- visit vertices 按照順序（what oder? total dist from source)
	- 如果往已經 visited 過的 vertices, relax 會 fail
  

- sudo code
	- pq.add(sourceVertex, 0) // 0 is dist
	- for other vertices, pq.add(v, infinity)
	- while po is not empty
		- v= pq.removeSmallest()
		- relax v
- relax p  (say p -> q with weight w)
	- if(distTo[p] + w < distTo[q])  // 如果目前到 p 的 best dist +  w 比 d 目前到q的 best dist 少  -> 需要 relax
		- distTo[q] = distTo[p] + w
		- edgeTo[q] = p                                  // update edgeTo, 這個牽涉到路徑
		- [ ] pq.changePriority(q, distTo[q])     // update q 的 priortity 


algo book code:
```java

private void relax(EdgeWeightedDigraph G, int v) {
    for (DirectedEdge e : G.adj(v)) {
        int w = e.to();
        if (distTo[w] > distTo[v] + e.weight()) {
            distTo[w] = distTo[v] + e.weight();
            edgeTo[w] = e;
        }
    }
}
```

all code see
`/Users/re4388/project/personal/CS61B/cs61b_re4388/graph/DijkstraSP.java`

![[IMG-cs61b  Graph Shortest Paths-20241003105258087.png]]

上面的算法 required all edges 是非負
![[IMG-cs61b  Graph Shortest Paths-20241003105300318.png]]

一個概念上的證明
如果你挑的 edge 已經是最優，那你往後走，表示 visit 已走過的，你就會會至少多一個 非負的 edge, 因此不可能找到更優的
![[IMG-cs61b  Graph Shortest Paths-20241003105302274.png]]

下圖顯示為何 negative edge 會讓這個條件(relax visited vertex fail)失敗
![[IMG-cs61b  Graph Shortest Paths-20241003105304127.png]]




一個例子
![[IMG-cs61b  Graph Shortest Paths-20241003105305976.png]]


上面這個例子，你可以用 Dijkstra 去走
你會把 t 都走完了，然後走到 b 時，relax A, a的 dist 變成 -95
但是依照算法， a 已經 從 pq 拿出且走完了，因此這樣算法就壞掉了 XD

(Dijkstra 的算法，算是一個greedy 算法，你算是已經窮盡看完，一直走都會是那個時間點短的路徑，因此你如果回去看到 走過的 dist, 也不會更好，但是這個算法需要假設全部的 weight on edge 需要是 positive)









主要是 PQ 的 runtime
- 因為 pq 裡面會放 V (vertices counts)
- add -> logV
	- 共有 V 個，因此全部是 V * logV
- removeSmallest logV (to get node into PQ to releax)
	- 共有 V 個，因此全部是 V * logV
- changePriority
	- worse case 會有 E (edge count) 次, 因此是 E* logV
![[IMG-cs61b  Graph Shortest Paths-20241003105307528.png]]

# A\*

(發音 a start)

Dijkstra 不夠有效率
![[IMG-cs61b  Graph Shortest Paths-20241003105308887.png]]

不過因為 Dijkstra 會看所有的 places, get SPT
![[IMG-cs61b  Graph Shortest Paths-20241003105310208.png]]

如果只想要找一個點呢
![[IMG-cs61b  Graph Shortest Paths-20241003105311422.png]]

![[IMG-cs61b  Graph Shortest Paths-20241003105311715.png]]

跟 Dijkstra 都一樣
就是考慮要往那邊走的時候，我們本來會看 shorted dist, 多加上一個考慮: h(v, goal)
h(v, goal) 是指 source 到 goal 的一個 heristic, 一個估算，一個經驗值，以這題來說，可以是一個直線距離
![[IMG-cs61b  Graph Shortest Paths-20241003105314126.png]]

demo link [A\* Algorithm Demo - Google 簡報](https://docs.google.com/presentation/d/177bRUTdCa60fjExdr9eO04NHm0MRfPtCzvEup1iMccM/edit#slide=id.g771336078_0_180)

差異在於
- 我們要挑往那個 vertex 走時，除了考慮 distTo, 還要加上 heristic 的值, 加總後，那個低，往那邊走
- 沒有要建立 source to all vertices 的 最端路徑樹，而是只要往某個點找出最短路徑
![[IMG-cs61b  Graph Shortest Paths-20241003105314262.png]]


init state, s = 0, goal = 6
![[IMG-cs61b  Graph Shortest Paths-20241003105316339.png]]


relax 0 的 鄰居
![[IMG-cs61b  Graph Shortest Paths-20241003105316457.png]]


選擇往 vertex 1走 (use color to denote mark or unmark)
因為2+3 < 1+15,  dist + h 的值挑小的
![[IMG-cs61b  Graph Shortest Paths-20241003105317253.png]]


relax 1 的 鄰居
![[IMG-cs61b  Graph Shortest Paths-20241003105318172.png]]


往 vertex 4 走
因為 13+2 vs 5+1 -> 4 比較小

然後 relax 4  的鄰居, vertex 6 and 10
![[IMG-cs61b  Graph Shortest Paths-20241003105318271.png]]


這時候 vertex 6 已經是下一個要走的點 (下一個會被 dequeue 的)
done! 已經到目標 6
![[IMG-cs61b  Graph Shortest Paths-20241003105319248.png]]


走完後：
- 不會每個都走
- 結論不是 SPT
![[IMG-cs61b  Graph Shortest Paths-20241003105319327.png]]


地圖的話，heuristic 可以是直接距離
![[IMG-cs61b  Graph Shortest Paths-20241003105320324.png]]

線上展示差異
[PathFinding.js](https://qiao.github.io/PathFinding.js/visual/)
![[IMG-cs61b  Graph Shortest Paths-20241003105320448.png|779]]

Dijkstra
- good for SPT
- 如果只要找一個點，A* 較有效率
![[IMG-cs61b  Graph Shortest Paths-20241003105321678.png|623]]


A*
![[IMG-cs61b  Graph Shortest Paths-20241003105321779.png|473]]




# A start Heuristics (188 Preview)
note: [UCB CS188: Introduction to Artificial Intelligence - csdiy.wiki](https://csdiy.wiki/en/%E4%BA%BA%E5%B7%A5%E6%99%BA%E8%83%BD/CS188/)

if h(v, goal) is the same for every vertices, then A start degrade to Dijkstra
![[IMG-cs61b  Graph Shortest Paths-20241003105322789.png|841]]

bad heuristic even lead to incorrect result
![[IMG-cs61b  Graph Shortest Paths-20241003105322878.png|794]]

結論就是，如果 heuristics 不好， A\* 會有不好的表現
![[IMG-cs61b  Graph Shortest Paths-20241003105327265.png|807]]

![[IMG-cs61b  Graph Shortest Paths-20241003105327390.png|782]]

![[IMG-cs61b  Graph Shortest Paths-20241003105328882.png|788]]

![[IMG-cs61b  Graph Shortest Paths-20241003105329000.png]]
