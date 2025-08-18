---  
alias:  
creation_date: 2024-10-09 20:47  
related:
- 

---  

### [200. Number of Islands](https://leetcode.com/problems/number-of-islands/)
給定一個 `m x n` 二維二進位網格 `grid` 
它表示 `'1'` s（土地）和 `'0'` s（水）的地圖
返回島嶼數量。
島嶼四面環水，相鄰陸地水平或垂直連接而成。您可以假設網格的所有四個邊緣都被水包圍。

```js fold

function numIslands(grid: string[][]): number {
    let rows = grid.length
    let cols = grid[0].length

    function dfs(r, c) {
        if (r < 0 || c < 0 || r >= rows || c >= cols) return false
        if (grid[r][c] !== '1') return false

        // visit
        grid[r][c] = 'marked'




        /**

        why 不是這樣操作?
            let path = dfs(r + 1, c) &&
            dfs(r - 1, c) && or  ||
            dfs(r, c + 1) &&
            dfs(r, c - 1)
		
		這樣走，請問你要在哪裡 return true or false?
		上面那樣走，常見的用在 DP 題目，你在 base case 定義好 return true or false
		然後你用上面的 && or false 決定這一次的決策是 true or false



		這一題的我們要做的是，我們要往四個方向移動，或決策圖就是4 factor branching
		因此最後需要有 4 個 function call like below
		因此只要四個 fn call 一直遞迴呼叫，有些會撞到上面的return false
		沒有的就是一直走在'1'上，然後最後就會 return 一個 true -> 這個就算記入一個島
    
        
         */
        dfs(r + 1, c)
        dfs(r - 1, c)
        dfs(r, c + 1)
        dfs(r, c - 1)

        return true
    }




    let count = 0
    for (let r = 0; r < rows; r++) {
        for (let c = 0; c < cols; c++) {
            if (dfs(r, c) === true) {
                count++
            }
        }
    }
    return count
};



```

### [695. Max Area of Island](https://leetcode.com/problems/max-area-of-island/)
找最大面積的那個島，返回面積大小
```js fold

function maxAreaOfIsland(grid: number[][]): number {
  let rows = grid.length;
  let cols = grid[0].length;

  function dfs(r, c) {
    if (r < 0 || c < 0 || r >= rows || c >= cols) return 0;
    if (grid[r][c] !== 1) return 0;

    // visit
    grid[r][c] = 2;

    // 錯誤 code
    // 你這樣做，下面要怎麼接?
    // let dir1 = dfs(r + 1, c, pathsum+1)
    // let dir2 = dfs(r - 1, c, pathsum+1)
    // let dir3 = dfs(r, c + 1, pathsum+1)
    // let dir4 = dfs(r, c - 1, pathsum+1)

    // 錯誤code:
    //  你用 || or && return 出去都是 true or false, 這樣跟你想要return number 違背了
    // dfs(r + 1, c)+1 ||
    // dfs(r - 1, c)+1 ||
    // dfs(r, c + 1)+1 ||
    // dfs(r, c - 1)

    // 下面這行code代表了什麼?
    // 每一個 dfs function body 只會 return dfs 的 returned value + 1
    // 從每一個 dfs 的角度來看，這個 dfs 只會貢獻 value 1
    // 但是下面有四個 dfs, 每一個可以帶上很多層的 value, 因此最終的 dfs 可能會貢獻很多 value, > 1
    // 然後如果都沒有撞到上面的 return 0, 就會一直進入四個方向的 dfs 呼叫, 一直到最後撞到 return 0
    // 會一層一層的 return, 每一個 return 會 + 1, 最後給出一直走到1的那些 cell 的全部的 1
    // 如果有走到 5 個 1 cell, 最後return 就會一直return, like 1+1+1+1+1+1 = 5
    // 總結：
    // +1 是因為一個 dfs 呼叫就是走一個 cell, 所以 +1
	// 全部加起來是因為，我要把之前每一個遞迴的 dfs 呼叫的裡面那些 + 1 都要計算進去
    let res = dfs(r + 1, c) + dfs(r - 1, c) + dfs(r, c + 1) + dfs(r, c - 1) + 1;

    return res;
  }


  let res = 0;
  for (let r = 0; r < rows; r++) {
    for (let c = 0; c < cols; c++) {
      res = Math.max(dfs(r, c), res);
    }
  }
  return res;
}

```

### [417. Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)
返回一個update的 list, this list contain many (r,c), all of which can flow from cell to Both Ocean
有一個 `m x n` 矩形島嶼，與太平洋和大西洋接壤。太平洋接觸島嶼的左側和頂部邊緣，大西洋接觸島嶼的右側和底部邊緣。
該島被劃分為方形網格。
給定一個 `m x n` 整數矩陣 `heights` ，其中 `heights[r][c]` 表示座標 `(r, c)` 處單元格的海拔高度。
該島降雨較多，如果相鄰單元格的高度小於或等於目前單元的高度，
雨水可以直接流向北、南、東、西的相鄰單元格。水可以從與海洋相鄰的任何細胞流入海洋。
傳回網格座標 `result` 的二維列表，其中 `result[i] = [ri, ci]` 表示雨水可以從單元 `(ri, ci)` 流向太平洋和大西洋。

