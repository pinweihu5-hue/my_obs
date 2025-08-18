- [[cs61b graph related algo summary]]


Dijkstra 算法：求某一個 vertex 到某一個 vertex 的最小或最大的某個物理量, 最常見的就是最小距離 



adj list 資料結構
```ts

ar

                               vertex0                vertex2
adj: number[][][] = [  [[dist0, w0], [dist1, w1]],    [[dist2, w3]]     ]

每一個 vertex 對應到的 nei list
每一個 nei list 的 ele 是 [nei, dist]

下面就會用    for (nei of adj[n]) 去拿,  解構 const [dist, nei] = neiNode

```



### [743. Network Delay Time](https://leetcode.com/problems/network-delay-time/)

給定 n 個 node (label  1 到 n)
也給定一個 arr of arr, `[[2,1,1],[2,3,1],[3,4,1]]`
定義了 source node, target node 和 time travel from source to target
我們會從給定 node(`k`) 發送訊號
求最短的時間，可以讓訊號傳到所有的 node
如果傳不到所有的 node → 返回 -1

```ts fold

function networkDelayTime(times: number[][], n: number, k: number): number {

    //                              vertex0       vertex1
    // 建立 adj list 資料結構：[   [target0, w0], [target1, w1]]
    // 錯誤點: 題目已經有給你 node number 是 n 了, 用 n+1 因為是 1->n, 因此需要多一個空間, since arr idx 從 0 開始
    const adj = Array.from({ length: n + 1 }, () => [])
    for (let [u, v, w] of times) {
        adj[u].push([v, w])
    }
    // 建立 dist list, init will all Infinity 除了 起點k
    let dist = Array.from({ length: n + 1 }, () => Infinity)
    
    // 錯誤點: 沒有 init k
    dist[k] = 0


    // 建立 visit list, init will all false
    let visited = Array.from({ length: n + 1 }, () => false)

    // 建立 pq, use js array and shift 來做, 先塞入 起點, 資料結構：[[dist1, node1], [dist2, node2]]
    // 錯誤點：let pq = [[dist[k], k]], init, 距離是 0
    let pq = [[0, k]]

    // 開始 Dijastra
    // 只要pq 不為空, shift out vertex
    // if visited -> continue
    // add to visited 
    // 用 adjList 來 visit its nei
    // if newDist(dist + xx) < dist[?]
    // update it
    // also add to pq?
    // pq 也要重新 sort
    while (pq.length !== 0) {
        let [curDist, v] = pq.shift()

        if (visited[v]) continue
        visited[v] = true

        // 錯誤點：不是 loop thru adj[i], 是 loop thru 上面拿出來的 v 的鄰居
        // for (let i = 0; i < adj.length; i++) {
        for (let [nei, distNei] of adj[v]) {
            // let [distNei, nei] = adj[i]
            // 錯誤點 distNei + ??  忘記要加什麼 -> 應該要加 currentDist
            let newDist = distNei + curDist
            if (newDist < dist[nei]) {
                dist[nei] = newDist

                // 錯誤點：要把新的更新的推入pq
                pq.push([newDist, nei])
		
                pq.sort((a, b) => a[0] - b[0]) //這邊用 js list 模擬 queue, 每一次操作都要 sort
            }
        }
    }

    let maxDist = -Infinity;
    for (let i = 1; i <= n; i++) { // 1~n 個 node
        if (dist[i] === Infinity) {
            return -1; // 並非所有節點都可以到達, ret -1
        }
        maxDist = Math.max(maxDist, dist[i]);
    }
    return maxDist;


    // 錯誤點: 下面的寫法用 dist 來 loop 會錯
    // 因為第0個一定是 無限大

    //////
    // 上面跑完算法
    // 找出 dist 裡面最大的那個 dist
    // 那個 dist 就是訊號發出後要讓大家都要收到訊號的最短時間
    // let maxDist = -Infinity
    // console.log(dist)
    // for (let ele of dist) {
    //     console.log(ele)
    //     if (ele === Infinity) return -1
    //     maxDist = Math.max(maxDist, ele)
    // }
    // return maxDist


    // 這樣就可以，因為避開 第零個
    // let maxDist = -Infinity
    // console.log(dist)
    // for (let i = 1; i < dist.length; i++) {
    //     let ele = dist[i]
    //     console.log(ele)
    //     if (ele === Infinity) return -1
    //     maxDist = Math.max(maxDist, ele)
    // }
    // return maxDist
};

```



