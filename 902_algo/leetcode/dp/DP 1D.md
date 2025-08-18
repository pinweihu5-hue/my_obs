

### [509. Fibonacci Number](https://leetcode.com/problems/fibonacci-number/)
```ts fold
var fib = function(n) {
    let memo = {}
    function dfs(n){
        if (n in memo) return memo[n]
        if (n==0) return 0
        if (n==1) return 1
        memo[n] = dfs(n-1) + dfs(n-2)
        return memo[n]
    }
    
    return dfs(n)
    
};
```

### [fibonacci-modified](https://www.hackerrank.com/challenges/fibonacci-modified/problem?isFullScreen=true)
```ts fold
function fibonacciModified(t1, t2, n) {
    let mem = {}
    function dfs(n){
        if(n in mem) return mem[n]
        if(n===0) return BigInt(t1)
        if(n===1) return BigInt(t2)
        if(n>1 ){
            mem[n] = dfs(n-1)*dfs(n-1) + dfs(n-2)
            return mem[n]
        }
    }
    
    return dfs(n-1)

}

```

### [70. Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)
Q: 
You are climbing a staircase. It takes n steps to reach the top.
Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

```ts fold

function climbStairs(n: number): number {

    let mem = {}

    function dfs(n) {
        if (n === 0) return 1
        if (n < 0) return 0
        if (n in mem) return mem[n]
        
        
        const res = dfs(n - 1) + dfs(n - 2)


        mem[n] = res
        return res
    }

    return dfs(n)
};

```
### [746. Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/)
Q:
You are given an integer array cost where cost[i] is the cost of ith step on a staircase. 
Once you pay the cost, you can either climb one or two steps.
You can either start from the step with index 0, or the step with index 1.
Return the minimum cost to reach the top of the floor.



從root走下去比較直覺，在base case 處裡可stop index 0 or 1
```ts fold
function minCostClimbingStairs(cost: number[]): number {
    /**
    
    cost arr 的 val 是 爬梯的 cost
    找最小 cost
    可以爬一個 or 爬兩個
    爬到最後一個是超出 idx

    一個推薦作法是從 top 往前面走 
    cost.push(0)

    然後往前走， -1 or -2 兩種走法
    每一次走法都可以算 min cost

    走到 idx 0 or idx 1 就算是結束

    (PS: not try, 但是如果要往上走，可能要跑兩次找 min)
     */

     cost.push(0)
     let mem = {}
     function dfs(i){
        if(i in mem) return mem[i]

        // 走到最後一個，就可以 ret
        // 我們用ret 來返回 cost
        // 這邊也要算上那個 idx 上的成本
        if(i === 0 || i === 1) return cost[i]
        if(i <0) return 0

        ///////

        /**
                 0  1  2  3
        cost = [10,15,20, 0]

              dfs(3)
          dfs(2)  dfs(1)
        
         */

        // 算上這一層的 idx, then 往下走?
        let goDownOneStep = dfs(i-1) + cost[i]
        let goDownTwoStep = dfs(i-2) + cost[i] 
        let res = Math.min(goDownOneStep, goDownTwoStep)

        ///////
        mem[i] = res
        return res
     }

     return dfs(cost.length-1)
};
```

