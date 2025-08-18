



## 可重複選 Unbounded Knapsack

### [518. Coin Change 2](https://leetcode.com/problems/coin-change-2/)
Q: 湊硬幣有多少組合, 可重複選

```ts fold
var change = function(amount, coins) {
    let memo = {}
    
    function dfs(i, amount){
        
        let key = i + ',' + amount
        if (key in memo) return memo[key]
        
        // amount 這個 dim 要處理的兩個 base case
        if (amount === 0) return 1  // 剛好扣到 0, 算一種
        if (amount < 0) return 0    // overshot, 不算
         
        // i 這個 dim 需要處理，如果超過 range 還沒撞到上面的 amount === 0, 也不算是一種
        if (i >= coins.length) return 0
        
        // why i 可以不動? -> 這條路徑就是一直選第一個, 但會扣掉
        let useCoinWay = dfs(i, amount - coins[i]) 
        // 這條路徑就是會往下走，但是不扣
        let noUseCoinWay = dfs(i+1, amount)
        
        // 全部共有幾種，要加起來
        memo[key] = useCoinWay + noUseCoinWay
        return memo[key]
    }
    
    return dfs(0, amount)
    
};
```
### [322. Coin Change](https://leetcode.com/problems/coin-change/)
Q: 加到 target 需要的最小 coin 數量, 可重複選
```ts fold

function coinChange(coins: number[], amount: number): number {

    let mem = {}
    function dfs(i, amt) {
        let key = i + ',' + amt
        if (key in mem) return mem[key]

        // ret 0 是因為我們已經在每一次 dfs called 後會 +1, 因此這邊不能 return 1
        // 通常 ret 1 也是用在 count-how-many-ways 的問題上
        if (amt === 0) return 0



        
        // 錯誤記錄：想了很久就是沒有想到這一行需要加上去!!!!
        // 錯誤上已經暗示你，就是無窮loop了 -> 表示你有 base case 漏掉
        // 這個不加上，為何會無窮loop? 因為你有可能一直跑 dfs(i + 1, amt) -> 然後 amt 不會撞到 0 or <0
        // 這樣你就無窮迴圈了
        if (i>=coins.length) return Infinity // go over 但也還沒撞到 amt === 0
        
             
        if(amt<0) return Infinity // overshot


        // why this is not a valid condition for good base case?
        // 因為可以重複選，因此也可以一直選第一個, 那也就沒有需要判斷走到是否最後的條件了吧
        // if(amt===0 && i===coins.length-1)

        let pickOneCoinCount = 1 + dfs(i, amt - coins[i])
        let notPickOneCoinCount = dfs(i + 1, amt)
        mem[key] = Math.min(pickOneCoinCount, notPickOneCoinCount)
        return mem[key]

    }

    let res = dfs(0, amount)
    if (res === Infinity) return -1
    else return res

};
```
### [unbounded-knapsack](https://www.hackerrank.com/challenges/unbounded-knapsack/problem)
```ts fold
function unboundedKnapsack(k, arr) {
  let mem = {};
  let res2 = -Infinity;
  function dfs(i, sum) {
    let key = i + "," + sum;
    if (key in mem) return mem[key];

    if (sum === k) {
      return sum;
    }
    if (sum > k) return -Infinity;
    if (sum < k) {
      res2 = Math.max(res2, sum);
    }

    if (sum < k && arr.length === i) return -Infinity;

    let choose = dfs(i, sum + arr[i]);
    let notChoose = dfs(i + 1, sum);

    mem[key] = Math.max(choose, notChoose);
    return mem[key];
  }

  const res = dfs(0, 0);
  if (res === -Infinity) {
    return res2;
  } else {
    return res;
  }
}
```


## 不可重複選 0/1 Knapsack
### [494. Target Sum](https://leetcode.com/problems/target-sum/)
Q: for each number, we can add + or -
return **the number of all different expressions** which **evaluated to target**