same logic, use MiniPQ
```js fold

function networkDelayTime(times: number[][], n: number, k: number): number {
    let adj = setupDirectedGraph(times, n)
    // console.log(adj)

    let dist = Array.from({ length: n + 1 }, () => Infinity)
    dist[k] = 0

    let visit = Array.from({ length: n + 1 }, () => false)

    let pq = new MinPQ(((a, b) => a[1] - b[1]))
    pq.enqueue([k, 0])

    while (!pq.isEmpty()) {
        let [n, curDist] = pq.dequeue()

        if (visit[n]) continue
        visit[n] = true

        for (let [nei, neiDist] of adj[n]) {
            let newDist = neiDist + curDist
            if (newDist < dist[nei]) {
                dist[nei] = newDist
                pq.enqueue([nei, dist[nei]])
            }
        }
    }
    let res = -Infinity
    for (let i = 1; i <= n; i++) {
        if (dist[i] === Infinity) return -1
        res = Math.max(res, dist[i])
    }
    return res
};


function setupDirectedGraph(times, n) {
    // 1-based -> n+1
    // directed edges 
    let adj = Array.from({ length: n + 1 }, () => [])
    for (let [u, v, w] of times) {
        adj[u].push([v, w])
    }
    return adj
}



class MinPQ {
    pq
    comparator
    n
    constructor(comparator?) {
        this.n = 0
        this.pq = []
        this.comparator = comparator || ((a, b) => a - b)
    }
    size() {
        return this.n
    }

    isEmpty() {
        return this.size() === 0
    }

    enqueue(val) {
        this.n++
        this.pq[this.n] = val
        this.swim(this.n)
    }


    dequeue() {
        let retVal = this.pq[1]
        this.swap(this.n, 1)
        this.n--
        this.sink(1)
        return retVal
    }


    peak() {
        return this.pq[1]
    }


    parent(k) {
        return Math.floor(k / 2)
    }



    sink(k) {
        while (2 * k <= this.n) {
            let kid = k * 2

            if (kid < this.n && this.bigger(kid, kid + 1)) {
                kid++
            }

            if (this.bigger(kid, k)) break
            this.swap(kid, k)
            k = kid
        }
    }

    swim(k) {
        while (k > 1 && this.bigger(this.parent(k), k)) {
            this.swap(this.parent(k), k)
            k = this.parent(k)
        }
    }



    bigger(i, j) {
        return this.comparator(this.pq[i], this.pq[j]) > 0
    }



    swap(i, j) {
        let tmp = this.pq[i]
        this.pq[i] = this.pq[j]
        this.pq[j] = tmp
    }
}


```




same logic as ver0 just use my own PQ)
```ts fold

function networkDelayTime(times: number[][], n: number, k: number): number {
    let adj = setupDirectedGraph(times, n)

    let dist = Array.from({ length: n + 1 }, () => Infinity)
    dist[k] = 0

    let visit = Array.from({ length: n + 1 }, () => false)

    let pq = new MinPQ(((a, b) => a[1] - b[1]))
    pq.enqueue([k, 0])

    while (!pq.isEmpty()) {
        let [n, curDist] = pq.dequeue()

        if (visit[n]) continue
        visit[n] = true

        for (let [nei, neiDist] of adj[n]) {
            let newDist = neiDist + curDist
            if (newDist < dist[nei]) {
                dist[nei] = newDist
                pq.enqueue([nei, dist[nei]])
            }
        }
    }
    let res = -Infinity
    for (let i = 1; i <= n; i++) {
        if (dist[i] === Infinity) return -1
        res = Math.max(res, dist[i])
    }
    return res
};

function setupDirectedGraph(times, n) {
    // 1-based -> n+1
    // directed edges 
    let adj = Array.from({ length: n + 1 }, () => [])
    for (let [u, v, w] of times) {
        adj[u].push([v, w])
    }
    return adj
}


class MinPQ {
    pq
    comparator
    n
    constructor(comparator?) {
        this.n = 0
        this.pq = []
        this.comparator = comparator || ((a, b) => a - b)
    }
    size() {
        return this.n
    }

    isEmpty() {
        return this.size() === 0
    }

    enqueue(val) {
        this.n++
        this.pq[this.n] = val
        this.swim(this.n)
    }


    dequeue() {
        let retVal = this.pq[1]
        this.swap(this.n, 1)
        this.n--
        this.sink(1)
        return retVal
    }


    peak() {
        return this.pq[1]
    }


    parent(k) {
        return Math.floor(k / 2)
    }



    sink(k) {
        while (2 * k <= this.n) {
            let kid = k * 2

            if (kid < this.n && this.bigger(kid, kid + 1)) {
                kid++
            }

            if (this.bigger(kid, k)) break
            this.swap(kid, k)
            k = kid
        }
    }

    swim(k) {
        while (k > 1 && this.bigger(this.parent(k), k)) {
            this.swap(this.parent(k), k)
            k = this.parent(k)
        }
    }



    bigger(i, j) {
        return this.comparator(this.pq[i], this.pq[j]) > 0
    }



    swap(i, j) {
        let tmp = this.pq[i]
        this.pq[i] = this.pq[j]
        this.pq[j] = tmp
    }
}

```