## house robber
### [198. House Robber](https://leetcode.com/problems/house-robber/)
```js fold

var rob = function(nums) {
    let memo = {}
    
    function dfs(i){
        if (i >= nums.length) return 0
        if (i in memo) return memo[i]
        
        // let robCurGain = dfs(i) + nums[i]
        // 錯誤: 寫成上面那樣，一直不知道如何表示 robcur and next next one
        // 結論是下面這樣 nums[i] 就是 rob cur, dfs(i+2) 就是 next next one
        // 另外我原本的錯誤 code, 因為 dfs(i) 就一直卡在無限回圈也是一個提示，只是我 無法想到
        let robCurGain = nums[i] + dfs(i+2)
        let robNextGain = dfs(i+1)
        let curMaxGain = Math.max(robCurGain, robNextGain)
        memo[i] = curMaxGain
        return curMaxGain
    }
    
    return dfs(0)
    
};
```
### [213. House Robber II](https://leetcode.com/problems/house-robber-ii/)
```js fold

var rob = function(nums) {
    
    if(nums.length === 1) return nums[0]
    
    let noFirst = nums.slice(1) // nums[1:] 
    let noLast = nums.slice(0, nums.length-1) // nums[:-1]
    return Math.max(robHelper(noFirst), robHelper(noLast))
    
    function robHelper(nums){
        let memo = {}

        function dfs(i){
            if (i >= nums.length) return 0
            if (i in memo) return memo[i]

            // let robCurGain = dfs(i) + nums[i]
            // 錯誤: 寫成上面那樣，一直不知道如何表示 robcur and next next one
            // 結論是下面這樣 nums[i] 就是 rob cur, dfs(i+2) 就是 next next one
            // 另外我原本的錯誤 code, 因為 dfs(i) 就一直卡在無限回圈也是一個提示，只是我 無法想到
            let robCurGain = nums[i] + dfs(i+2)
            let robNextGain = dfs(i+1)
            let curMaxGain = Math.max(robCurGain, robNextGain)
            memo[i] = curMaxGain
            return curMaxGain
        }

        return dfs(0)
        }
};
```

### [740. Delete and Earn](https://leetcode.com/problems/delete-and-earn/)
看例子比較好懂..
![[IMG-DP 1D-20250810200102756.png]]
[3,4,2], pts = 0

選了4來砍  pts+=4
arr 變成 [3,2]

另外 4+1=5 and 4-1=3都要全部一起砍掉
因此arr剩下 [2]

選2砍     pts+=2
arr 變成-> []


pts = 6 as result




![[IMG-DP 1D-20250810200106984.png]]
[2,2,3,3,3,4], pts = 0

選了3來砍  pts+=3
=>  [2,2 3,3,4]

另外 3+1=4 and 3-1=2都要全部一起砍掉 
因此arr剩下 [3,3]

選3砍     pts+=3
arr 變成->  [3]

選3砍     pts+=3
arr 變成->  []



pts = 9 as result





===

這一題的觀念是要想到: **要把 array 的 idx treat it as value**
them, array val 放的是累積的值
另外因為要這樣放，因此 array length, 必須是題目給的 nums 裡面的 max value + 1 (因為 idx 0 我們不會使用)
```js fold
/**
 * @param {number[]} nums
 * @return {number}
 */
var deleteAndEarn = function (nums) {
  /**
   * why 這題其實可以轉為robbers problem?
   * 因為你選了，就是有分數, 但是你放棄你選的那個 val 的上下val的分數
   * 因此你如果選了 3, 你就可以一直選3, get 9 pts, 但是你就放棄了 2 and 4 的 all pts
   * 
   * 所以你要建立一個 arr
   * arr idx 就是那個 val 本身
   * arr val 是那個 val 在整個 arr 的加總值
   * 
   * 這個 arr length 需要是 這個 arr max val + 1
   *   (why? 因為如果你有 arr 裡面最大的 val 是 4, 你需要 idx 4 來放大，那你的 arr 是否就需要有 5 個 lot? 0~4, length is 5)
   * 
   *  nums = [2,2,3,3,3,4]
   *   0  1  2  3  4    -> idx 放的是 val
   *  [0  0  4  9  4 ]  -> val 放的是 加總值
   */


  let arr = Array(Math.max(...nums) + 1).fill(0);
  for (let ele of nums) {
    arr[ele] += ele;
  }

  let memo = {};
  function dfs(i) {
    if (i in memo) return memo[i];
    if (i >= arr.length) return 0;

    // rob cur and cannot rub next, so i+2
    let robCur = dfs(i + 2) + arr[i];
    // no rub cur (so no `+arr[i]`) and can go next, so i+1
    let noRobCur = dfs(i + 1);

    let curRobMax = Math.max(robCur, noRobCur);
    memo[i] = curRobMax;
    return curRobMax;
  }

  return dfs(0);

  // ref solving
  // https://leetcode.com/problems/delete-and-earn/discuss/109895/JavaC%2B%2B-Clean-Code-with-Explanation
  // https://leetcode.com/problems/delete-and-earn/discuss/109871/Awesome-Python-4-liner-with-explanation-Reduce-to-House-Robbers-Question
};

```

