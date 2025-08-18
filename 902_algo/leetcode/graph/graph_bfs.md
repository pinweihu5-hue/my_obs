

### [787. Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/)
Q: 
有 n 個 city
給定 flights arr

`flights[i]` 裡面是一個 arr, 表示 `from_node`, `to_node`, `price`
表示從 a→b的價格
給定 src, dst and k
求從 src 到 dist, 最多 k stops 下的最便宜的價格
如果沒有辦法到，返回 -1

![[IMG-graph_bfs-20250810200102748.png|668]]



less comment ver
```js fold

var findCheapestPrice = function (n, flights, src, dst, k) {
    let adj = createDirectedGraph(n, flights)
    let price = Array.from({ length: n }, () => Infinity)
    price[src] = 0

    let q = [[src, 0, 0]] // city, price, stopTime
    while (q.length !== 0) {

        let [n, curPrice, curStopTime] = q.shift()
        
        if (curStopTime > k) continue

        for (let [nei, neiPrice] of adj[n]) {
            let newPrice = curPrice + neiPrice
            if (newPrice < price[nei]) {
                price[nei] = newPrice
                q.push([nei, newPrice, curStopTime + 1])
            }
        }
    }

    if(price[dst]===Infinity) return -1
    else return price[dst]

};



function createDirectedGraph(n, flights) {
    let adj = Array.from({ length: n }, () => []) // zero based -> 0
    for (let [from, to, price] of flights) {
        adj[from].push([to, price])
    }
    return adj
}

```



```ts fold

function findCheapestPrice(n, flights, src, dst, k) {
  const adj = directGraph(n, flights);

  // 初始化距離数组，將所有城市到起點城市的距離初始化為最大值, 因為我們要求最小花費
  const cost: number[] = Array(n).fill(Infinity);
  // 起點城市到自身的花費是 0
  cost[src] = 0;

  // 使用隊列進行廣度優先搜索 (BFS)，隊列中存儲 [城市，停靠次數，花費]
  const q = [[src, 0, 0]];

  while (q.length !== 0) {
    // 從隊列中取出第一个元素
    const [curCity, stopTime, curCost] = q.shift()!;

    // 如果已经达到最大允许停靠次數，就跳過，不會進行 graph traversal
    if (stopTime > k) continue;

    // 遍历当前城市的所有鄰居
    for (const [nei, price] of adj[curCity]) {
      
      // 計算到達鄰居城市的新花費
      const newCost = curCost + price; // 錯誤記錄：這邊寫成 cost[nei] + curCost
      
      if (cost[nei] > newCost) {
        // 如果新花費比當前記錄的到鄰居城市的花費更低
        cost[nei] = newCost; // 則更新距離数组
        q.push([nei, stopTime + 1, newCost]); // 將鄰居城市加入隊列
      }
    }
  }

  // 如果不可达則返回 -1, o.w 返回到达目的地城市的最低花費
  return cost[dst] === Infinity ? -1 : cost[dst];
}


function directGraph(n: number, flights: number[][]) {
  // from example, you can see city is 0-indexed, so length is n
  const adj = Array.from({ length: n }, () => []);

  // 將航班信息轉換為鄰接表表示
  for (const [from, to, price] of flights) {
    adj[from].push([to, price]);
  }

return adj;
}

```

TIL
- 建立 directedGraph
- 很典型的 adj traversal, 走完後， dist array 都會被 update, 然後就去那邊拿答案
- 這題算法跟 Dijkastra的差異在哪裡?
	- dijkastra 需要用 pq 來 maintain min/max value 來推出使用
	- 剩下的邏輯很類似
- update newDist 的邏輯要看題目
	- flight 這題是 newCost = currentCost + price
	- 最大機率那提示 newProb = curProb * neiborProb

[[為何  Cheapest Flights Within K Stops 要用 BFS 解，而不是用 Dijkstra 算法解？我要如何分辨 ]]