```ts fold
function findTargetSumWays(nums: number[], target: number): number {
    /**
    只能選一次
    共有幾種的問題, ret 共有幾種

    選+ -> target +
    選 - -> target -
     */

    let mem = {}
    function dfs(i, sum) {
        let key = i + ',' + sum
        if (key in mem) return mem[key]

        if (i === nums.length && sum === target) return 1
        if (i === nums.length && sum !== target) return 0


        let usePlusCount = dfs(i + 1, sum + nums[i])
        let useMinusCount = dfs(i + 1, sum - nums[i])
        mem[key] = usePlusCount + useMinusCount
        return mem[key]
    }
    return dfs(0, 0)
};
```
### [416. Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)
Q: 確認陣列是否可以被分成兩個subset，且這兩個subset的和一樣。
```ts fold


/**
這題 leetocde 效能改的很嚴格 (如果用 DP 解可能就很簡單了)
遞迴的話，需要進行一些優化手段
 */

function canPartition(nums: number[]): boolean {
    // 如果 nums 的加總除以2 無法整除，那也就不可能拆成兩個sum一樣的 subset了
    let sum = nums.reduce((pre, cur) => pre + cur)
    if (sum % 2 !== 0) return false

    let target = sum / 2

    /**
        nums = [1,5,11,5]
        求是否這個 nums 可以加總到 target
        不可重複選取
     */


    // 優化：對數組進行排序, 下面要才可以剪枝
    nums.sort((a, b) => a - b);


    // 優化：use map, 比 obj 快
    let mem = new Map()
    
    
    function dfs(i, sum) {
        // 優化：轉為 String 比 JS 自己轉 然後再concate 快
        let key = String(i) + String(sum)

        // 優化：提前返回：如果 sum 超過了 target，直接返回 false
        if (sum > target) return false;
        
        // 優化： 剪枝, 如果當前數字已經超過目標值，則可以停止遞歸, need to sort nums fist
        if (sum + nums[i] > target) return false;


        // 如果走完了 && 總和拿到目標數 -> good base case
        if (i === nums.length && sum === target) return true
        // 但是如果走完了，卻沒有一樣 ->  bad base case
        if (i === nums.length && sum !== target) return false


        // 依照題意, 要切兩邊，因此就算是不可以重複選, i+1
        let NoChooseOnePath = dfs(i + 1, sum)
        let chooseOnePath = dfs(i + 1, sum + nums[i])

        // 兩種其一可以, 只要可以順利走完
        let result = NoChooseOnePath || chooseOnePath; 
        mem.set(key, result);
        return result;
    }
    return dfs(0, 0)
};


```

### [2915.Length of the Longest Subsequence That Sums to Target](https://leetcode.com/problems/length-of-the-longest-subsequence-that-sums-to-target/description/)

目前此 code is not passed due to TLE -> seems need to use DP-table way
```ts fold
function lengthOfLongestSubsequence(nums: number[], target: number): number {

    nums.sort((a, b) => a - b)

    let mem = new Map()


    const res = dfs(0, 0)
    if (res === -Infinity) return -1
    else return res


    function dfs(i, sum) {
        let key = String(i) + String(sum)
        if (sum > target) return -Infinity
        if (i === nums.length && target !== sum) return -Infinity
        if (target === sum) return 0

        if (mem.has(key)) return mem.get(key)

        let choose = dfs(i + 1, sum + nums[i]) + 1
        let notChoose = dfs(i + 1, sum)
        mem.set(key, Math.max(choose, notChoose))
        return mem.get(key)
    }
};
```

## 路徑題

### [62. Unique Path](https://leetcode.com/problems/unique-paths/)
Q: 找出從最左上到右下的所有可能路徑數