錯誤記錄：
```js fold
if (kid < this.n && this.bigger(kid, kid+1)) {  // fixed
// if (kid < this.n && this.bigger(kid, k)) {   // 本來是恍神嗎XD



// let pq = new MiniPQ( ( (a, b) => a - b ) )    // 原本寫法
let pq = new MiniPQ( ( (a, b) => a[1] - b[1] ) ) // fixeed  -> 這邊很容易忘記我要比較的東西不是最外層






// 誤會題意 -> 寫成 sum up all dist from
// 我們要找的是從 k 到 all pts 的最短時間 -> 就是要找從 k 到 最花時間的那個 pt 的時間，因為這個 pt 都到了，就全部都到了
    let res = 0
    for (let i = 1; i <= n; i++) {
        let d1 = dist[i]
        if (d1 === Infinity) return -1
        res += d1
    }
    return res


```


---

### [1514 Path with Maximum Probability](https://leetcode.com/problems/path-with-maximum-probability/description/)
給定無向圖, n 個 node, (0-indexed)
用  `edges[i] = [a, b]`  list 來表示，a 連到 b
用 `succProb[i]` 表示這兩個 edge 間的機率

也給定 start node and end node,
求最大的機率從 start node 移動到 end node 然後返回機率

如果找不到 path -> 返回0


If there is no path from start to end, return 0. Your answer will be accepted if it differs from the correct answer by at most 1e-5.

實作
`function maxProbability(n: number, edges: number[][], succProb: number[], start_node: number, end_node: number): number {}`

例子
![[IMG-graph_Dijkstra-20250810200102739.png|564]]

無法抵達的例子

![[IMG-graph_Dijkstra-20250810200106977.png]]


