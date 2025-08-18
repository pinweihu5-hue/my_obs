

題目
### [125. Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)

Q:
給一個 string 確認是否是pali


==


除了判斷是否是pali外，這題還需要濾掉其他 non-Alphanumeric 的 char 才可以判斷

```js fold


function isPalindrome(s1: string): boolean {
    let s = covert(s1)

    let l=0
    let r=s.length-1
    while(l<=r){
        if(s[l]!==s[r]) return false
        l++
        r--
    }
    return true
};


function covert(s){
    const s1 = s.toLowerCase()
    //    console.log("s1", s1)


    let res = ""
    for (let i = 0; i < s1.length; i++) {
        const code = s1[i].charCodeAt()
        if(is_a_to_z(code) || is_0_to_9(code) ){
            res+=s1[i]
        }
    }

    return res
}
// only check lower case
function is_a_to_z(code: number){
    if(code >=97 && code <= 122) return true
    else return false
}

function is_0_to_9(code: number){
    if(code >=48 && code <=57) return true
    else return false
}


```

### [11. Container With Most Water](https://leetcode.com/problems/container-with-most-water/)
```js fold

const maxArea = function (height) {
    let maxArea = 0
    let l = 0
    let r = height.length - 1

    while (l < r) {
        // 算出 裝水 area
        area = (r - l) * Math.min(height[l], height[r])
        // keep track of maxArea
        maxArea = Math.max(maxArea, area)
        
        // 關鍵邏輯
        // 把小的那邊先內縮
        // 下面就是，如果左比右小，l++
        if (height[l] < height[r]) {
            l++
        } else {
            r--
        }
    }

    return maxArea

};

```

### [167.Two Sum II - Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)
```js fold

function twoSum(nums: number[], target: number): any {

    let l = 0
    let r = nums.length - 1
  
    while (l < r) {
  
      if ((nums[l] + nums[r]) > target) r--
      if ((nums[l] + nums[r]) < target) l++
      if ((nums[l] + nums[r]) === target) return [l + 1, r + 1];
  
    }
  
  };

```

### [15.3sum](https://leetcode.com/problems/3sum/)
```ts fold

function threeSum(nums) {
    let res = []
    nums.sort((a, b) => a - b)

    for (let i = 0; i < nums.length; i++) {
        if (i > 0 && nums[i] === nums[i - 1]) continue

        let j = i + 1
        let k = nums.length - 1

        while (j < k) {
            let target = nums[i] * -1
            if (nums[j] + nums[k] > target) k--
            else if (nums[j] + nums[k] < target) j++
            else { 
                res.push([nums[i], nums[j], nums[k]])
                j += 1
                k -= 1
                while (nums[j] === nums[j - 1]) j += 1
            }
        }
    }
    return res

};


```





錯誤記錄
(下面的code已經修改過，是對的)


下面這邊是把 i 作為要比對的, 然後包住的區域去找跟i加起來可以是0
1 2 3 4 5
L     R i


跟上面的作法不同，上面一樣是比i作為要比對的
然後沒包住的地方去找跟i加起來可以是0
1 2 3 4 5
i  L       R


這兩種作法都可以



```js fold

var threeSum = function (nums) {
    nums.sort((a, b) => a - b)

    let res = []
    for (let i = 0; i < nums.length; i++) {
        if (nums[i] === nums[i + 1]) continue

        let l = 0
        let r = i - 1
        while (l < r) {
            let target = nums[i] * -1
            if (nums[l] + nums[r] > target) {
                r--
            } else if (nums[l] + nums[r] < target) {
                l++
            } else {
                res.push([nums[l], nums[r], nums[i]])
                
                
                l++
                r--

                while (nums[l] === nums[l - 1]) l++
                while (nums[r] === nums[r + 1]) r--

                // 錯誤記錄，應該要放在上面, 因為邏輯是這樣的：
                // 如果match, 那ptr 就可以走了
                // 那如果有重複，就繼續移動
                // l++
                // r--

            }
        }
    }

    return res

};



```

---

### [977.Squares of a Sorted Array](https://leetcode.com/problems/squares-of-a-sorted-array/description/)
Q:  給定一個 sorted arr, `nums = [-4,-1,0,3,10]` 需 return 一個平方後的 arr, sored ->  `[0,1,9,16,100]`