錯誤記錄：
```ts

const newCost = curCost + price; // 錯誤記錄：這邊寫成 cost[nei] + curCost

```
新的 cost 應該是 目前 node 的 cost 加上 price



錯誤記錄:
```js

// 錯誤觀念, 沒有要 early return, 要全部都放到pricing arr 最後再去那邊拿
// if (n === dst) return curPrice

// 這邊判斷錯誤, ===k 的情況下，你還是希望會往下走去 update arr 
if (curStopTime > k) continue
// if (curStopTime >= k) continue

```


## 2分圖 isBipartite

note: 也可以用 dfs 解，只是這邊選擇用 bfs
有興趣可以看下去


### [Is Graph Bipartite?](https://leetcode.com/problems/is-graph-bipartite/description/)
Q: 無向圖 n 個 node
0-indexed, n from 0 ~ n-1
題目提供 graph arr, like `edges`
graph[u] 是 array of node, that node `u` is adjacent to


```ts fold

function isBipartite(adj: number[][]): boolean {
    const n = adj.length;

    // 用染色來看是否可以是二分圖, // 0: 未染色, 1: 紅色, -1: 藍色
    const colors = Array.from({ length: n }, () => 0);

    // 遍歷所有節點
    for (let i = 0; i < n; i++) {
        // 如果當前節點未染色，則進行廣度優先搜索
        if (colors[i] === 0) {
            if (!bfs(i, adj, colors)) return false; // 如果廣度優先搜索返回 false，則表示圖不是二分圖
        }
    }

    return true; // 所有節點都已染色，且沒有衝突，表示圖是二分圖
}

// 廣度優先搜索函式
function bfs(startNode: number, adj: number[][], colors: number[]): boolean {
    const q = [startNode]; // 初始化队列，將起始節點加入队列
    colors[startNode] = 1; // 將起始節點染成紅色

    while (q.length !== 0) {
        const n = q.shift()!; // 從队列中取出一個節點
        for (const nei of adj[n]) {


            // 如果鄰居節點未染色
            // 如果還沒染色，將鄰居節點染成另一個顏色
            // 如果跟現在顏色一樣，表示無法顏色一樣，圖不是二分圖

            if (colors[nei] === colors[n]) return false


            if (colors[nei] === 0) {
                colors[nei] = -colors[n] // 將鄰居節點染成與當前節點不同的顏色
                q.push(nei); // 將鄰居節點加入队列
            }

        }
    }

    return true; // 廣度優先搜索完成，沒有衝突，表示圖是二分圖
}

```



dfs 解
```ts fold

function isBipartite(graph: number[][]): boolean {
  const n = graph.length;
  const colors: number[] = new Array(n).fill(0); // 0: 未着色, 1: 红色, -1: 蓝色

  function dfs(node: number, color: number): boolean {
    colors[node] = color;

    for (const neighbor of graph[node]) {
      if (colors[neighbor] === color) {
        return false; // 邻接节点颜色相同，冲突
      }
      if (colors[neighbor] === 0 && !dfs(neighbor, -color)) {
        return false; // 邻接节点未着色，递归着色失败
      }
    }

    return true; // 当前节点着色成功
  }

  for (let i = 0; i < n; i++) {
    if (colors[i] === 0 && !dfs(i, 1)) {
      return false; // 从未着色的节点开始DFS，如果着色失败，则不是二分图
    }
  }

  return true; // 所有节点都着色成功，是二分图
}


```

---

### [Possible Bipartition](https://leetcode.com/problems/possible-bipartition/description/)
Q: 
想要把 n 個人 (from 1 到 n) 分成兩個 size
有人不喜歡某人，因此不喜歡同一組
給 n, 表示 n 個人
給 dislikes array, `dislike[i] = [ai, bi]` 表示 a 這個人不喜歡 b這個人


