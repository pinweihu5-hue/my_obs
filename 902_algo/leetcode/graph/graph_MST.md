related: [[cs61b Minimum Spanning Trees]]



### [1584. Min Cost to Connect All Points](https://leetcode.com/problems/min-cost-to-connect-all-points/)
給定一些 2-d points  (arr of arr form)
我們定義這些點之間的距離的成本是 manhattan dist (計算方式如題)

請求出讓每個點都連起來的路線的最小成本
(其實就是求 MST, 每一個點就是 vertex)

example
**Input:** points = `[[0,0],[2,2],[3,10],[5,2],[7,0]]`
**Output:** 20
![[IMG-graph_MST-20250810200102743.png|303]]


```js fold

var minCostConnectPoints = function (points) {
    let vertexNumber = points.length

    let edges = []  // dist, p1_idx, p2_idx 
    for (let i = 0; i < points.length; i++) {
        for (let j = i+1; j < points.length; j++) {
            let p1 = points[i]
            let p2 = points[j]
            edges.push([getDist(p1, p2), i, j])
        }
    }


    let qu = new QuickUnion(vertexNumber)

    // dist, small -> largest
    edges.sort((a, b) => a[0] - b[0])
    
    // Kruskal's algorithm
    let cost = 0
    let connectedEdges = 0
    for(let [dist, p1_idx, p2_idx] of edges){

        if(!qu.isConnected(p1_idx, p2_idx)){
            qu.connect(p1_idx, p2_idx)
            cost+=dist
            connectedEdges++
            if(connectedEdges === vertexNumber-1) break
        } 
    }

    return cost
};


function getDist(p1, p2){
    let [x1, y1] = p1
    let [x2, y2] = p2
    return Math.abs(x1-x2) + Math.abs(y1-y2)
}


class QuickUnion {
    // ref
    // /Users/re4388/project/personal/CS61B/cs61b_re4388/disjointSets/WeightedQuickUnionPathCompression.java
    // parents 的 idx 是 node val
    // perents 的 val 是那個 node 的 parent idx
    // 如果 node val 是 -1 表示此 node 是 root node
    // 如果 node val 是 -3, 表示此 node 是 root node with 3 kid nodes
    // 如果 node val 是  2, 表示 此 node 的 parent at idx 2 
    // node 的 idx 才是本身的 node 的值
    parents
    constructor(N) {
        // init all -1, negative number means root
        // -3 means this root have 3 nodes
        this.parents = Array.from({ length: N }, () => -1)
    }

    connect(p, q) {
        let pRoot = this.getRoot(p)
        let qRoot = this.getRoot(q)

        let pRootVal = this.parents[pRoot]
        let qRootVal = this.parents[qRoot]

        let newSize = -1 * (Math.abs(pRootVal) + Math.abs(qRootVal))

        //  看誰值比較小，就是小樹
        if (Math.abs(pRootVal) < Math.abs(qRootVal)) { // p 是小樹
            /**
                0 1 2 3
                p   q
                -3  -2  ...

                0 1 2 3
                p   q 
                -3  -2  ... 
                把 p改為 q 的 idx
            */
            this.parents[pRoot] = qRoot

            // update q 的 size
            this.parents[qRoot] = newSize
        } else {
            this.parents[qRoot] = pRoot
            this.parents[pRoot] = newSize
        }

    }

    isConnected(p, q) {
        return this.getRoot(p) === this.getRoot(q)

    }


    // 沒有 path compression 的版本
    getRoot(p) {
        let cur = p
        // 只要跳過去的那個值非負, 就繼續下去
        while (this.parents[cur] >= 0) {
            cur = this.parents[cur]
        }
        return cur
    }

    // 這個是有 path compression 的版本，會更快
    // getRoot(p){
    //     // 走過的可以存在這裡
    //     let visitHistory=[]

    //     let cur = p
    //     while(this.parents[cur] >= 0){
    //         visitHistory.push(cur)  // <---存進去
    //         cur = this.parents[cur]
    //     }

    //     // 把走過的這些 node 的 par 都改為 root
    //     for(let ele of visitHistory){
    //         this.parents[ele] = cur
    //     }

    //     return cur
    // }


    // 錯誤記錄:
    // getRoot(p){
    //     let cur = p
    //     while(cur >= 0){ // <---寫錯了
    //         cur = this.parents[cur]
    //     }
    //     return cur
    // }

}


```



錯誤記錄
我忘記了 qu.connect(xxx, yyy)
這邊的 xxx, yyy 到底要記錄什麼
答案:
這邊要記錄的是 pt1_idx and pt2_idx
也因此我們要建構出的 array 是 `[[dist_12, pt1_idx, pt2_idx], [dist_13, pt1_idx, pt3_idx], ...]`


錯誤記錄
```ts

本來我是這樣處理
只避開 i==j 的情況，就是把全部都連在一起
但是如果 pt0 在第一個 loop 已經連到 pt3
那你 輪到 pt3的 loop, 又可以連到 pt0, 不就已經重複連了嗎
因此這樣處理是錯誤的

for (let i = 0; i < points.length; i++) {
	for (let j = 0; j < points.length; j++) {
		if (i === j) continue // 不用處理自己對自己的距離計算
		distList.push([i, j, getManhattanDist(i, j, points)])
	}
}


只需要如正解 i=0到底, nest loop j=i+1到底, 每一個點就互相連上且算了 dist了

 for (let i = 0; i < numPoints.length; i++) {
        for (let j = i + 1; j < numPoints.length; j++) {
        }
    }
```

如下
![[IMG-graph_MST-20250810200106980.png|403]]



錯誤記錄
```js fold

connect(p, q) {
	let pRoot = this.getRoot(p)
	let qRoot = this.getRoot(q)

	let pRootCount = this.par[pRoot]
	let qRootCount = this.par[qRoot]

	let curTotalCount = Math.abs(pRootCount) + Math.abs(qRootCount)


	if (Math.abs(pRootCount) < Math.abs(qRootCount)) {
		// 把 p 接去 q
		this.par[pRoot] = qRoot
		this.par[qRoot] = curTotalCount   //  <- 這邊塞 size 回去沒有 * -1 -> 會讓 getRoot 無限 cycle
	} else {
		this.par[qRoot] = pRoot
		this.par[pRoot] = curTotalCount   //  <- 這邊塞 size 回去沒有 * -1
	} 

}



// 這邊觀念不夠清楚，一直懷疑是這邊寫錯
    getRoot(p) {
    
        let val = p

		// 當 this.par[val] 等於 neg 時，就不會跑了，so, val 還會是正的z
        while (this.par[val] >= 0) {
            val = this.par[val]
        }
        return val
    }


```


錯誤記錄
除非遇到 negative 才可以停
```js

    getRoot(k) {
        let j = k
        while (this.par[j] > 0) {  // 要改為：while (this.par[j] >= 0)
            j = this.par[j]
        }
        return j
    }


```