```ts fold

function sortedSquares(nums: number[]): number[] {
  let res = [];
  let l = 0;
  let r = nums.length - 1;

  /**
  2 ptr, 取 abs, 2 end pt 兩端往內走
  誰大，就 unshift 入 res arr

    l         r
  [-4,-1,0,3,10]
  10 > |-4|
  [100]


   l       r
  [-4,-1,0,3,10]
  |-4| > 3
  [16, 100]


       l   r
  [-4,-1,0,3,10]
  |3| > |-1|
  [9, 16, 100]


   */

  while (l <= r) {
      if (Math.abs(nums[l]) > Math.abs(nums[r])) {
          res.unshift(nums[l] * nums[l]);
          l++;
      } else {
          res.unshift(nums[r] * nums[r]);
          r--;
      }
  }

  return res;
}


```




### [1498 Number of Subsequences That Satisfy the Given Sum Condition](https://leetcode.com/problems/number-of-subsequences-that-satisfy-the-given-sum-condition/description/)
給定 nums, 找這個 nums中 seq 的組合有多少個可以符合其 max(seq)+min(seq) <= taget

```ts fold

function numSubseq(nums: number[], target: number): number {
  const mod = 10 ** 9 + 7;
  const n = nums.length;
  nums.sort((a, b) => a - b); // 排序数组

  // 预处理 2 的幂次
  let powerOf2 = Array.from({length: n}, ()=> 1)
  for (let i = 1; i < n; i++) {
	 powerOf2[i] = (powerOf2[i-1])*2 % mod  // =>  // [ 1, 2, 4, 8 ] 
  }

  let l = 0;
  let r = n - 1;
  let res = 0;

  while (l <= r) {
    if (nums[l] + nums[r] <= target) {
      res = (res + powerOf2[r - l]) % mod;
      l++; 
    } else {
      r--;
    }
  }

  return res;
}



```

TLE:
- 透過 sort 和比較兩邊往內縮(`l = 0`, `r = nums.length-1`)
	- 這一題最天真的想法：找出所有的 subseq ，然後算其max and min 的 sum, 看看有多少個，但是這樣一定 TLE。
	- 更好的解法：
		- 先排序，因此 給定一個區間, `nums[l]` 是 min,  `nums[r]` 是 max , 因此 `nums[l] + nums[r] <= target` 就是題目要問的了
		- 如果目前還沒超過 target , l 往右走(因為 sort 由小到大), l++, 找到更多組合 累加到 res
		- 如果超過target, r 往左走，r-- ，因為 r 是大的那邊，加起來會比較小，這邊因為超過，不會累加到res
- 如何給區間去計算組合數
	- 使用 powerOf2
	- 先建立出這個: [1,2,4,8] , 可以優化速度
		- 0 個選項 1 個組合,  1 個選項 2個組合(選和不選), 2個選項 4個組合(`2*2`), 3個選項 8個組合(`2*2*2`)
	- 一個區間有多少組合  -> `powerOf2[那個區間有多少 ele]`
	- 我們上面的 code 只有 r-l, 不是 r-l+1 是因為我們不考慮空組合, 因此簡化計算下，這樣直接少一個 ele, 這樣答案會一樣(有心可以驗算)
- 如何使用 mod
	- 建立 powerOf2 and 累加都要加上 mod, 因為這兩邊都可能會太大
		- 就無法用 res+= xxx, 需要 res =  (res + xx) % mod

衍生閱讀 [[what is subseq, 如何計算一個 subseq的組合]]


== gpt explain, 參考
**解释:**

1. **预处理:**
   - `MOD`:  定义模数，避免结果溢出。
   - `powersOfTwo`:  预先计算 2 的幂次，方便后续计算组合数。

2. **排序:**
   - 对数组进行排序，方便使用双指针技巧。

3. **双指针:**
   - `left`:  指向子序列的左边界。
   - `right`:  指向子序列的右边界。
   - 当 `left <= right` 时，循环执行以下操作：
     - 如果 `nums[left] + nums[right] <= target`，说明以 `left` 为左边界，`[left, right]` 之间的任意组合都是合法的子序列，其数量为 `2^(right - left)`。
     - 否则，说明以 `right` 为右边界的所有组合都不合法，需要将 `right` 左移。