```ts fold

function uniquePaths(m: number, n: number): number {

   let mem = {}

   // 題目算是給 rows and cols
   /**
    *     <----- n ----->
    *     ________________  
    *    |                |   ^
    *    |                |   | m
    *    |________________|   V
    */
   let rows = m
   let cols = n

   function dfs(r, c) {
       let key = r +','+ c
       if (key in mem) return mem[key]

       if (r === rows - 1 && c === cols - 1) return 1 // good base cas, 走到底，算一種走法
       
       // 有可能會 r 走到底，但是另一個還沒到，要處理一下, ret 0 
       if (r >= rows || c >= cols) return 0

       // move right
       let moveRightCount = dfs(r, c + 1)
       // move down
       let moveDownCount = dfs(r + 1, c)
       mem[key] = moveRightCount + moveDownCount
       return mem[key]
   }

   return dfs(0, 0) // 從 0, 0 開始 目標走到 rows - 1, cols - 1
};

```

### [64. Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)
Q: 找出最左上到最右下的最小加總路徑（走過的cell需要加上去）
```ts fold
function minPathSum(grid: number[][]): number {

    /**
        從左上走到右下
        每一個點都有一個數字，走過去會累積
        只能往右和往下走
        找出最小的 path_sum
     */

    let rows = grid.length
    let cols = grid[0].length

    let mem = {} // r,c -> sum
    function dfs(r, c) {
        let key = r + ',' + c
        if (key in mem) return mem[key]
        
        if (r === rows - 1 && c === cols - 1) return grid[r][c]
        if (r >= rows || c >= cols) return Infinity


        let goLeftCurSum = grid[r][c] + dfs(r, c + 1)
        let goDownCurSum = grid[r][c] + dfs(r + 1, c)
        mem[key] = Math.min(goLeftCurSum, goDownCurSum)
        return mem[key]
    }
    return dfs(0, 0)
};
```



why 這一題不可以用 set
下面 red line 是的走法是答案
但是會因為 blue line 走過就無法走了
![[IMG-DP 2D-20250810200102760.png|617]]


---

### [329 Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/description/)

Q:
給定`m*n` grid, 求 最長的遞增路徑的長度 in grid
可以走上下左右
![[IMG-DP 2D-20250810200106987.png|455]]


```js fold

var longestIncreasingPath = function (grid) {
    let rows = grid.length
    let cols = grid[0].length
    let mem = {}

    let s1 = new Set()
    function dfs(r, c, prevVal) {
        let key = r + ',' + c + ',' + prevVal  // <----------–但是上面你要正確的加上 prevVal
        if (key in mem) return mem[key] // <---------這個不需要放下面
        if (r < 0 || c < 0 || r >= rows || c >= cols) return 0
        if(s1.has(key)) return 0
        if (grid[r][c] <= prevVal) return 0


        // visit //////////

        s1.add(key)

        let a1 = dfs(r + 1, c, grid[r][c]) + 1
        let a2 = dfs(r - 1, c, grid[r][c]) + 1
        let a3 = dfs(r, c + 1, grid[r][c]) + 1
        let a4 = dfs(r, c - 1, grid[r][c]) + 1

        s1.delete(key)

        mem[key] = Math.max(a1, a2, a3, a4)

        return mem[key]

    }


    let res = -Infinity
    for (let r = 0; r < rows; r++) {
        for (let c = 0; c < cols; c++) {
            res = Math.max(res, dfs(r, c, -1))
        }
    }
    return res

};

```



忘了加上 + prevVal 差異在哪裡?
```js

// 其他 code skip

let s1 = new Set()
function dfs(r, c, prevVal) {
	let key = r + ',' + c + ',' + prevVal // <--- 如果只有寫 `let key = r + ',' + c`
	if (key in mem) return mem[key]
	if (r < 0 || c < 0 || r >= rows || c >= cols) return 0
	if(s1.has(key)) return 0
	if (grid[r][c] <= prevVal) return 0

	// visit //////////
// 其他 code skip

```


下面這兩個走法，都會被看成是同一個, 但是卻不同
![[IMG-DP 2D-20250810200111945.png]]


這題考什麼?
- why set 資料要回補 why?
	- 因為這個 dfs 會從每一個 cell 出來來去進行判斷，你不回補，下一次重新找不就很多都找過了?
