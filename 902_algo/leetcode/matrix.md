
### [Set Matrix Zeroes - LeetCode](https://leetcode.com/problems/set-matrix-zeroes/description/)
一個matrix 裡面有 0
把 0 cell 的 row and col 都變成 0
只能 in-place 修改
![[IMG-matrix-20250810200102718.png]]

```ts fold


/**
 Do not return anything, modify matrix in-place instead.
 */
function setZeroes(matrix: number[][]): void {
  let rows = matrix.length;
  let cols = matrix[0].length;

  // 用這兩個來收集是 0 的 row 和 co
  let rowContainZero = [];
  let colContainZero = [];
  for (let r = 0; r < rows; r++) {
    for (let c = 0; c < cols; c++) {
      if (matrix[r][c] === 0) {
        rowContainZero.push(r);
        colContainZero.push(c);
      }
    }
  }

  // console.log(rows, cols)

  for (let r = 0; r < rows; r++) {
    for (let c = 0; c < cols; c++) {
      if (rowContainZero.includes(r) || colContainZero.includes(c)) {
        matrix[r][c] = 0;
      }
    }
  }
}


```


### [Rotate Image - LeetCode](https://leetcode.com/problems/rotate-image/description/)
把 matrix 順時鐘轉 90度
只能 in-place
![[IMG-matrix-20250810200106963.png|681]]





1. 對角線對調 and then reverse at rows
2. how to 對角線對調 ?
    1. r !== c && r > c 下進行 swap
```ts fold

/**
 Do not return anything, modify matrix in-place instead.
 */
function rotate(matrix: number[][]): void {
    let rows = matrix.length
    let cols = matrix[0].length

    for (let r = 0; r < rows; r++) {
        for (let c = 0; c < cols; c++) {
            if (r !== c && r > c) { 
                let tmp = matrix[r][c]
                matrix[r][c] = matrix[c][r]
                matrix[c][r] = tmp
            }
        }
    }

    for (let r = 0; r < rows; r++) {
        matrix[r].reverse()
    }

};




```


### [Spiral Matrix - LeetCode](https://leetcode.com/problems/spiral-matrix/description/)
給一個 matrix, return 一個 arr, 用下面的方式去走

![[IMG-matrix-20250810200110734.png]]


使用跟 spiral matrix II 類似的解法, 比較好懂
```js fold

var spiralOrder = function (grid) {

    let rows = grid.length
    let cols = grid[0].length

    let res = []

    let topRow = 0
    let bottomRow = rows - 1
    let leftCol = 0
    let rightCol = cols - 1

    while (true) {
        for (let i = leftCol; i <= rightCol; i++) {
            res.push(grid[topRow][i])
        }
        topRow++
        if(res.length === rows * cols) break
  

        for (let i = topRow; i <= bottomRow; i++) {
            res.push(grid[i][rightCol])
        }
        rightCol--
        if(res.length === rows * cols) break

        for (let i = rightCol; i >= leftCol; i--) {
            res.push(grid[bottomRow][i])
            
        }
        bottomRow--
        if(res.length === rows * cols) break



        for (let i = bottomRow; i >= topRow; i--) {
            res.push(grid[i][leftCol])
            
        }
        leftCol++
        if(res.length === rows * cols) break
    }

    return res
};



```




```ts fold 


function spiralOrder(grid) {

    let res = []

    let rows = grid.length
    let cols = grid[0].length
    let curR = 0
    let curC = 0

    function goRight(r, c) {
        if (c >= cols) return
        if (grid[r][c] === 'marked') return

        // visit
        res.push(grid[r][c])
        grid[r][c] = 'marked'
        curR = r
        curC = c
        goRight(r, c + 1)
    }

    function goDown(r, c) {
        if (r >= rows) return
        if (grid[r][c] === 'marked') return

        // visit
        res.push(grid[r][c])
        grid[r][c] = 'marked'
        curR = r
        curC = c
        goDown(r + 1, c)
    }


    function goLeft(r, c) {
        if (c < 0) return
        if (grid[r][c] === 'marked') return

        // visit
        res.push(grid[r][c])
        grid[r][c] = 'marked'
        curR = r
        curC = c
        goLeft(r, c - 1)
    }

    function goUp(r, c) {
        if (r < 0) return
        if (grid[r][c] === 'marked') return

        // visit
        res.push(grid[r][c])
        grid[r][c] = 'marked'
        curR = r
        curC = c
        goUp(r - 1, c)
    }


    while (res.length < rows * cols) {
        goRight(curR, curC)
        goDown(curR + 1, curC) // 需要自己幫他走一步，不然 curR, curC 已經 marked
        goLeft(curR, curC - 1)
        goUp(curR - 1, curC) 

        // 多走一次
        goRight(curR, curC + 1)  // 需要多走一個 goRight 來幫忙多走一步

        // 上面重複走沒差，因為有 marked

    }

    return res

};

```


---

## [Spiral Matrix II](https://leetcode.com/problems/spiral-matrix-ii/description/)

```js fold

function generateMatrix(n) {
    // setup 2d matrix, n*n -> all 9
    const matrix = Array.from({ length: n }, () => Array(n).fill(0))

    let num = 1 // 當前要填入的數字

    // 四個邊界
    let topRow = 0
    let bottomRow = n - 1
    let leftCol = 0
    let rightCol = n - 1

    while (num <= n * n) {
        // 從左到右
        for (let i = leftCol; i <= rightCol; i++) {
            // top 不會動，都是 0
            // i is changing, i is col, 1,2,3
            matrix[topRow][i] = num++
        }
        topRow++ // 這時候 topRow++ , 0->1   多一個往下


        // 從上到下
        for (let i = topRow; i <= bottomRow; i++) {
            // right 不會動, 是 n-1
            // i ++, row
            matrix[i][rightCol] = num++
        }
        rightCol-- // rightCol n-1 -> n-2, 多一個往左


        // 從右到左
        for (let i = rightCol; i >= leftCol; i--) {
            // bottom n-1 不動, row
            // i 會 -- (右到左)
            matrix[bottomRow][i] = num++
        }
        bottomRow-- // 多一個往上


        // 從下到上, leftCol is 0
        for (let i = bottomRow; i >= topRow; i--) {
            // left 固定都是 0, col 0
            // i 往上走，減少
            matrix[i][leftCol] = num++
        }
        // 多一個往右 col 0->1
        leftCol++


        // 最後持續重複
    }

    return matrix
}


```

這個可以畫出來，就解掉了
![[IMG-matrix-20250810200112974.png]]