4. **计数:**
   - `count`:  记录合法子序列的数量。
   - 在循环过程中，不断累加 `count`，并对 `MOD` 取模，避免溢出。

**核心思想:**
- 利用排序和双指针技巧，快速找到所有满足条件的子序列。
- 利用预处理的 2 的幂次，快速计算组合数。

**时间复杂度:** O(N log N)，其中 N 是数组的长度，主要取决于排序的时间复杂度。
**空间复杂度:** O(N)，主要取决于存储 2 的幂次的数组。

====

 这里解释一下为什么 `[left, right]` 之间的任意组合都是合法的子序列， 以及为什么其数量为 `2^(right - left)`。


Ben note: 下面這一個 block GPT  解釋怪怪的，參考就好
**1. 为什么 `[left, right]` 之间的任意组合都是合法的子序列？**

* 首先， 我们已经对数组进行了排序， 因此 `nums[left]` 是 `[left, right]` 区间内的最小值， `nums[right]` 是最大值。
* 题目要求子序列中最小值和最大值之和不超过 `target`， 而我们已经保证了 `nums[left] + nums[right] <= target`。
* 那么， 在 `[left, right]` 区间内， 任意选择一些数形成的子序列， 其最小值一定大于等于 `nums[left]`， 最大值一定小于等于 `nums[right]`， 因此它们的和也一定不超过 `target`， 满足题目要求。

**2. 为什么组合数是 `2^(right - left)`?**

* 在 `[left, right]` 区间内， 我们有 `right - left + 1` 个数可以选择。
* 对于每个数， 我们都有两种选择： **选** 或 **不选**， 来决定它是否属于最终的子序列。
* 因此， 总的组合数就是 2 * 2 * ... * 2  (一共 `right - left + 1` 个 2 相乘)， 即 `2^(right - left + 1)`。
* 但是， 这其中包含了 **一个数都不选** 的情况， 它不是合法的子序列（题目要求非空子序列）。
* 所以， 我们需要减去 1， 最终的组合数就是 `2^(right - left + 1) - 1`。

**简化计算:**

* 由于我们在代码中是先加上 `2^(right - left)`， 然后再移动 `left` 指针， 
* 因此， 可以直接使用 `2^(right - left)` 来表示当前 `left` 指针下的合法子序列数量， 
* 这样就避免了在每次循环中都进行一次减 1 的操作， 简化了计算。







### [986 Interval List Intersections ](https://leetcode.com/problems/interval-list-intersections/description/)
Q:
給定兩組 intervals
like
`firstList = [[0,2],[5,10],[13,23],[24,25]]`
`secondList = [[1,5],[8,12],[15,24],[25,26]]`
已經 sorted, 另外自己沒有 overlapping
求兩個 interval 的重疊,  output : `[[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]]`
(或看下圖)


```ts fold

function intervalIntersection(l1: number[][],l2: number[][]): number[][] {
   const res = [];
 
   // 初始化兩個指針，分別指向 l1 和 l2 的起始位置
   let i = 0;
   let j = 0;
 
   // 當兩個指針都還在各自的列表範圍內時，繼續迭代
   while (i < l1.length && j < l2.length) {
     // 獲取當前兩個指針指向的區間
     const [start1, end1] = l1[i];
     const [start2, end2] = l2[j];
 
     // 下面開始起始點的最大值和終點的最小值
     const maxStart = Math.max(start1, start2);
     const minEnd = Math.min(end1, end2);
 
     // 如果起始點的最大值小於等於終點的最小值，則表示兩個區間有重疊
     if (maxStart <= minEnd) {
       // 將重疊部分 [maxStart, minEnd] 加入結果数组
       res.push([maxStart, minEnd]);
     }
 
     // 移動終點值較小的區間的指針, 下一輪繼續檢查終點大的區間是否會有重疊
     if (end1 < end2) {
       i++;
     } else {
       j++;
     }
   }
 
   // 返回所有找到的交集區間
   return res;
 }


```

TIL:
- 如何判斷兩組 intervals 重疊 (見下圖)
![[IMG-Two Pointer-20250810200102694.png|398]]


