
- prefix sum 是一個資料結構
- 這個資料結構可以透過 idx 直接 get idx(含) 之前的 sum
- 也可以很快用這個資料結構來達出兩個 idx 圍的 sub array sum
- 然後題目也可以進一步去問，符合這個 sub arrau sum === k 的條件下，最常的 subarray len, 或是有幾個 sub array 等等問題




## prefix sum 建立

**prefix sum**  https://leetcode.com/problem-list/prefix-sum/

建立一個 **prefix sum arr**

idx 1 是 idx to 1 的 sum
idx 2 是 idx to 2 的 sum 
idx 6 是 idx to 6 的 sum
就是下面的藍色 arr
![[IMG-prefix sum-20250810200102726.png]]

如果你要一個區間的 sum
like idx`[2, 5]`  inclusive → `p[6] - p[2-1]` = 18

因此如果你要拿 idx 到幾的 sum
p$i$: 0~i 的 sum

```ts fold

// arr 作法

              [          ]
idx        0 1 2  3  4  5  6
arr        1 2 3  4  5  6  7 
prefixsum  1 3 6 10 15 21 28



          [    ]
idx        0 1 2  3  4  5  6
arr        1 2 3  4  5  6  7 
prefixsum  1 3 6 10 15 21 28



function getPrefixSum(arr) {
  // 我們需要初始化 as 0, 因為我們要第一個元素是 0
  let res = new Array(arr.length + 1).fill(0);

  // 第一個元素是 0, 後面的元素是前面的元素加上後面的元素
  // [  1,2,3, 4, 5, 6,  7]   <-- so we need to use arr.length + 1 for res
  // [0,1,3,6,10,15,21, 28]
  
  // // PS. 下面是 arr.length, 不是 res.length, 不然你下面會無窮loop, 你會一直增加 res 的長度
  for (let i = 1; i <= arr.length; i++) {
    // res 從1 開始， 拿 res[0] 加上 arr[0]
    res[i] = res[i - 1] + arr[i - 1];
  }
  return res;
}




const nums = [1, 2, 3, 4, 5, 6, 7];
let prefixMap = new Map();
prefixMap.set(0, 0);

let sum = 0;
for (let i = 0; i < nums.length; i++) {
  sum += nums[i];
  prefixMap.set(i + 1, sum); // begin from 1 (0+1=1)
}
console.log("prefixMap -->", prefixMap);
// prefixMap --> Map(8) {
//   0: 0,
//   1: 1,
//   2: 3,
//   3: 6,
//   4: 10,
//   5: 15,
//   6: 21,
//   7: 28,
// }

```

- 可以用 array 來表示 prefixsum, 也可以用 map/obj, 這邊用 map/obj, 因為後者多了 look-up 功能，變得很好用

## 問題

彙整
- rangeSumQuery: 求給定區間的和 → 求 subarray sum
- subarr sum equal k:   求有多少個 subarray sum === k
- contiguous array:  求 subarray sum === 0 的最長 subarr  (題目是問0,1 相等，但可以把0轉為-1)
- max size subarr sum equal k: 求 subarray sum === k 中最長的 subarr 長度
- continuous subarr sum: 求是否可找到 subarr sum === k 的倍數 且 subarr長度是2,  ret T/F
- 下面有幾個類似 subarr sum 都不適合用 prefix map pattern
    - minimal size subarr sum: 求滿足 subarr sum ≤ target 的subarr最小長度 → 用 SW,  找最小，因此適合用 shrinking condition 的 SW
    - maximum subarray: 找最大和的那個subarray, ret 最大和 → Kadane 演算法
    - maximun product subarray: 找最大積的那個subarray, ret 最大積 → Kadane 演算法





- why 當 `m1.has(curSum-k)` 的時候表示這個時候的 idx 跟前一個 idx 之間的 sum 是 k -> 看例子才比較好理解



### [Range Sum Query - Immutable](https://leetcode.com/problems/range-sum-query-immutable/description/)
實作一個 class
這個 class  init with an arr
然後 method 會給 [left, right], 算 left, right 區間的 sum, inclusive
![[IMG-prefix sum-20250810200106970.png]]
```ts fold

class NumArray {
  prefixMap

  constructor(nums: number[]) {
    this.prefixMap = new Map();
    this.prefixMap.set(0, 0); // 初始化：索引 0 的前綴和為 0

    let sum = 0;
    for (let i = 0; i < nums.length; i++) {
      sum += nums[i];
      this.prefixMap.set(i + 1, sum); // begin from 1 (0+1=1)
    }
  }

  sumRange(left: number, right: number): number {
    return this.prefixMap.get(right + 1) - this.prefixMap.get(left); // 利用前綴和計算區間和
  }
}
```

