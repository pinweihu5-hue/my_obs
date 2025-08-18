---  
alias:  
creation_date: 2024-10-09 12:37  
tags: 
related:
- [[cs61b Disjoint Sets, quick union]]

---  

總結
- 也用在 graph 的 Kruskal algo to solve MST (最小生成樹)
	- [[cs61b graph related algo summary]]


## 題目
### [684. Redundant Connection](https://leetcode.com/problems/redundant-connection/)
給一個 list of list, 表示一個 undirected graph 且會有 cycle
找出一個 edge, 移除這個edge, 剩下的 graph是一個tree, (tree不可以有 cycle)
![[IMG-Disjoint Set-20250810200102683.png]]
```ts fold

function findRedundantConnection(edges: number[][]): number[] {
    let qu = new QuickUnion(edges.length)

    for (let [p, q] of edges) {
        if (!qu.isConnected(p, q)) {
            qu.connect(p, q)
        } else {
            return [p, q]
        }
    }
};


class QuickUnion {
    // ref
    // /Users/re4388/project/personal/CS61B/cs61b_re4388/disjointSets/WeightedQuickUnionPathCompression.java
    // parents 的 idx 是 node val
    // perents 的 val 是那個 node 的 parent idx
    // 如果 node val 是 -1 表示此 node 是 root node
    // 如果 node val 是 -3, 表示此 node 是 root node with 3 kid nodes
    // 如果 node val 是  2, 表示 此 node 的 parent idx 2 
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




---


more on union find: [Graph Series : Part 1 union find - LeetCode Discuss](https://leetcode.com/discuss/career/5876587/graph-series-part-1-union-find)