- grid 題型，找最長的那個路徑，要每一個方向+ 1 then max(up, down, left, right), why?
	- 因為你會往4個方向走 -> 4 dfs calls, 每一個 call 都會 +1, 然後下面用 math.max 找最大
- 可以算是 grid dfs 題型的進階題，需要加上 DP 的 mem 記憶, why?
	- 不一定加上 DP 的 mem ，就是 memorization, 讓你加速
	- 一樣的路如果知道最後沒有機會拿到最大，或是會往外走，那以後就沒必要走了


錯誤記錄
- 下面這個寫法的意思是：把四種路徑都加起來，然後另外 + 1
- 這跟題目要求的不同。題目要求，找出最長的那個路徑。
```ts



let curLen =
	1 + dfs(r + 1, c, grid[r][c]) +
	dfs(r - 1, c, grid[r][c]) +
	dfs(r, c + 1, grid[r][c]) +
	dfs(r, c - 1, grid[r][c])


visit.delete(key)
mem[key] = curLen
return mem[key]


```




```ts fold


function longestIncreasingPath(grid) {
  let rows = grid.length;
  let cols = grid[0].length;

  let visit = new Set();

  let memo = {};

  let dfs = (r, c, preValue) => {
    let key = r + "," + c;
    
    if (r < 0 || c < 0 || r >= rows || c >= cols) return 0; // out of bound
    if (grid[r][c] <= preValue) return 0; // ensure increase path
    if (visit.has(key)) return 0; // prevent revisit

    // 上面 key 少加上 preValue, 但是 19行放這裡也可以過, why? 大概是上面都檔掉可能的問題了?
    if (key in memo) return memo[key];

    visit.add(key);

    let up = 1 + dfs(r + 1, c, grid[r][c]);
    let down = 1 + dfs(r - 1, c, grid[r][c]);
    let left = 1 + dfs(r, c + 1, grid[r][c]);
    let right = 1 + dfs(r, c - 1, grid[r][c]);

    // we only need to get the max line, not the possible set
    let curMaxLen = Math.max(up, down, left, right);

    //////////// 這邊要拿掉，因為你要讓其他 dfs 可以用同一個 visit set
    visit.delete(key);

    memo[key] = curMaxLen;

    return curMaxLen;
  };

  
  // check each cell as question required
  let res = -Infinity;
  for (let r = 0; r < rows; r++) {
    for (let c = 0; c < cols; c++) {
      res = Math.max(res, dfs(r, c, -1));
    }
  }

  return res;
};




```



---



## string 相關

### [678. Valid Parenthesis String](https://leetcode.com/problems/valid-parenthesis-string/)
Q: 給定一個字串 `s` 僅包含三種類型的字元： `'('` , `')'` 和 `'*'` ， 返回 `true` 如果 `s` 是有效的。
- 任何左括號`'('` 必須有對應的右括號`')'`
- 任何右括號`')'` 必須有對應的左括號`'('`
- 左括號`'('` 必須位於對應的右括號`')'` 之前
- `'*'` 可視為單一右括號`')'` 或單一左括號`'('` 或空字串 `""`


```ts fold
/**
 * @param {string} s
 * @return {boolean}
 */
var checkValidString = function (s) {
  let memo = {};


  function dfs(i, leftCount) {
    let key = i + "," + leftCount;
    if (key in memo) return memo[key];

    // is this necessary? yes, 表示 `())` 這個註定 invalid
    if (leftCount < 0) return false; 

    // 走到底... 如果 left 等於 0, 表示 () 都有配對/抵消了
    // 為何下面這個判定無法包括上面那個情境? -> 目前還沒想到，但是上面要加，不然會 fail
    if (i === s.length) {
      if (leftCount === 0) return true;
      else return false;
    }

    let isCurrentValid;
    // 有三種可能
    if (s[i] === "(") {
      isCurrentValid = dfs(i + 1, leftCount + 1);
    } else if (s[i] === ")") {
      isCurrentValid = dfs(i + 1, leftCount - 1);
    } else { // 表示 s[i] 是 *
      // * 代表 ( or ) or "" 
      isCurrentValid = dfs(i + 1, leftCount + 1) || dfs(i + 1, leftCount - 1) || dfs(i + 1, leftCount);
    }

    memo[key] = isCurrentValid;
    return isCurrentValid;
  }

  return dfs(0, 0);
};

```

