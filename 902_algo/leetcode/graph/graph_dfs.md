


### [178 · Graph Valid Tree](https://www.lintcode.com/problem/178/)
Q:
given n，表示 0 ~ n-1 個 node
和提供edge list, 是無向邊
edge 代表 pair nodes
確認這些 edges是否構成一個valid tree

```js fold

export class Solution {
  /**
   * @param n: An integer
   * @param edges: a list of undirected edges
   * @return: true if it's a valid tree, or false
   */
  validTree(n: number, edges: number[][]): boolean {
    const adj: number[][] = this.createUndirectedGraph(n, edges);



    const visited: Set<number> = new Set();

    // use dfs go thru adj for cycle detection
    function dfs(node: number, parent: number): boolean {
      // 已经访问过，存在环 -> return
      if (visited.has(node)) return false;
      visited.add(node);

      // use adj to traversal graph
      for (const neighbor of adj[node]) {
        // 如果不往回走，且 dfs return false -> cycle 
        if (neighbor !== parent && !dfs(neighbor, node)) return false
          
      }

      return true; // 当前节点没有环
    }
		
	// tree的條件1: no cycle
    // 从节点 0 开始 DFS
    if (!dfs(0, -1)) return false; // 存在环

	// tree的條件2: check all connected together
    // 如果全部都有 visit 過，則说明是树
    return visited.size === n;
  }

  
  private createUndirectedGraph(n: number, edges: number[][]) {
    // 依照題目是 0-indexed, 所以用 Array.from 得到 n 長度的 array
    // no dist or related stuff to track, so number[][] is enough
    const adj: number[][] = Array.from({ length: n }, () => []);

    // use edges to setup undirected adj
    for (const [u, v] of edges) {
      // 兩邊都要加
      adj[u].push(v);
      adj[v].push(u);
    }
    return adj;
  }
}

```

TIL
- valid tree的定義
	1. 沒有 loop
	2. 全部必須連在一起
- 找到一個 init arr 但是不需要 n+1 的例子 since 這題是 0-indexed
- 如何建立 undirectedGraph 的 adj list using `number[][]` data structur
- 知道 how to use dfs to traversal adj and check cycle


卡住記錄：
- 無相圖，因此要找 cycle 的 dfs 需要 track 上一個, 不然一定 cycle


### [3651 · Number of Connected Components in an Undirected Graph](https://www.lintcode.com/problem/3651/)
Q:
n 個 node 的 undirected graph
給定 edges array, `edges[i] = [a, b]` 表示 node `a` and node `b` 的 的 edge 
求這個圖有幾個 connected component

```ts fold

export class Solution {
  countComponents(n: number, edges: number[][]): number {
    const adj: number[][] = this.undirectedGraph(n, edges); // 建立一個鄰接表來表示圖
    const visited: boolean[] = Array.from({ length: n }, () => false); // 初始化 visited 陣列，用於標記已訪問的節點

    let res = 0;

    // 遍歷所有節點
    for (let i = 0; i < n; i++) {
      // 因為有可能存在 connected component, 所以每一個 node as start point 都要試試看
      if (!visited[i]) {
        this.dfsHelper(i, adj, visited);
        res++; // 每完成一次深度優先搜索，connected component 數量加 1
      }
    }

    return res; 
  }

  private undirectedGraph(n: number, edges: number[][]) {
    // 從題目可以看到 n 是 0-indexed 的，所以 length 為 n
    const adj: number[][] = Array.from({ length: n }, () => []);
    for (const [u, v] of edges) {
      adj[u].push(v);
      adj[v].push(u); // 無向圖，需要雙向添加邊
    }
    return adj;
  }

  // 深度優先搜索
  private dfsHelper(node: number, adj: number[][], visited: boolean[]): void {
    visited[node] = true; // 標記當前節點為已訪問
    // 遞迴訪問所有鄰居節點
    for (const nei of adj[node]) {
      if (!visited[nei]) { // 如果還沒訪問過
        // 每走過就繼續走
        this.dfsHelper(nei, adj, visited);
      }
    }
  }
}

```
TIL:
- dfs 走 adj graph, 確認 connected component 數量