## 算可能數
### [91. Decode Ways](https://leetcode.com/problems/decode-ways/)
number of ways to decode it.  ⇒ number of ways, 也是某種 組合類 的題目


```js fold

function numDecodings(s: string): number {
    /**      
                         
        idx   012       
             "12"
            1  12

          2


     */

    let mem = {}
    function dfs(i){
        if(i in mem) return mem[i]
        if(s[i]==="0") return 0 // invalid
        if(i===s.length) return 1
        if(i>s.length) return 0 // overshot

        let singleDigit = dfs(i+1)

        // 要挑兩個 digit 是有條件的
        // 合法的 doubleDigit range 是 10~26
        // 如果第一個 digit 是 1, 第二個 digit 是 0~9
        // 如果第二個 digit 是 2, 第二個 digit 是 0~6
        let t1 = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
        let t2 = ['0', '1', '2', '3', '4', '5', '6']
        let doubleDigit = 0
        let c1 = s[i] === '1' && t1.includes(s[i+1])
        let c2 = s[i] === '2' && t2.includes(s[i+1])
        if(c1 || c2){
            doubleDigit = dfs(i+2)
        }



        mem[i] = singleDigit + doubleDigit
        return mem[i]
    }

    return dfs(0)
    
};
```
TIL:
- 這題考點在於：什麼情況下你才可以 call dfs
	- 何時才算是 valid oneDigit , 何時是 valid twoDigit
- 算可能數(combination number, number of ways) -> 都是最後到 good base case return 1 的加總，dfs 呼叫不會加總。 


另一個寫法，把允許的都列出來
```js fold

var numDecodings = function(s) {


    let oneToNine = ['1','2','3','4','5','6','7','8','9']
    let ZeroToNine = ['0','1','2','3','4','5','6','7','8','9']
    let ZeroToSix = ['0', '1','2','3','4','5','6']
    

    let mem = {}
    return dfs(0)
    function dfs(i){
        if(i in mem) return mem[i]

        if(i>=s.length) return 1


        let oneStep = 0
        if(oneToNine.includes(s[i])){
            oneStep = dfs(i+1)
        }   

        let twoStep = 0
        if(
            (s[i]==="1" && ZeroToNine.includes(s[i+1])) || 
            (s[i]==="2" && ZeroToSix.includes(s[i+1]))
        ){
            twoStep = dfs(i+2)
        }


        mem[i] = oneStep + twoStep
        return mem[i]
    }
};


```



---

### [139. Word Break](https://leetcode.com/problems/word-break/)
Q:
given `s` as `string`
given `wordDict` as `string[]`
s 是否可以被 切成 成 wordDict 裡面的 string?
return True or false




===

**先建立** `wordDict`  words 的 `len` 的 `arr` 來切 s segment來判斷 
 (1d: string切segment的idx區間)
 ```js fold
 var wordBreak = function (s, wordDict) {
  // 建立 new arr, 對應到 wordDict arr 每一個 string 的長度 → 我們要用這個來切 s 然後跟 wordDict 比較
  let lenOfWords = wordDict.map((e) => e.length)
  let s1 = new Set(wordDict) // 用 set 避免 wordDict arr 有重複

  let memo = {}

  function dfs(i) {
    if (i in memo) return memo[i]

    // 這題是要走到 length, not length-1 why?
    // 這會跟下面的判斷有關，因為邏輯是，當你走到 length-1當下，還是需要往下走，不可以這裡 ret 掉
    // 當跨過去最後一個 ele, 才算是全部結束，才可以 ret
    // 可以切到剛剛好的 length-1, 就是 true
    if (i === s.length) return true

    for (const lenOfWord of lenOfWords) {
      // 這一題用我們建立的 wordLen 去跑
      let seg = s.slice(i, i + lenOfWord)
      // 一路都要 在wordDict裡面。最後就是 true
      // 下一個切就是從 i + lenOfWord 開始
      if (s1.has(seg) && dfs(i + lenOfWord)) {
        memo[i] = true
        return true
      }
    }

    // 如果上面沒有切好，剩下的情境就是 ret false
    // 這一題的邏輯，變得要分別 ret and save into memo (why?)
    // 因為判斷 ret true 的 case 有滿多準備工作要處理的, (prep lenOfWords, slice seg, s1.has....)
    memo[i] = false
    return false
  }

  return dfs(0)
}

```
 