錯誤記錄: 
沒有確認 right 在任何時候，都不可以大於left
只有檢查到終點的數量需要一樣，但是這樣不夠。
因為 `())(`  is not a valid para, but left and right count is the same
```js fold

var checkValidString = function(s) {
    
    let mem = {}
    function dfs(i, left, right){
        
        let key = i + ',' + left + ',' + right
        if(key in mem) return mem[key]

        // 錯誤的原因：少了這一行
        if(right > left) return false

        if(i===s.length && left !== right) return false
        if(i===s.length && left === right) return true



        if(s[i]==="("){
            mem[key] = dfs(i+1, left+1, right)
            return mem[key]
        } else if(s[i]===")"){
            mem[key] = dfs(i+1, left, right+1)
            return mem[key]
        } else if(s[i]==="*"){
            mem[key] = dfs(i+1, left+1, right) || 
                       dfs(i+1, left, right+1) || 
                       dfs(i+1, left, right)
            return mem[key]
        } else {
            console.log("nothing shall see")
        }
    }

    return dfs(0, 0, 0)
};


```


---


### [97. Interleaving String](https://leetcode.com/problems/interleaving-string/)
Q: 使用 s1 和 s2 去比對s3, 是否s可以透過交織 s1 and s2 得到？
s1和s2交織s3, 需要有維持string的順序性。

```ts fold

function isInterleave(s1: string, s2: string, s3: string): boolean {

    // 如果 s1 的長度和 s2 的長度加起來不會是 s3 的長度，就可以判斷 False
    if (s1.length + s2.length !== s3.length) return false

    /**

        use i, j 兩個 ptr
        指向 s1, s2
        如果 s1 可以 match 到 s3 上, s1 and s3 advance
             2                       2       
        
        
        如果上面可以 match, ret True -> 一直遞迴呼叫 直到每一個 char 都跑過, 就表示 s1, s2 可以 interleaving s3
        反之，就無法, 要 ret  false
     */

    let mem = {} // (i, j) -> T/F
    function dfs(i, j) {
        let key = i + ',' + j
        if (key in mem) return mem[key]

        // s3 走到底， s1 and s2 還有 -> false  <--這個條件其實不用
        // if((i + j) === s3.length && i < s1.length) return false
        // if((i + j) === s3.length && j < s2.length) return false

    
        // 因為目標是 s3 是 s1 and s2 交織構成，因此如果走到底，如果剛好對齊s3.length 就會是 good use case
        if (i === s1.length && j === s2.length && (i + j) === s3.length) return true
        // or 下面這樣也可以，(i + j) === s3.length 不是必要的，因為你下面這樣的條件去判斷，走到底就是 true
        // if (i === s1.length && j === s2.length) return true

				
				
				// 下面兩個 if 要分開，因為可以從 i or j 開始比對
        // if-else 就綁死誰先比了
        if (s1[i] === s3[i + j] && dfs(i + 1, j) === true) {
            mem[key] = true
            return true
        }

        if (s2[j] === s3[i + j] && dfs(i, j + 1) === true) {
            mem[key] = true
            return true
        }

        mem[key] = false
        return false
    }
    return dfs(0, 0)
};
```

### [Edit Distance](https://leetcode.com/problems/edit-distance/)

