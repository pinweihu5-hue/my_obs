

## 總結
- 重新開始 or 繼續累積 curSum / curProd(有兩種可能，nest Math.max)


## 題目
### [53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)

找最大加總的那個subarray, 並返回其加總


PS: subarray指連在一起的array部分
![[IMG-Kadane-20250810200102691.png]]
```ts fold



// 這是更簡潔的邏輯
// 不需要去記錄要從 idx 0 開始去 初始化 curSum and maxSum
// 然後也不需要去刻意去記憶從 1 開始
function maxSubArray(nums: number[]): number {
    let curSum = -Infinity;
    let maxSum = -Infinity;
    for (const ele of nums) {
        curSum = Math.max(ele, curSum + ele);
        maxSum = Math.max(maxSum, curSum);
    };
    return maxSum
}






// 錯誤記錄
// 不是  curSum or curSum + nums[i]
// 是    nums[i] or curSum + nums[i] -> 重新開始 or 繼續累積 curSum
curSum = Math.max(curSum, curSum + nums[i])









// commet ver2
function maxSubArray(nums: number[]): number {

    // init 從 nums[o]
    let curSum = nums[0]
    let globalSum = nums[0]

    // 從索引 1 開始遍歷數組（因為索引 0 已經用於初始化）
    for (let i = 1; i < nums.length; i++) {
        
        // curSum += nums[i] <--- 寫錯的邏輯,  這樣子的邏輯，就只是隨著 loop 一直往上加而已
        
        // 透過 Math.max, 會變成是 "選擇性"的要不要往下加，只會選擇更大的情況下，才會加上 nums[i]
        // 因此 curSum 也是一直在變動的
        curSum = Math.max(nums[i], curSum+nums[i])


        // 最後透過 globalSum 幫助 update curSum 中曾經有過的最大值
        globalSum = Math.max(globalSum, curSum)
    }
    return globalSum
};




// comment ver1
function maxSubArray(nums: number[]): number {
  let maxSum = nums[0]; // 我們將 maxSum 初始化為陣列的第一個元素。這處理了陣列只有一個元素或第一個元素最大的情況。
  let currentSum = nums[0]; // 我們也將 currentSum 初始化為第一個元素。這個變數將跟踪當前正在考慮的子陣列的總和。

  // 迴圈從第二個元素（i = 1）開始，因為我們在初始化時已經考慮了第一個元素。
  for (let i = 1; i < nums.length; i++) {

    // 這是 Kadane 演算法的核心
    // 我們是否應該從當前元素（nums[i]）開始一個新的子陣列
    // 或者我們應該通過包含當前元素（currentSum + nums[i]）來擴展先前的子陣列
    currentSum = Math.max(nums[i], currentSum + nums[i]);
    
    // 在確定 currentSum 之後，我們將其與目前為止找到的 maxSum 進行比較。如果 currentSum 更大，我們就更新 maxSum
    maxSum = Math.max(maxSum, currentSum);
  }

  return maxSum; // 返回最大子数组和
}
```
### [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/description/)
找最大的積的那個subarray, 並返回積
![[IMG-Kadane-20250810200106927.png]]

```ts fold



// new comment
function maxProduct(nums: number[]): number {
    let maxSoFar  = nums[0]
    let minSoFar  = nums[0]
    let res = nums[0]

    for (let i = 1; i < nums.length; i++) {

        // 重新開始 or 繼續累積 curProd
        // curProd 有兩種可能 nums[i] * maxSoFar and nums[i] * minSoFar, 因此需要取其大 or 取其小
        
        // 另外，這邊建立一個maxTmp, 不直接 assing back to maxSoFar, 是因為 第 16 行的 maxSoFar
        // 預計是要下一個 iteration 才應該被用到，不是下一行, 因此這邊先用 maxTmp 接住, 下面 20 行 assign back
        let maxTmp = Math.max(nums[i], Math.max(nums[i] * maxSoFar, nums[i] * minSoFar))
        let minTmp = Math.min(nums[i], Math.min(nums[i] * maxSoFar, nums[i] * minSoFar))

        // 上面會互相引用，因此需要拉出來
        maxSoFar =maxTmp
        minSoFar =minTmp  

        res = Math.max(maxSoFar, res)
    }
    return res
};

```