### [300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)
Q: 
給一個arr, 找出最長嚴格遞增數列的長度數
遞增數列: 如果remove掉其中幾個ele也可，只要order不變和有嚴格遞增即可



===
需要 loop from next index and find bigger one and get pre_val


```js fold


function lengthOfLIS(nums: number[]): number {
    let mem = {}
    return dfs(0, -Infinity)
    function dfs(i, prev) {
        let key = i + ',' + prev
        if (key in mem) return mem[key]

        if (i === nums.length) return 0

        let localLIS = 0
        for (let j = i; j < nums.length; j++) {
            if (nums[j] > prev) {
                localLIS = Math.max(localLIS, dfs(j+1, nums[j]) + 1)
            }
        }

        // 錯誤記錄:
        // 首先，你用這種 loop 方法，無法控制 指針從那邊開始, 因此下面這種 loop 就不好用了
        // 這邊忘記的第一個觀念是:
        // 你忘記 確認是否比較大是從 i 開始，不是從0開始喔
        // 這邊忘記的第二個關鍵是：
        // 另外 你也忘記要用 j+1, 你要往下走，因為你要 try out every possible seq in one loop
        // let localLIS = 0
        // for (let [idx, ele] of nums.entries()) {
        //     if (ele > prev) {
        //         localLIS = Math.max(localLIS, dfs(idx, ele) + 1)
        //     }
        // }

        mem[key] = localLIS
        return mem[key]
    }
};








function lengthOfLIS(nums: number[]): number {
    let mem = {}
    function dfs(i, prev) {
        let key = i + ',' + prev
        if (key in mem) return mem[key]

        if (i == nums.length) return 0

        // 用這個會錯誤, why, 次數應該要從 0 開始, use -Infinity 
        // 這個 test case [1,3,6,7,9,4,10,5,6] -> 5, 但應該是 6
        // let localLIS = -Infinity 
        
        let localLIS = 0


        /**
        
        下面這個是錯誤的
        why?
        這個是指 val 要大於 prev 才可以進行 for loop
        下面正確的是, 進行 for loop 但 val 要大於 prev 才可以進入 dfs
         */
        // if (nums[i] > prev) {
        //     for (let j = i; j < nums.length; j++) {
        //         localLIS = Math.max(localLIS, dfs(j + 1, nums[j]) + 1)
        //     }
        // }

        for (let j = i; j < nums.length; j++) {
            if (nums[j] > prev) {
                localLIS = Math.max(localLIS, dfs(j + 1, nums[j]) + 1)
            }
        }




        mem[key] = localLIS
        return mem[key]
    }

    return dfs(0, -Infinity)
};



function lengthOfLIS(nums: number[]): number {

    let mem = {}
    function dfs(i, prev) {
        if (i in mem) return mem[i]
        if (i === nums.length) return 0

        let localLIS = 0
        // 錯誤點: let j = 0 
        // 應該是: let j = i
        // 下一次應該從上一次開始的地方開始 loop
        for (let j = i; j < nums.length; j++) {
            if (nums[j] > prev) {
                // 錯誤點:  dfs(i+1, nums[j])
                // 應該是:  dfs(j+1, nums[j])
                // 你這樣會呼叫很多個 dfs 出去, 因此 dfs 當然是跟 inner loop 的 j 走
                // 反過來說，你如果是跟 i 走，那你這個 loop 不就是每一個loop 呼叫的 dfs
                // 都是同一個i?
                localLIS = Math.max(localLIS, dfs(j + 1, nums[j]) + 1)

            }
        }
        mem[i] = localLIS
        return mem[i]
    }

    return dfs(0, -Infinity)
};




/**
 * @param {number[]} nums
 * @return {number}
 */
var lengthOfLIS = function (nums) {
  let memo = {};

  // 需要跟上一個比，才會知道是否有遞增, so pass preV
  function dfs(i, preV) {
    if (i in memo) return memo[i];

    let curLIS = 0;
    // nums 從 i 開始往後挑, 每一輪有很多個分支
    for (let k = i; k < nums.length; k++) {
      if (nums[k] > preV) { // 需要嚴格遞增，因此需要 `> preV`
        // `1+` 是每一次選了，都要 + 1
        // `K+1` 選了後，要往下一個走 
        // 然後會有很多個分支，我們只要最大的分支就好了, 因此我們這邊需要找 Max
        curLIS = Math.max(curLIS, 1 + dfs(k + 1, nums[k]));
      }
    }

    memo[i] = curLIS;
    return curLIS;
  }

  // why -Infinity?
  // 第一個 element 一定都可以，因此用 -Infinity 跟他比就一定會比 -Infinity 大
  return dfs(0, -Infinity);
};
```