```ts fold

function minDistance(w1: string, w2: string): number {
//     https://leetcode.com/problems/edit-distance/discuss/159295/Python-solutions-and-intuition
    
//     two idx.. to track decision space moving..
//     we will continue slice both string and make them smaller along the way
//           
//     w1 = "horse"
              // 1 
//            j
//     w2 = "ros"
//     
//     goal: covert w1 to w2
    
    
    let memo = {}
    let dfs = (i, j) => {
        let key = i+","+j
        
        // if both reach end -> ret 0, no more ops, means they are equal for now
        // meaning they slice to empty string ""
        if(i === w1.length && j === w2.length) return 0
        
        // if only i/j reach end
        // return cur w2/w1 len  (the number of insert need)
        if(i === w1.length && j !== w2.length) return w2.length - j 
        if(i !== w1.length && j === w2.length) return w1.length - i
        
        
        if(key in memo) return memo[key]
        
        
        //  if both the same, both idx move 1
        if(w1[i] === w2[j]){
            let sameCase = dfs(i+1, j+1)
            memo[key] = sameCase
            return sameCase
            
        //     if both are dif, 3 choice
        //     replace    -> both idx move 1
        //     w1 delete  -> i move 1  
        //     w1 insert  -> j move 1   

        //     + 1 and find minOp
        
        } else {
            let replaceOp =  1 + dfs(i+1, j+1)
            let insertOp  =  1 + dfs(i, j+1)
            let deleteOp  =  1 + dfs(i+1, j)
            let curMinOp = Math.min(replaceOp, insertOp, deleteOp)
            memo[key] = curMinOp
            return curMinOp
        }
        
    }
    
    
    return dfs(0, 0)
};
```


### [712. Minimum ASCII Delete Sum for Two Strings](https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/)
```ts fold
function minimumDeleteSum(s1: string, s2: string) {

    let mem = {}
    function dfs(i, j){
        let key = i+','+j
        if(key in mem) return mem[key]

        if(i===s1.length && j===s2.length) return 0
        if(i===s1.length && j<s2.length) return getASCIIS(s2.slice(j))
        if(i<s1.length && j===s2.length) return getASCIIS(s1.slice(i))

        if(s1[i]===s2[j]){
            mem[key] = dfs(i+1, j+1)
            return mem[key]
        }
        // 如果 char on pts are different
        let deleteS1 = dfs(i+1, j) + s1[i].charCodeAt(0)
        let deleteS2 = dfs(i, j+1) + s2[j].charCodeAt(0)
        mem[key]  = Math.min(deleteS1, deleteS2)
        return mem[key]
    }
    return dfs(0,0)
};


function getASCIIS(s:string){
    let res = 0

    // get a ASCII value for a char: 'a'.charCodeAt(0)
    for(let char of s){
        res+=char.charCodeAt(0)
    }
    return res
}
```


## 子序列

### [Shortest Common Supersequence](https://leetcode.com/problems/shortest-common-supersequence/)
Q: 看 example 1 會比較好理解

cabac 拿掉 c → abac 就是 str1
cabac 拿掉 ac 就是 cab 就是 str2
找最短的 common supersequence
![[IMG-DP 2D-20250810200113419.png]]

```ts fold
function shortestCommonSupersequence(s1: string, s2: string): string {

    let mem = {}
    function dfs(i, j){
        let key = i + ',' + j
        if(key in mem) return mem[key]

        if(i===s1.length && j===s2.length) return ''
        if(i<s1.length && j===s2.length) return s1.slice(i)
        if(i===s1.length && j<s2.length) return s2.slice(j)




        // 如果一樣，都可以
        if(s1[i]===s2[j]){
            mem[key] = s2[j] + dfs(i+1, j+1)  // 錯誤記錄：要 concat 在前面
            return mem[key]
        } 

        // 如果不一樣，選短的
        let useS1 = s1[i] + dfs(i+1, j)  // 錯誤記錄：要 concat 在前面
        let useS2 = s2[j] + dfs(i, j+1)  // 錯誤記錄：要 concat 在前面
        let s1Len = useS1.length
        let s2Len = useS2.length
        if(s1Len<s2Len){
            mem[key] = useS1
            return useS1
        } else {
            mem[key] = useS2
            return useS2
        }
    }

    return dfs(0,0)
};
```


---


### [115. Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/)
Q:
給定 s, t 兩個 string
找出 s 裡面多少個 相異的 t子序列

子序列就是同順序的 string,但不用連在一起