mroe comment ver, same logic
```ts fold


function maxProduct(nums: number[]): number {
  if (nums.length === 0) return 0; // Handle empty array case

  // use first one to init all 3
  let maxSoFar = nums[0];
  let minSoFar = nums[0]; // 我們需要 minSoFar, 因為可能會因為負負得正下一輪變成最大的 product
  let res = nums[0];

  for (let i = 1; i < nums.length; i++) {

    // kadane的經隨，只選 cur 或是跨大選取
    // 這邊擴大選取，是乘上 maxSoFar 和 乘上 minSoFar, 取大的
    // 兩層的 Math.max
    let tempMax = Math.max(nums[i], Math.max(nums[i] * maxSoFar, nums[i] * minSoFar));

    // 這邊取 負的意義上，負最大的, so use Math.min
    let tempMin = Math.min(nums[i], Math.min(nums[i] * maxSoFar, nums[i] * minSoFar));


    // 上面算完，這邊存起來，下一輪會用這兩個基礎來算新一輪的值
    // 需要這兩個 temp
    // 如果你直接把上 tempMax 用 maxSoFar 取代, 那你算 tempMin 時，你的 maxSoFar 已經不是上一輪
    // 算的數，而是剛剛上面算的數字
    maxSoFar = tempMax;
    minSoFar = tempMin;

    // Update the overall maximum product if needed
    res = Math.max(res, maxSoFar);
  }

  return res;
}

```



錯誤記錄
```ts

function maxProduct(nums: number[]): number {
    let res = -Infinity
    let maxSoFar = 1
    let minSoFar = 1

    for (let i = 0; i < nums.length; i++) {

        // 你這樣寫的話，你這邊 被 assigned 的 maxSoFar 不就馬上又被 11 行的 給使用了嗎?
        maxSoFar = Math.max(nums[i], Math.max(maxSoFar * nums[i], minSoFar * nums[i]))
        minSoFar = Math.min(nums[i], Math.min(maxSoFar * nums[i], minSoFar * nums[i]))

        let tmpMax = maxSoFar
        let tmpMin = minSoFar


        res = Math.max(res, tmpMax)
    }
    return res
};


```



---

### [The Maximum Subarray](https://www.hackerrank.com/challenges/maxsubarray/problem?isFullScreen=true)

一次要求 max subarray sum and max subseq sum in one go
```ts fold

function maxSubarray(arr) {


  // 這裡需要跟 max subseq 一起算， so we need to idx start from 0
  // 所以我們不 init from arr[0] and start from idx 0, so just use ele of arr to loop is enough
  let curSum = -Infinity;
  let maxSum = -Infinity;
  
  let maxSeqSum1 = 0;
  let maxSeqSum2 = -Infinity;

  for (const ele of arr) {

    // 這兩行，我們用 Kadane 算法可以得到 max subarray sum
    curSum = Math.max(ele, curSum + ele);
    maxSum = Math.max(maxSum, curSum);



    // 下面我們來找 max subsequence sum
    // 只要有一個 ele 大於 0 -> max subsequence sum 就是 正整數 的加總
    // 如果 ele 小於等於 0 -> 我們用 maxSeqSum2 來記錄出現過的最大的非正整數, e.g. like -1 > -2 之類的
    if (ele > 0) {
      maxSeqSum1 += ele;
    } else {
      maxSeqSum2 = Math.max(maxSeqSum2, ele);
    }
  }

  // 這裡不可以用 maxSeqSum2 是否等於 -Infinity 來判斷
  // 因為如果一個 arr 裡面有正有負，那 maxSeqSum1 會 > 0 and maxSeqSum2也不等於 -Infinity
  // and in this case, we still choose use maxSeqSum1
  if (maxSeqSum1 > 0) {
    // 如果 ele 有被累積過大於 0 -> use it
    return [maxSum, maxSeqSum1];
  } else {
    // o.w. 表示都沒被正整數累加過 -> use max negative number
    return [maxSum, maxSeqSum1];
  }
}

```