錯誤記錄
```js

var lengthOfLIS = function(nums) {

    let mem = {}
    return dfs(0, -1) // 寫錯，要用 -Infinity -> 更多 test case 就會發現
    function dfs(i, prev){
        let key = i + ',' + prev
        if(key in mem) return mem[key]
        if(i===nums.length) return 0

        let LIS = 0
        // 這裡是 let j=i
        // 當下這個也要看看是否可以
        for(let j=i+1; j<nums.length; j++){
            if(nums[j] > prev){
                
               // 這邊是 dfs(j+1, nums[j]+1)
               // 這邊才需要+1, 因為這個+1是要看下一個
               LIS = Math.max(LIS, dfs(j, nums[j]) + 1)
            }
        }
        mem[key] = LIS
        return mem[key]
    }
};

```

---

### [377. Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)

給一個數字array, 裡面不會重覆。另外也給一個整數 taregt
求可以array可以加總到taregt 的全部可能組合的數量
![[IMG-DP 1D-20250810200111779.png]]



=====

This question is to asking the ways, so use DP, not backtracking
```js fold
function combinationSum4(nums: number[], target: number): number {
  let mem = {};
  return dfs(0);
  function dfs(sum) {
    if (sum in mem) return mem[sum];

    if (target === sum) return 1;
    if (target < sum) return 0;

    /**
     * 錯誤記錄：
     *  let choose = dfs(i, sum + nums[i]);
     *  let notChoose = dfs(i + 1, sum);
     *  mem[key] = choose + notChoose;
     *  return mem[key];
     * 這種寫法會錯。因為不會納入不同組合的情況
     * 題目: nums = [1,2,3], target = 4
     * 類似 (1, 1, 2), 但是我們需要 (1, 1, 2), (1, 2, 1), (2, 1, 1)
     * 類似 (1, 3), 但是我們需要 (1, 3), (3, 1)
     * 因為這種寫法的邏輯就是 選 or 不選 -> branch factor 是 2
     * `1` 只會被選一次或是不選，然後也是放在第一個
     * 因為題目需求: 不同 seq 也算不同組合
     * 因此我們需要每一輪(每一次dfs)都要可以挑 each element as the first one -> 才可能會上面的  (2, 1, 1) 的這個組合
     * 像是第一次 for loop, 就是把每一個 ele 當做 組合的第一個，然後開始往下迭代
     * 因此要用下面的寫法
     */


    let curNum = 0;
    for (let ele of nums) {
      curNum += dfs(ele + sum);
    }
    mem[sum] = curNum;
    return mem[sum];

    
  }
}
```

TIL:
- 需要每一輪(每一次dfs)都要可以挑 each element as the first one -> 才可能會有 (2, 1, 1) 的這個組合
	- 第一次 for loop, 就是把每一個 ele 當做 組合的第一個，然後開始往下迭代