Input: 
s = "rabbbit", 
t = "rabbit"

Explanation:
As shown below, there are 3 ways you can generate "rabbit" from s.

**rabb** b **it**
**ra** b **bbit**
**rab** b **bit**

```ts fold
/**

題目

給定s, t
找出 s 裡面多少個 t 子序列


要從s裡面去找到 t 子序列
子序列就是同順序的 string,但不用連在一起

Input: 
s = "rabbbit", 
t = "rabbit"

Explanation:
As shown below, there are 3 ways you can generate "rabbit" from s.
rabb b it 
ra b bbit
rab b bit

上面可以透過隔離出來的 b, 發現有三種隔離方法, 因此t可以在s裡面找到三個子序列


 */
function numDistinct(s: string, t: string): number {
    const memo = {};

    /**
     * Input: s = "babgbag", t = "bag"
        Output: 5
        Explanation:
        As shown below, there are 5 ways you can generate "bag" from s.
        babgbag
        ~~~
        babgbag
        ~~    ~ 
        babgbag
        ~    ~~
        babgbag
          ~  ~~  
        babgbag
            ~~~
     */
    // 找 s 裡面多少個 t 子序列
    function dfs(i, j) {
        // i, j 為 s, t 的 index
        let key = i + "," + j;
        if (key in memo) return memo[key];

        /**
         * 示意圖:
         *
         * s               t
         *    i             j
         * babgbag        bag
         *
         *
         * s               t
         *       i         j
         * babgbag        bag
         */

        
        /**
        這邊我有一次做錯，因為用huristic, 覺得題目是問, 有幾種 way, 因此下面用 1 + dfs(i + 1, j + 1) + dfs(i + 1, j)
        結果答案超大, 然後這邊又不確定要不要 ret 1 or 0

        這題比較特別，subseq 下面邏輯的特性，真正決定找到的地方就是這個 j === t.length 就算是找到
        你下面如果 +1, 那就是只要找到 一個一樣的 char 就 +1, 這樣就明顯不對
         */
        if (j === t.length) return 1;  // 如果 j 遍歷到 t 的最後，表示 t 已經找到全部匹配，ret 1

        // 如果 i 遍歷到 s 的最後，但 j  沒有遍歷到 t 的最後，表示 s 不匹配 t，ret 0
        if (i === s.length) return 0;

        // 如果 s[i] === t[j] 的話，就可以往前遍歷，看是否有其他的匹配
        if (s[i] === t[j]) {
            // 為何要dfs(i + 1, j)? -> 這就是這題的關鍵!! 因為這是 找sequence!, 不是找 substring
            // 因此就算找到了，j 還是可以留在原位置，不要往前遍歷, 這樣如果後續 s 有其他的匹配，j 一樣可以匹配到
            memo[key] = dfs(i + 1, j + 1) + dfs(i + 1, j);
        } else {
            // 如果不同，i+1, j 一樣不變，因為我們是在 s(i) 裡面找 t(j)
            memo[key] = dfs(i + 1, j);
        }

        return memo[key];
    }


    return dfs(0, 0);
}
```



note:
這兩個 base case 順序有差
如果下面那個放前面，會少算
```ts

if (j === t.length) return 1;
if (i === s.length) return 0;

```


---

### [392. Is Subsequence](https://leetcode.com/problems/is-subsequence/)
Q:
給定 s, t 兩個 string

如果 s 是 t 的子序列 return T, o.w. return F

範例 1：
Input: s = "abc", 

t = "ahbgdc"
     ~ ~  ~  
Output: true

範例 2：
Input: 
s = "axc", 
t = "ahbgdc"
     ~    ~  (no x between a and c) 
Output: false

s        t
i     j
abc   ahbgdc

s是否是t的子序列
t裡面找s 子序列