### [Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/description/)
求subarray 的 sum === k 的數量
![[IMG-prefix sum-20250810200111061.png]]
```ts fold
function subarraySum(nums: number[], k: number): number {
    let count = 0; // 子陣列計數
    let curSum = 0; // 当前currentSum

    // mapping: curSum -> counts
    let m1 = new Map()

    m1.set(0, 1) // 初始化的狀態：curSum 0，出現1次

    for (let i = 0; i < nums.length; i++) {
        curSum += nums[i];

        // 檢查 (sum - k) 是否存在於哈希表中
        // 如果相減的累積和 在哈希表中存在，表示用 i 作為 array 結尾，總和為k的子陣列存在
        // 我們將 m1.get(curSum - k) 加到 count 中，因為每個之前出現的 curSum - k 都可以與當前位置組成一個符合條件的子陣列
        if (m1.has(curSum - k)) {
            count += m1.get(curSum - k)
        }


        // 每一輪都要更新 curSum
        if (!m1.has(curSum)) {
            m1.set(curSum, 1)
        } else {
            m1.set(curSum, m1.get(curSum) + 1)
        }
    }

    return count;
}
```


---

### [Contiguous Array](https://leetcode.com/problems/contiguous-array/description/) 
→ 轉為尋找總和為零的最長子陣列

subarray 裡面包括的 0 和 1 數量要一樣
求這一種 subarray的最長的長度
![[IMG-prefix sum-20250810200113147.png]]

```ts fold

function findMaxLength(nums: number[]): number {
  // 透過將 0 轉換為 -1，我們將問題轉變為尋找總和為零的最長子陣列。
  const arr = nums.map((num) => (num === 0 ? -1 : 1));

  // 初始化一個映射來儲存累積和及其第一次出現的索引
  // 累積和 -> 索引
  let m1 = new Map()
  m1.set(0, -1) // 累積和為 0 時, idx is -1


  let res = -Infinity;
  let curSum = 0;

  for (let i = 0; i < arr.length; i++) {
    curSum += arr[i];

    // 每當我們找到一個之前出現過的累積和時，就表示先前出現的位置和當前索引之間的子陣列總和為零。 
    if (m1.has(curSum)) {
      const length = i - m1.get(curSum); // 計算子陣列的長度
      res = Math.max(res, length);
    } else {
      // 儲存這個累積和第一次出現的索引
      m1.set(curSum, i)
    }
  }

  return res;
}
```


###  [911 · Maximum Size Subarray Sum Equals k](https://www.lintcode.com/problem/911/)
給定 nums arr and k
求 subarray 的 sum 是 k 的
找這種 subrarray 最長的的長度有多少
找不到的話，return 0
![[IMG-prefix sum-20250810200114201.png|713]]

```ts fold

export class Solution {
  /**
   * @param nums: an array
   * @param k: a target value
   * @return: the maximum length of a subarray that sums to k
   */
  maxSubArrayLen(nums: number[], k: number): number {
    const m1 = new Map();

    // 記錄 idx, 才可以算最長
    m1.set(0, -1); // curSum -> idx

    let res = 0;
    let curSum = 0;

    for (let i = 0; i < nums.length; i++) {
      curSum += nums[i];

      // 表示找到區間了, 拿 idx 來找最長 subarray 
      // why? 看下面解釋
      if (m1.has(curSum - k)) {
        const prevIndex = m1.get(curSum - k)!;
        res = Math.max(res, i - prevIndex);
      }

      // 建立映射如果還沒的話
      if (!m1.has(curSum)) {
        m1.set(curSum, i);
      }
    }

    return res;
  }
}



```

解釋：
例子: 
nums:         `[1,-1,5,-2, 3]`
prefixmap   `[1,0, 5, 3, 6]`
idx                 0  1     2    3    4
到 idx 3 時， subArrSum 是 3 (就是題目要的 k)
那跟一開始  subArrSum 是 0 
因此我們查找的條件就是  `m1.has(curSum - k)` 這時候的 idx 和之前的 idx 建構的區間就是 subArrSum 為 3

### [Continuous Subarray Sum](https://leetcode.com/problems/continuous-subarray-sum/description/)

if 這個 arr 有 good 的 subarray → ret true
o.w. → false

good 是指:
- 長度至少2
- sum 需要是 k 的倍數
![[IMG-prefix sum-20250810200114785.png]]

```ts fold

function checkSubarraySum(nums: number[], k: number): boolean {
  const m1 = new Map(); // 儲存前綴和對 k 取餘數的映射
  m1.set(0, -1); // 初始化：remainder 0 出現在索引 -1。 mapping: remainder -> index
  
  let curSum = 0; // 紀錄當前的累積和

  for (let i = 0; i < nums.length; i++) {
    curSum += nums[i];
    
    const remainder = curSum % k; // 計算當前累積和對 k 取餘數

    // 如果 remainder 已經出現過，則表示存在一個子陣列的總和是 k 的倍數。
    // 这是因为，如果两个前缀和对 k 的余数相同，那么这两个前缀和之间的子数组的总和一定是 k 的倍数
    if (m1.has(remainder)) {
      // 检查子数组长度是否至少为 2
      if (i - m1.get(remainder) >= 2) {
        return true; // 找到符合條件的子陣列
      }
    } else {
      // 如果當前餘數沒有出現過，則將其加入映射中
      m1.set(remainder, i);
    }
  }

  return false; // 表示都無法找到符合條件的子陣列
}


```