```js fold
function pacificAtlantic(heights: number[][]): number[][] {

    let rows = heights.length
    let cols = heights[0].length

    let pacSet = new Set()
    let altSet = new Set()

    function dfs(r: number, c: number, set1: Set<unknown>, prev: number) {
        let key = r + "-" + c

        if (r < 0 || c < 0 || r >= rows || c >= cols) return false
        if (set1.has(key)) return false
        if(prev > heights[r][c]) return false
      
        set1.add(key)

        dfs(r+1, c, set1, heights[r][c])
        dfs(r-1, c, set1, heights[r][c])
        dfs(r, c+1, set1, heights[r][c])
        dfs(r, c-1, set1, heights[r][c])

        return true
    }

    for (let r = 0; r < rows; r++) {
        dfs(r, 0, pacSet, heights[r][0])
        dfs(r, cols-1, altSet, heights[r][cols-1])
    }

    for (let c = 0; c < cols; c++) {
        dfs(0, c, pacSet, heights[0][c])
        dfs(rows-1, c, altSet, heights[rows-1][c])
    }

    let res = []
    for (let r = 0; r < rows; r++) {
        for (let c = 0; c < cols; c++) {
            let key = r + "-" + c
            if (pacSet.has(key) && altSet.has(key)) res.push([r, c])
        }
    }
    return res

};

```

### [130. Surrounded Regions](https://leetcode.com/problems/surrounded-regions/)
找到那些被X包住的O，把那些變成X

因為邊邊的O一定不會被X包住，要另外處理
最後返回update的Board
Given an `m x n` matrix `board` containing `'X'` and `'O'`, *capture all regions that are 4-directionally surrounded by* `'X'`.
A region is **captured** by flipping all `'O'`s into `'X'`s in that surrounded region.

![[IMG-grid_dfs-20250810200102752.png]]

```js fold

/**
 Do not return anything, modify board in-place instead.
 */
 function solve(grid: string[][]): void {
    /**
    1. 第一次 pass 是 島嶼問題，需要用 dfs mark 邊上包圍的"的cells to "t"
    2. 把全部的 O 變成 X
    3. 把全部的 T 變成 O
     */

    let rows = grid.length
    let cols = grid[0].length

    let s1 = new Set()
    function dfs(r: number, c: number) {
        let key = r + "," + c
        if (r < 0 || c < 0 || r >= rows || c >= cols) return false
        if(grid[r][c]!=="O") return false
        if(s1.has(key)) return false
        
        s1.add(key)
        grid[r][c] = "t"

        dfs(r + 1, c)
        dfs(r - 1, c)
        dfs(r, c + 1)
        dfs(r, c - 1)
        return true
    }

    for (let r = 0; r < rows; r++) {
        for (let c = 0; c < cols; c++) {
            if (r === 0 || c === 0 || r === rows - 1 || c === cols - 1) {
                dfs(r, c)
                // 事情沒有單純到下面這兩行XDD
                // 第一次 pass 是 島嶼問題，需要用 dfs mark 邊上包圍的"的cells to "t"

                // if (grid[r][c] === "O") {
                //     grid[r][c] = "q"
                // }
            }


        }
    }

    // console.log(`grid1`, grid)

    for (let r = 0; r < rows; r++) {
        for (let c = 0; c < cols; c++) {
            if (grid[r][c] === "O") {
                grid[r][c] = "X"
            }
        }
    }

    // console.log(`grid2`, grid)


    for (let r = 0; r < rows; r++) {
        for (let c = 0; c < cols; c++) {
            // 不需下面這行, why?
            // 因為你第一個 dfs mark的 t 的區域會超出邊邊的範圍
            // if (r === 0 || c === 0 || r === rows - 1 || c === cols - 1) {
                if (grid[r][c] === "t") {
                    grid[r][c] = "O"
                }
            // }

        }
    }

    // console.log(`grid3`, grid)
};

```


### [79. Word Search](https://leetcode.com/problems/word-search/)
```js fold


function exist(grid, word) {
  let rows = grid.length;
  let cols = grid[0].length;
  /**
   * use let set1 = new Set()?
   * 簡單的 global set is not working here,
   * 因為可以從任一cell出發, 然後只要 同一個 dfs path 不走到重複的，
   * 但不同地方出發的 dfs path 是可以走到之前不同 dfs path 走過的 cell
   */

  function dfs(r, c, word) {
//    let key = r + "," + c;
    if (word === "") return true; // <--- 錯誤點：忘記定義這個word找完的成功base case

    if (r < 0 || c < 0 || r >= rows || c >= cols) return false; // <-- out of bound
    if (grid[r][c] !== word[0]) return false; // <-- not match

    // 存起來
    let tmp = grid[r][c];

    // 同一個 path 走過的路就不走了, mark #
    // 這樣，上面 `grid[r][c] !== word[0]` 就會檔掉
    grid[r][c] = "#";


    // OR, 因為只要有一個方向可以讓我最後湊滿，就算是找到
    let curRes =
      dfs(r + 1, c, word.slice(1)) ||
      dfs(r - 1, c, word.slice(1)) ||
      dfs(r, c + 1, word.slice(1)) ||
      dfs(r, c - 1, word.slice(1));

    // dfs ret 後 用上面存的 tmp overwrite `#`, 補回原本的值
    //  因為其他的 dfs 還要走
    grid[r][c] = tmp;

    return curRes;
  }

  // 可以從每一個地方出發
  for (let r = 0; r < rows; r++) {
    for (let c = 0; c < cols; c++) {
      // 只要有其中一個 path 可以從頭到尾都 ret true 就是 true
      if (dfs(r, c, word) === true) {
        return true;
      }
    }
  }
  return false;
}
```