```ts fold

/**

給定 s, t 兩個 string

如果 s 是 t 的子序列 return T, o.w. return F

範例 1：
Input: s = "abc", 

t = "ahbgdc"
     ~ ~  ~  
Output: true

範例 2：
Input: 
s = "axc", 
t = "ahbgdc"
     ~    ~  (no x between a and c) 
Output: false

s        t
i     j
abc   ahbgdc

s是否是t的子序列
t裡面找s 子序列

 */
function isSubsequence(s: string, t: string): boolean {

    let mem = {}
    function dfs(i, j) {
        let key = i + ',' + j
        if (key in mem) return mem[key]

        // 如果 i 都走完就是找到了, 可以看下面的 code, 如果沒match, i 不會走
        if (i === s.length) return true

        // 如果 j 走完 但i 還沒到底，就是還沒找到
        if (j === t.length && i < s.length) return false



        if (s[i] === t[j]) {
            // match 都 ++
            mem[key] = dfs(i + 1, j + 1)
            return mem[key]
        } else {
            // 沒match, j++
            mem[key] = dfs(i, j + 1)
            return mem[key]
        }
    }

    return dfs(0, 0)
};
```

### [1143. Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)
Q: 給兩個字串，返回這兩個字串，最長公共子序列的長度
```ts fold

function longestCommonSubsequence(s1: string, s2: string): number {

    /**
    給 s1, s2
    求兩個 string 的 LCS
    LCS 定義：最長共同序列的長度, 序列不需要兩個 char 連在一起 (兩個 char 連一起叫做 substring)

    Input: text1 = "abcde", text2 = "ace" 
    Output: 3
    Explain: "ace" is LCS and and its length is 3. so ans is 3

    i, j
    2 ptr 指向 s1, s2
    如果一樣 -> 共同 char , 每一個 dfs要加上 1
    如果不同，有兩個選擇，i+1  OR j+1, 因此沒有找到 不需要 加上1

    要算 Max

    最後 j, j 其中一個走到底就結束了
     */

    let mem = {} // (s1, s2) -> MaxLCS
    function dfs(i, j) {
        let key = i + ',' + j
        if (key in mem) return mem[key]

        // 如果一個走完就可以 ret了，因為不可能找到 common cahr了
        if (i === s1.length || j === s2.length) return 0

        let localLCS1 = 0
        let localLCS2 = 0
        // 如果一樣, i, j 都加1
        if (s1[i] === s2[j]) {
            let localLCS1 = dfs(i + 1, j + 1) + 1
            mem[key] = localLCS1
            return mem[key]
        } else { // 如果不一樣
            let localLCS2 = Math.max(dfs(i + 1, j), dfs(i, j + 1))
            mem[key] = localLCS2
            return mem[key]
        }
    }
    return dfs(0, 0)
};

```

### [516. Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/)
Q:
給定一個 string s
裡面有很多 子序列
要找 palindromic的子序列, 找最長的
返回那個最長 palindromic子序列的長度

```ts fold

function longestPalindromeSubseq(s: string): number {
  // 
  // 從兩邊往內走
  // i   j
  // bbbab
  
  //  i j
  // bbbab
  // 上例中，i, j 一樣， ++2 => 因為是找 seq, 因此分開兩個 char 這樣是算的!

  //      i j
  //     bbbab

  let memo = {};
  let dfs = (i, j) => {
    let key = i + "," + j;
    if (key in memo) return memo[key];

    // 當兩個一樣時, why ret 1?
    // handle edge case: 如果 s = 'a' 一個 string, 要 ret 多少 -> 1
    if (i === j) return 1;

    // 當 i 反過來大於 j 表示結束
    if (i > j) return 0;

    // 如果一樣，表示貢獻了這個 subseq 2 的長度, then i+1, j-1 繼續走
    if (s[i] === s[j]) {
      memo[key] = dfs(i + 1, j - 1) + 2;
      return memo[key];
    } else {
      // 如果不一樣，有兩個走法，一個是 i+1, j 不動，另一個是 j-1, i不動
      memo[key] = Math.max(dfs(i + 1, j), dfs(i, j - 1));
      return memo[key];
    }
  };

  return dfs(0, s.length - 1);
}

```
