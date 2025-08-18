---  
alias:  
creation_date: 2024-10-09 20:39  
tags: 
related:
- 

---  


### [994. Rotting Oranges](https://leetcode.com/problems/rotting-oranges/)
0 是空call,  1是好橘子 2是爛橘子
每一分鐘，爛橘子四周的cell都會變成爛橘子
求最少需要幾分鐘，整個grid沒有好橘子
如果做不到，都會有好橘子的話，返回 -1
```js fold

var orangesRotting = function (grid) {
  let rows = grid.length
  let cols = grid[0].length
  let fresh = 0
  let min = 0
  let q = []

  // 先把爛橘子放到 queue 中, this is multi-source BFS problem
  // 先看有多少好橘子
  for (let r = 0; r < rows; r++) {
    for (let c = 0; c < cols; c++) {
      if (grid[r][c] === 2) q.push([r, c]) // q 裡面放的是 grid 的 r, c 的 index
      if (grid[r][c] === 1) fresh += 1
    }
  }

  // 還要額外多一個 fresh > 0 的條件, why? 就算還有 rotten in q, 但是 fresh is 0, 就不需要動了
  while (q.length > 0 && fresh > 0) {
    qLen = q.length
    for (let i = 0; i < qLen; i++) { // 有一次我還忘記加上這個loop, 這樣等於你一次只會處理一個爛橘子
      let [r, c] = q.shift()  
      let directions = [
        [1, 0],
        [-1, 0],
        [0, 1],
        [0, -1]
      ]
      // 有四個方向可以走，我們用 directions 幫忙 traversal
      for (const [dr, dc] of directions) {
        let r1 = dr + r
        let c1 = dc + c
        if (r1 < 0 || c1 < 0 || r1 >= rows || c1 >= cols) continue
        if (grid[r1][c1] !== 1) continue // 只往好橘子走

        grid[r1][c1] = 2  // 好橘子變成爛橘子
        fresh -= 1        // 好橘子減一
        q.push([r1, c1])
      }
    }
    min += 1 // 每一輪 q shift 都是一輪，減少一分鐘
  }

  if (fresh == 0) return min
  else {
    return -1
  }
}


```

### [663 · Walls and Gates](https://www.lintcode.com/problem/663/)
You are given a `m x n` 2D grid initialized with these three possible values.
- `-1` - A wall or an obstacle.
- `0` - A gate.
- `INF` - Infinity means an empty room
Fill each empty room with the distance to its nearest gate. If it is impossible to reach a `Gate`, that room should remain filled with `INF`

`M*N` 2d grid
-1 是牆
0 是門
和 `inf`

算法目標：
update `INF`的值，讓這個 cell 到門是最短距離
如果走不到門，那就維持原本的`INF`

PS: JS 不會 TLE, TS 會 XD
```js fold

export class Solution {
  /**
   * @param rooms: m x n 2D grid
   * @return: nothing
   */
  wallsAndGates(grid) {
    let rows = grid.length
    let cols = grid[0].length
    let q = []

    // multi source bfs, put source into queue
    // 這個例子，就是門
    for (let r = 0; r < rows; r++) {
      for (let c = 0; c < cols; c++) {
        if (grid[r][c] === 0) {
          q.push([r, c])
        }
      }
    }

    let dist = 1
    while (q.length > 0) {
      let qLen = q.length
      for (let i = 0; i < qLen; i++) {
        let [r, c] = q.shift()
        let directions = [
          [1, 0],
          [-1, 0],
          [0, 1],
          [0, -1]
        ]
        for (let [dr, dc] of directions) {
          let r1 = r + dr
          let c1 = c + dc
          //錯誤點：這裡有順序性喔，得先檢查是否 out of bound! 不然下行 grid[r1][c1] will access error
          if (r1 < 0 || c1 < 0 || r1 >= rows || c1 > cols) continue
          if (grid[r1][c1] !== 2147483647) continue // 只往 INF 走
          grid[r1][c1] = dist // update dist
          q.push([r1, c1]) // 推入 q, 下一層可以跑
        }
      }
      dist += 1
    }
  }
}


```

### [Shortest Path in Binary Matrix - LeetCode](https://leetcode.com/problems/shortest-path-in-binary-matrix/description/)

找最短的 “clear path”, 找不到 ret -1
what is “clear path”?

從 `n*n` matrix 的 0,0 → n-1, n-1 → 都需要是 0
可以走對角線


short ver
```js fold

var shortestPathBinaryMatrix = function (board) {
    let rows = board.length
    let cols = board[0].length

    if (board[0][0] !== 0 ||
        board[rows - 1][cols - 1] !== 0) return -1

    let dir = [
        [1, 0],
        [-1, 0],
        [0, 1],
        [0, -1],
        [1, 1],
        [-1, -1],
        [1, -1],
        [-1, 1],
    ]

    let q = [[0, 0]]
    

    let dist= 1

    while (q.length !== 0) {

        let qLen = q.length
        for (let i = 0; i < qLen; i++) {
            let [r, c] = q.shift()

            if (r === rows - 1 && c === cols - 1) return dist

            for (let [dr, dc] of dir) {
                let r1 = r + dr
                let c1 = c + dc

                if (r1 < 0 || c1 < 0 || r1 >= rows || c1 >= cols) continue
                if (board[r1][c1] !== 0) continue

                board[r1][c1] = 2
                q.push([r1, c1])
            }

        }
        dist++
    }

    return -1
};


```


```js fold

// 同時往很多方向走，找出某一個最快達到目標的就是最短距離


function shortestPathBinaryMatrix(grid) {
  if (grid[0][0] === 1) return -1; // 起點不可通行

  const rows = grid.length;
  
  const queue = [[0, 0]]; // 初始化队列，加入起點
  grid[0][0] = 2; // 標記起點已訪問
  let dist = 1; // 初始化距離
  
  const dirs = [ [0, 1], [1, 0], [0, -1], [-1, 0], [1, 1], [1, -1], [-1, -1], [-1, 1], ]; // 八個方向

  while (queue.length > 0) {
    const qLen = queue.length; 
    for (let i = 0; i < qLen; i++) {
      const [r, c] = queue.shift(); // 取出队首节点
      if (r === rows - 1 && c === rows - 1) return dist; // 到达终点，返回距离

      // 遍历八个方向
      for (const [dr, dc] of dirs) {
        const r1 = r + dr;
        const c1 = c + dc;

        if ( r1 < 0 || c1 < 0 || r1 === rows || c1 === rows ) continue   // 检查边界条件和是否可通行
        if ( grid[r1][c1] !== 0 ) continue;  // only visit 0
    

        grid[r1][c1] = 2; // mark visited
        queue.push([r1, c1]); // 加入队列
      }
    }
    dist++; // 进入下一层级，距离加 1
  }

  return -1; // 无法到达终点
}



```


因為這題要求最短
因此可以用 BFS, 用 DFS 不好處理最短
每走一步都是一個距離，沒有自定義 dist -> 因此也不需要用類似 dijsktra 的算法