錯誤記錄
```js

var intervalIntersection = function (l1, l2) {
    let i = 0
    let j = 0
    let res = []

    while (i < l1.length && j < l2.length) {
        let a1 = l1[i]
        let a2 = l2[j]

        // 錯誤
        // 這裡不是比兩個區間的頭和尾
        // 這邊要先解構出兩個區間的頭尾，然後找出 max頭和min尾
        // 要比較的是 max 頭和尾, 因為這個 max 頭和尾
        // 不一定會在不同一個，也可能在同一個
        if (a1[1] >= a2[0]) {
            res.push([Math.max(a1[0], a2[0]), Math.min(a1[1], a2[1])])
        }

        if (a1[1] < a2[1]) {
            i++
        } else {
            j++
        }
    }
    return res
};

```


### [Valid Mountain Array](https://leetcode.com/problems/valid-mountain-array/description/)

beautiful solution
```ts fold

function validMountainArray(arr: number[]): boolean {
  const n = arr.length;
  if (n <= 2) return false; // 数组长度小于 3，不符合山脉数组的定义

  let i = 0;

  // 1. 向上攀登：寻找山顶
  while (i < n - 1 && arr[i] < arr[i + 1]) {
    i++;
  }

  // 2. 检查是否到达山顶
  // i 根本沒有動，表示根本沒有遞增
  // or i 已經到底了，表示全部都是遞增
  // 都是 false
  if (i === 0 || i === n - 1) return false;


  // 3. 向下行走：从山顶下降
  while (i < n - 1 && arr[i] > arr[i + 1]) {
    i++;
  }

  // 4. 检查是否到达数组末尾
  // // 如果到达数组末尾，说明是有效的山脉数组
  return i === n - 1; 
}

```
TIL:
- 用 i 的移動和位置判定
	- 如果遞增  `arr[i] < arr[i + 1]` , `i++`
	- 如果遞增 `i++`, 看 i 的位置如果是 0 or len-1 就知道不是
	- 如果遞減  `arr[i] > arr[i + 1]` , `i--`
	- 如果走到底就是 pass


我第一次解，天真解
```ts fold


/**
找 山頂
check 遞增到山頂
check 遞減到山下
排除全部遞增和遞減
**/

function validMountainArray(arr: number[]): boolean {
    if (arr.length <= 2) return false


    let maxIdx = findMaxIdx(arr)
    for (let i = 1; i <= maxIdx; i++) {
        let prev = arr[i - 1]
        let cur = arr[i]
        if (prev >= cur) return false
    }

    for (let i = arr.length-2; i>=maxIdx; i--) {
        let prev = arr[i + 1]
        let cur = arr[i]
        if (prev >= cur) return false
    }

    // prevent all is increasing like, [0,1,2,3,4,5,6,7,8,9]
    if(arr[arr.length-1] > arr[arr.length-2]) return false

    // prevent all is decreasing like, [9,8,7,6,5,4,3,2,1,0]
    if(arr[0] > arr[1]) return false

    return true
};


function findMaxIdx(arr) {
    let maxNum = -Infinity
    let maxIdx = -1
    for (let i = 0; i < arr.length; i++) {
        let ele = arr[i]
        if (ele > maxNum) {
            maxNum = ele
            maxIdx = i
        }
    }
    return maxIdx

}


```




### [Shortest Subarray to be Removed to Make Array Sorted](https://leetcode.com/problems/shortest-subarray-to-be-removed-to-make-array-sorted/description/)
Q: 給定一個 arr, 要移除 subarr 來讓 arr 變成 非遞減 (or layman term 遞增但可以等於)
求要移除 subarr 的最短長度

例子
arr = [1,2,3,10,4,2,3,5]
移除  [10,4,2] 就可以
output: 3

