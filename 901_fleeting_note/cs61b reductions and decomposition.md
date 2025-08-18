[[CS61B Data Structures and Algorithms|cs61b_index]]

to see article on A start algo, after checking the A start in course
- [ ] [https://www.redblobgames.com/pathfinding/a-star/introduction.html](https://www.redblobgames.com/pathfinding/a-star/introduction.html)




到目前學到的東西
- java
- asymptotic 分析
- 很多 ADT
![[IMG-cs61b reductions and decomposition-20241003104933908.png|828]]
    


why we learn algo?
![[IMG-cs61b reductions and decomposition-20241003104948841.png|826]]



![[IMG-cs61b reductions and decomposition-20241003105004850.png|772]]


![[IMG-cs61b reductions and decomposition-20241003105024479.png|863]]

DepthFirstPaths.java  [Demo](https://docs.google.com/presentation/d/1lTo8LZUGi3XQ1VlOmBUF9KkJTW_JWsw_DOPq8VBiI3A/edit?usp=sharing)
BreadthFirstPaths.java [Demo](https://docs.google.com/presentation/d/1JoYCelH4YE6IkSMq_LfTJMzJ00WxDj7rEa49gYmAtc4/edit?usp=sharing)
DijkstrasSP.java [Demo](https://docs.google.com/presentation/d/1_bw2z1ggUkquPdhl7gwdVBoTaoJmaZdpkV6MoAgxlJc/pub?start=false&loop=false&delayms=3000)|
A*: Same as Dijkstra’s but with h(v, goal) added to priority of each vertex. [Demo](https://docs.google.com/presentation/d/177bRUTdCa60fjExdr9eO04NHm0MRfPtCzvEup1iMccM/edit#slide=id.g369665031c_0_350)|



![[IMG-cs61b reductions and decomposition-20241003105024795.png|917]]

LazyPrimMST.java [Demo](https://docs.google.com/presentation/d/18leOHESniaJqqehiTR-YAL4WeEEcHJyRB9aw_S1FLG0/edit#slide=id.g772f8a8e2_0_28)|
PrimMST.java [Demo](https://docs.google.com/presentation/d/1GPizbySYMsUhnXSXKvbqV4UhPCvrt750MiqPPgU-eCY/edit#slide=id.g9a60b2f52_0_0)|
KruskalMST.java [Demo](https://docs.google.com/presentation/d/18leOHESniaJqqehiTR-YAL4WeEEcHJyRB9aw_S1FLG0/edit#slide=id.g5347e2c8f_2213)|



# Topological Sorting

下面的箭頭是表示一種依賴性
你一定要從0開始，才可以到1 or 3
或是從2開始，才可以到3 or 5

goal: a algo to help use to find a valid ordering for these tasks
![[IMG-cs61b reductions and decomposition-20241003105050829.png]]



一種可能是用 DFS..
![[IMG-cs61b reductions and decomposition-20241003105119490.png]]


另一種可能是找 in-bound degress 0 的 vertex 開始，然後開始 BFS

不過我們可以找到counter example, 如下圖，8 連到 7, 
這樣你 BFS 的地一輪就會是 0/2/8,  第二輪是1,3,5,7
這個alog無法達到..
![[IMG-cs61b reductions and decomposition-20241003105132327.png]]

答案
![[IMG-cs61b reductions and decomposition-20241003105140927.png]]


從 0 開始
走過的都要 goes into marked list (下面用顏色表示)
DFS post-order

0 -> 1 -> 4 -> 7, 
開始ret
postorder, so 7 go into list, then 4, then 1
還沒到0, 0 還有其他 branch 要走 
0 -> 3
開始 ret
3 進入 list
![[IMG-cs61b reductions and decomposition-20241003105141069.png]]


then 0 進入 list, 這邊都走完
換 node 2

2 -> 5 -> 6 開始 ret
6 進入 list, then 5, then 2

![[IMG-cs61b reductions and decomposition-20241003105148852.png]]


最後 reverse 這個 list -> 就獲得 topologocal ordering
![[IMG-cs61b reductions and decomposition-20241003105155000.png]]


what is topological sort?
就是你如果sort 好的順序排列，箭頭都會指向右邊
![[IMG-cs61b reductions and decomposition-20241003105200507.png]]


當我們說 dfs, 我們可能會只需要 restart or not, 這個看脈絡
![[IMG-cs61b reductions and decomposition-20241003105208623.png|903]]


我們要如何找到 vertices of indegree 0?
不需要, run DFS from 隨便一個 vertx 都會 work
![[IMG-cs61b reductions and decomposition-20241003105208718.png]]



![[IMG-cs61b reductions and decomposition-20241003105213056.png]]


PS: 這邊 post order 要留意, 有分支回來就要往另一邊走，類似 
0 -> 3 -> 4 -> 5, 開始 ret
5 into list
4 into list
3 不會進入 list
往 1 走, 往 2 走
2 into list
1 into list
3 into list
0 into list

so postOrder: 5,4,,2,1,3,0
![[IMG-cs61b reductions and decomposition-20241003105216622.png]]

only exit om DAG
![[IMG-cs61b reductions and decomposition-20241003105221189.png]]


# Shortest Paths on DAGs -> relax on Topological Order

複習一下 SPT
找下圖的 SPT
![[IMG-cs61b reductions and decomposition-20241003105224918.png]]


ans
![[IMG-cs61b reductions and decomposition-20241003105225106.png]]

if edge have negative
assume 從 vertex 2 還沒被 visit 開始
![[IMG-cs61b reductions and decomposition-20241003105228831.png]]

7->4 會 update  2 to -13
但是 7 +1 = 8 > 3 因此 不 update 5 的 3
![[IMG-cs61b reductions and decomposition-20241003105232541.png]]

這會造成 5 的 distTo 的最後值 3 是錯的
![[IMG-cs61b reductions and decomposition-20241003105236084.png]]


How to find SPT with negative weighted edge?
![[IMG-cs61b reductions and decomposition-20241003105238723.png]]

![[IMG-cs61b reductions and decomposition-20241003105241344.png]]


[CS61B DAG SPT Demo With Negative Edge - Google 簡報](https://docs.google.com/presentation/d/1CfnLS3FSXV8X2sXPTravZGXeBUUkcFQv7Uf2iGWGUfs/edit)

![[IMG-cs61b reductions and decomposition-20241003105243897.png]]


![[IMG-cs61b reductions and decomposition-20241003105246308.png]]



demo
依照 topo order 把 vertex 放入 fringe, 一個一個從前面拿出來的 relax
![[IMG-cs61b reductions and decomposition-20241003105248418.png]]



take 0 out, checl 0 連到的 3 and 1 是否需要 update
![[IMG-cs61b reductions and decomposition-20241003105248494.png]]

yes
![[IMG-cs61b reductions and decomposition-20241003105253478.png]]


take 3 out
![[IMG-cs61b reductions and decomposition-20241003105255741.png]]


only need to update from infinite to 2
![[IMG-cs61b reductions and decomposition-20241003105257961.png]]


next one is 1
![[IMG-cs61b reductions and decomposition-20241003105300202.png]]



update to 7
![[IMG-cs61b reductions and decomposition-20241003105302168.png]]



next is 2
會遇到 negative
但是因為 topo sort, 前面不會受到影響
![[IMG-cs61b reductions and decomposition-20241003105303902.png]]


update 兩個 to -13 and 8
![[IMG-cs61b reductions and decomposition-20241003105304004.png]]

next is 4
![[IMG-cs61b reductions and decomposition-20241003105305866.png]]

update to -12
![[IMG-cs61b reductions and decomposition-20241003105307405.png]]

done
![[IMG-cs61b reductions and decomposition-20241003105308833.png]]


run time
![[IMG-cs61b reductions and decomposition-20241003105308979.png]]



今天已經談到兩個新的算法了
![[IMG-cs61b reductions and decomposition-20241003105310323.png]]


# Longest Paths

LPT 的前提
- no cycle, o.w. you won't find it (you can just cycle it...)
![[IMG-cs61b reductions and decomposition-20241003105312279.png|903]]



LPT
- current best alog is ln exp
- one of the important unsolved problem
![[IMG-cs61b reductions and decomposition-20241003105312380.png]]


see a simpler question, LPT on DAG(有方向的無cycle圖)
![[IMG-cs61b reductions and decomposition-20241003105314394.png|815]]



1. make a new copy
2. we flip weighted edge sign in this new copy
3. run DAG SPT(上面的 relax on topologocal order) to get result X
4. flip the X distTo and edgeTo
5. done
![[IMG-cs61b reductions and decomposition-20241003105316579.png]]

![[IMG-cs61b reductions and decomposition-20241003105317383.png]]



demo

init state
![[IMG-cs61b reductions and decomposition-20241003105318388.png|631]]

flip
![[IMG-cs61b reductions and decomposition-20241003105318488.png|578]]

after DAG-SPT done
![[IMG-cs61b reductions and decomposition-20241003105319446.png|574]]

flip back
![[IMG-cs61b reductions and decomposition-20241003105319553.png|612]]




![[IMG-cs61b reductions and decomposition-20241003105320569.png|869]]
[Mathematical maturity - Wikipedia](https://en.wikipedia.org/wiki/Mathematical_maturity)




- [ ] to do
![[IMG-cs61b reductions and decomposition-20241003105320690.png]]

introduce in this course so far
![[IMG-cs61b reductions and decomposition-20241003105321914.png]]



# Reduction (170 Preview)

preprocss to G'
and we can solve it use DAG SPT
and poseeprocess to flip 
and get the result
![[IMG-cs61b reductions and decomposition-20241003105322050.png]]



這個問題可以 "化約" 為另一個我們已經會的問題
![[IMG-cs61b reductions and decomposition-20241003105323296.png]]


![[IMG-cs61b reductions and decomposition-20241003105327531.png]]




“If any subroutine for task Q can be used to solve P, we say P reduces to Q.”
[Polynomial-time reduction - Wikipedia](https://en.wikipedia.org/wiki/Polynomial-time_reduction)
![[IMG-cs61b reductions and decomposition-20241003105329153.png]]


more example
![[IMG-cs61b reductions and decomposition-20241003105330273.png]]




IND-SET
given a graph
find an independent set of K, where color vertices to red and no two vertices are adjacent
does it exisit and if it is exist, show it


![[IMG-cs61b reductions and decomposition-20241003105331014.png]]



給你一個 boolean formula
找出 這些 boolean 變數符合下面的條件
![[IMG-cs61b reductions and decomposition-20241003105331500.png]]



3SAT 問題可以化約約為indepentent set問題
- [ ] todo
![[IMG-cs61b reductions and decomposition-20241003105332085.png]]


- [ ] todo
![[IMG-cs61b reductions and decomposition-20241003105332184.png]]

- [ ] todo
![[IMG-cs61b reductions and decomposition-20241003105332811.png]]


化約甚至可以用在跨領域
3SAT is not graph 領域, IND-SET 是
但 IND-SET 可以解 3SAT
![[IMG-cs61b reductions and decomposition-20241003105332995.png]]




CS 和解決問題的核心！！
decompose, 
decompose 過程中所建立的 abstracton
![[IMG-cs61b reductions and decomposition-20241003105334096.png]]



![[IMG-cs61b reductions and decomposition-20241003105334757.png]]