ver0
```ts fold

function maxProbability(
  n: number,
  edges: number[][],
  succProb: number[],
  start_node: number,
  end_node: number
): number {
  // 建立 adj list，儲存每個節點的鄰居和邊的機率
  // 題目本身就是的 node 是 zero-indexed 的, 所以這裡可以直接用 n as length
  const adj: number[][][] = createUndirectedGraph(n, edges, succProb);

  // 初始化距離陣列，儲存從起點到每個節點的最大成功機率
  const prob: number[] = Array.from({ length: n }, () => 0);
  prob[start_node] = 1; // 起點到起點的機率為 1

  // 初始化优先队列，使用自定义比较器
  const pq = new MinPQ((a, b) => b[1] - a[1]); // prob is at idx 1

  // Dijkstra 算法
  pq.enqueue([start_node, 1]); // 将起點加入隊列, 起點到起點的機率為 1
  while (!pq.isEmpty()) {
    const [n, curProb] = pq.dequeue(); // 取出隊首元素

    // 如果當前節點是終點，抵達 ret 機率
    if (n === end_node) return curProb;

    // 遍歷當前節點的所有鄰居
    for (const [nei, neiProb] of adj[n]) {
      // get new prob, dist 是相加，這邊依照題目要相乘
      const newProb = curProb * neiProb;

      // 如果新的機率大於當前鄰居的機率，則更新距離陣列和隊列
      if (newProb > prob[nei]) {
        prob[nei] = newProb;
        pq.enqueue([nei, newProb]);
      }
    }
  }

  // 如果無法到達終點，則返回 0
  return 0;
}

function createUndirectedGraph(n, edges, succProb) {
  let adj = Array.from({ length: n }, () => []);
  for (let i = 0; i < edges.length; i++) {
    const [u, v] = edges[i];
    adj[u].push([v, succProb[i]]);
    adj[v].push([u, succProb[i]]);
  }
  return adj;
}

class MinPQ {
  pq;
  n;
  comparator;

  constructor(comparator) {
    this.pq = [];
    this.n = 0; // number of items on priority queue
    let defaultASC = (a, b) => a - b;
    this.comparator = comparator || defaultASC;
  }

  isEmpty() {
    return this.n === 0;
  }

  size() {
    return this.n;
  }

  peek() {
    if (this.isEmpty()) throw new Error("Priority queue underflow");
    return this.pq[1]; // 我們把第一個 ele 放在 idx 1, 這樣會比較處理, since parent become k/2
  }

  enqueue(val) {
    // 先 increment x
    this.n++;
    // 再把 val 塞到這個新的空間
    this.pq[this.n] = val;
    this.swim(this.n);
  }

  dequeue() {
    if (this.isEmpty()) throw new Error("Priority queue underflow");
    // 先把第一個（從樹的角度，就是最上面那個node）拿出來, 後面會 ret
    const min = this.pq[1];

    // 把最下面那個放到移除的最上面那個位置 via swap
    this.swap(1, this.n);

    // 拔掉下面的 (上面存起來的，等等可以 ret)
    this.n--;

    this.sink(1); // then sink

    return min;
  }

  parent(k) {
    return Math.floor(k / 2); // 錯誤點-> 忘記 Math.floor
  }

  // 往上游
  swim(k) {
    // 当 k == 1 时， 意味着该元素已经是根节点了， 不可能再向上调整， 因此循环条件不需要包含 k == 1 的情况
    // 且 parent 還比 k大，就繼續往上走 (min PQ, 越上面應該越小 )
    while (k > 1 && this.greater(this.parent(k), k)) {
      this.swap(this.parent(k), k); // 透過 swap 往上(前)移動
      k = this.parent(k); // update k to its parent, 然後繼續 while loop
    }
  }

  // 往下沉
  sink(k) {
    // 只要 k * 2 還沒有到最後一個位置就繼續往下走
    // 錯誤記錄：2 * k < this.n -> 少了等於
    // 這邊要用 <= this.n, 因為 2* k 還是需要跟最後一個node去進行比較
    while (2 * k <= this.n) {
      // 下面都用 kid ptr 做事情
      let kid = 2 * k;

      // 我們要讓 kid ptr 走到比較大的那個 kid
      // 因為 kid 有兩個可能的位置, 如果 kid 的值還比 kid+1 大, 大的應該要放在後面
      // 因此我們要把 kid++
      if (kid < this.n && this.greater(kid, kid + 1)) {
        kid++;
      }

      // 下面判斷是否已經到正確的位置

      // 如果 k 已經比 kid 小 -> 已經到正確的位址 -> break
      if (this.greater(kid, k)) {
        break;
      }
      // 如果沒有到正確的位置 -> swap 往下(後)移動
      this.swap(k, kid);

      // 更新 k as kid -> 繼續下一個 iteration
      k = kid; // 這裡要用 kid, 因為你要拿到最上面的 ref, 不可以用k*2, k 已經改變了
    }
  }

  // helper
  greater(i, j) {
    return this.comparator(this.pq[i], this.pq[j]) > 0;
  }

  swap(i, j) {
    const tmp = this.pq[i];
    this.pq[i] = this.pq[j];
    this.pq[j] = tmp;
  }
}


```


TIL
- 0-indexed 的話，不需要 n+1 to init arr
- adj list 的 type  triple arr  -> `type AdjList = number[][][]`
- Dijkstra 也可以求 max prob
- update prob 是 `curPob * neiProb`




錯誤記錄
```js

sink(k) {
	while (k * 2 <= this.n) {
		let kid = k * 2
		if (kid < this.n && this.bigger(kid, kid + 1)) {
			kid++
		}


		// 如果 kid 比 k 大，不要忘了, kid = k*2, 
		// 所以 k*2, 後面在 minPQ(越前面應該越小), 比 k 大，表示 這個結構已經 sorted
		// 因此就不需要調整了  -> break
		if (this.bigger(kid, k)) break
		// if (this.bigger(k, kid)) break  寫錯的邏輯
		this.swap(k, kid)
		k = kid
	}
}

```


錯誤記錄
let comparator = (a, b) => a[1] - b[1] -> 錯誤, 要由大到小, 因為我們要先推出 prob 大的, 這題要求 max prob
`const pq = new MinPQ((a, b) => b[1] - a[1]); // prob is at idx 1`


錯誤記錄
題目有給end_node
忘記加下面這行，只有在最後去 prob 拿答案
但是這樣 TLE
這題不需要把全部的 prob 都算完，只要抵達 end_node 就可以ret 了
`if(curNode === end_node) return curProb`


錯誤記錄
```ts
if (kid < this.n && this.bigger(k, k + 1)) {  
	kid++
}
```
應該是 this.bigger(kid, kid + 1)
這邊是比較 kid 需不需要 ++, 因此應該要用 kid, kid+1來比較