```ts fold

function findLengthOfShortestSubarray(nums) {
  const n = nums.length;
  let r = n - 1;

  // 先從右邊往左邊找，看看最長的區間有多少
  while (r > 0 && nums[r] >= nums[r - 1]) r--;

  // 到這邊，r右邊(含)都是遞增
  
  /* 
  * ele 5 4 3 2 1 2 3 4 5
  *             r
  */

  // 数组已经是非递减的，不需要移除任何元素
  if (r === 0) return 0; 

  /**
   *
   * 初始化需要移除的子数组的长度
   * 如果左邊全部都是遞減，那左邊全部都要 remove, 因此當下 r 的 idx 就是左邊所有 ele的數量
   * 
   * for example:
   * idx 0 1 2 3 4 5 6 7 8
   * ele 5 4 3 2 1 2 3 4 5
   *             r
   * 左邊 5,4,3,2 要移除，共 4個，也是 r 的 idx
   */
  let res = r;

  // 然後開始從左邊找
  for (let l = 0; l < n; l++) {
    // 因為我們要找遞增(非遞減), 因此如果從左邊過來是遞減, 結束尋找
    // l > 0 因為我們要比當前和前一個，l > 0 才開始比
    if (l > 0 && nums[l - 1] > nums[l]) break;

    // 這邊是看要用兩邊區間的那一邊當做最大遞增, 然後中間就是要移除的
    // 如果 l 比 r 大, r 就需要 ++, 這樣才構成遞增, 不然中間移除掉，就又變成遞減了(如果 r比l小)
    // 這邊是 while, 需要 r++ as long as nums[l] > nums[r]
    while (r < n && nums[l] > nums[r]) r++;

    // 這邊要算的是移除的 ele
    // 因此是兩個 idx 包住的區間的那些 ele, 因此要減1
    res = Math.min(res, r - l - 1);
  }

  return res;
}
```
TIL:
- 這題談的是非遞減 -> 就是  遞增 + 等於
- 知道如何移動 ptr 來找遞增區間
- 知道如何算要移除的 ele 數量 given 兩端 idx:  `right - left - 1`
- 要去想像中間是要移除的，因此其實要比大小的就是 num[l] and num[r],  非遞減就是 num[r] >= num[l]  
	- 因此需要 as long as nums[l] > nums[r], r 就要一直 ++


錯誤記錄
```js

var findLengthOfShortestSubarray = function (arr) {
    let n = arr.length
    let r = n - 1

	// 錯誤1: 這邊應該要加上 r >=0 的條件
    while (arr[r - 1] <= arr[r]) r--
    if (r === 0) return 0

    let l = 0
    let res = r - l

    for (let l = 0; l < n; l++) {


		// 錯誤2:
        // 下面寫錯了。這邊要反過來處理，如果不符合，要 break
        // 因為你下面這樣寫，會讓 l一直遞增，最後 iter 到最後，l, r 都是最後一個 n-1 -> res = 0
        if (l > 0 && arr[l - 1] <= arr[l]) {
            l++
        }

        // 錯誤3:
        // 這邊 應該是 r<n, 因為 已經是小於了，不應該用 n-1
        while (r < n - 1 && arr[l] > arr[r]) r++

		// 錯誤4:
        // 你要算兩者間要拿掉的 element, 要用 r-l-1
        // 整理一下：
        // r-l+1 -> 兩個 idx 間有多少個 ele (包括 2 的端點) -> 求 subarr 的數量
        // r-l-1 -> 兩個 idx 間有多少個 ele (不包括 2 的端點) -> 這一題的情境，中間要 remove 多少 element
        // r-l   -> 兩個 idx 距離差異
        res = Math.min(res, r - l)
    }

    return res
}

```



錯誤記錄
```js

var findLengthOfShortestSubarray = function (arr) {

    let n = arr.length
    let r = n - 1
    while (r >= 0 && arr[r] >= arr[r - 1]) r--

    // 錯誤記錄：忘記加上這一行
    if (r === 0) return 0; 


    let res = r

    for (let l = 0; l < n; l++) {

        if(l > 0 && arr[l-1] > arr[l]) break

        // 錯誤記錄，這邊要用 break, 反過來看， why?
        // if (l > 0 && arr[l - 1] <= arr[l]) {
        //     l++
        // }

        while (r <= arr.length - 1 && arr[l] > arr[r]) r++

        res = Math.min(res, r - l - 1)

    }

    return res
};

```







---


其他 2 ptr
- [Notion – The all-in-one workspace for your notes, tasks, wikis, and databases.](https://www.notion.so/nture4388/Two-Pointer-a0481529e11740aeafd6bca630582f6d?pvs=4#947e8b960e604a6a9f3f26eea606916f)
- [Leetcode刷題學習筆記--Two Pointers - HackMD](https://hackmd.io/@meyr543/B16lIiSzF)