same ver, just js 
```js fold

var possibleBipartition = function (n, dislikes) {
    let adj = createUndirectedGraph(n, dislikes)
    let color = Array.from({ length: n + 1 }, () => 0)

    for (let i = 1; i <= n; i++) {
        if(color[i]===0) {
            if (bfs(i, color, adj) === false) {
                return false
            }
        }
    }
    return true
};

function bfs(i, color, adj) {
    let q = [i]
    color[i] = 1
    
    while (q.length !== 0) {
        let n = q.shift()
       
        for (let nei of adj[n]) {
            if (color[nei] === color[n]) return false
            if (color[nei] === 0) {
                color[nei] = -color[n]
                q.push(nei)
            }
        }
    }
    return true
}

function createUndirectedGraph(n, dislikes) {
    let adj = Array.from({ length: n + 1 }, () => [])
    for (let [u, v] of dislikes) {
        adj[u].push(v)
        adj[v].push(u)
    }
    return adj
}

```


```ts fold



function possibleBipartition(n: number, dislikes: number[][]): boolean {
  const adj: number[][] = undirectedGraph(n, dislikes);
  const color: number[] = Array.from({ length: n + 1 }, () => 0); // 0: 未染色, 1: 紅色, -1: 藍色

  // 遍歷所有節點, 因為可能會有多個 component, 因此外層要這樣遍歷
  for (let i = 1; i <= n; i++) {
    // 如果節點未染色，則進行 BFS 染色
    if (color[i] === 0) {
      if (!bfs(i, adj, color)) {
        // 如果 BFS 染色失敗，則表示無法二分
        return false;
      }
    }
  }

  return true; // 所有節點都已染色，且沒有衝突，表示可以二分
}

function undirectedGraph(n: number, dislikes: number[][]) {
  // 這一題 n is 1-based, so we need to use n+1 array to hold all nodes
  const adj = Array.from({ length: n + 1 }, () => []);
  for (const [a, b] of dislikes) {
    adj[a].push(b);
    adj[b].push(a);
  }
  return adj;
}

// 廣度優先搜索 (BFS) 染色
function bfs(startNode: number, adj: number[][], color: number[]): boolean {

  // for each startNode，用一個 BFS 處理, q 放裡面即可
  const q: number[] = [startNode]; // 初始化队列
  color[startNode] = 1; // 將起始節點染成紅色

  while (q.length !== 0) {
    const n = q.shift(); // 取出队列中的節點

    // 遍歷鄰居節點
    for (const nei of adj[n]) {

      // 如果鄰居顏色和自己一樣，表示無法二分
      if (color[nei] === color[n]) return false;

      // 如果鄰居節點未染色
      if (color[nei] === 0) {
        color[nei] = -color[n]; // 染成與當前節點相反的顏色
        q.push(nei); // 加入队列
      }
    }
  }

  return true; // BFS 染色成功
}



```



錯誤記錄
```ts fold

function bfs(i, color, adj) {
    let q = [i]
    // color[i] = 1  // <-- 這樣才對, 因為我們要在 q 開始跑起來前， init 一個 node 的顏色
     
    while (q.length !== 0) {
        let n = q.shift()
        color[n] = 1  // <-- 這樣會錯，因為你每一次 iteration, 你都把 color n 改為1 (你改掉了下面推進去的)
       
        for (let nei of adj[n]) {
            if (color[nei] === color[n]) return false
            if (color[nei] === 0) {
                color[nei] = -color[n]
                q.push(nei)
            }
        }
    }
    return true
}

```


錯誤記錄
下面觀念錯了，下面的觀念是，只要有一個 true 就是 true
但是下面的 for-loop `for (let i = 1; i <= n; i++) {` 其實是針對 多亇connceted comonent下
去確認每一個 connected component 都要可以二分
因此，如果有一個是 false, 結果就應該是 false


```js


var possibleBipartition = function (n, dislikes) {
    let adj = setAdj(n, dislikes)
    let color = Array.from({ length: n +1  }, () => 0)

    for (let i = 1; i <= n; i++) {
        if(color[i]===0){
            if(bfs(i, color, adj)===true) return true
        }
    }
    return false
};


```