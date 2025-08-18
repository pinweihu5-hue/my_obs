---  
alias:  
creation_date: 2024-10-09 20:51  
related

---  


### [704. Binary Search](https://leetcode.com/problems/binary-search/) 

```js fold

const search = function(nums , t) {
    
    // 找出兩個端點的 idx
    let l=0
    let r=nums.length-1
    
    
    while(l<=r){
        let m = Math.floor((l+r)/2)
        if(nums[m] > t) r=m-1
        else if(nums[m]<t) l=m+1
        else return m
    }
    
    return -1
    
};
```

### [278. First Bad Version](https://leetcode.com/problems/first-bad-version/)

預設有 isBadVersion 可以用會給你 T/F 可以讓你知道這個 ver 是否是 bad version


```js fold
/**
 * The knows API is defined in the parent class Relation.
 * isBadVersion(version: number): boolean {
 *     ...
 * };
 */

var solution = function (isBadVersion: any) {

    /**
    
    n = 5, 5個版本
        

         m
    --1--2--3--4--5
               x  x (bad ver)
               4 is the first bad ver     

    如果2是   good, 往右找 l = m+1
    binary search 找 until
    l===r
    then return l is ans          
    
     */

    return function (n: number): number {
        let l = 0
        let r = n
        while (l <= r) {
            let m = Math.floor((l + r) / 2)
            if (!isBadVersion(m)) {
                // 往右邊找
                l = m + 1
            } else {
                r = m - 1
            }
        }

        return l




    };
};



// reference
function binarySearch(arr, target) {
    let l = 0
    let r = arr.length - 1

    while (l <= r) {
        let m = Math.floor((l + r) / 2)
        if (arr[m] === target) {
            return m
        } else if (arr[m] > target) {
            r = m - 1
        } else if (arr[m] < target) {
            l = m + 1
        } else {
            console.log('shall not happen')
        }
    }
}
```

### [35. Search Insert Position](https://leetcode.com/problems/search-insert-position/)
```ts fold

function searchInsert(nums: number[], target: number): number {
    let l = 0
    let r = nums.length-1
    
    
    while(l<=r){
        let m = Math.floor((l+r)/2)
        if(nums[m] > target){
            r=m-1
        } else if(nums[m] < target){
            l=m+1
        } else {
            return m
        }
    }

    return l
};
```

### [367. Valid Perfect Square](https://leetcode.com/problems/valid-perfect-square/)
```ts fold

function isPerfectSquare(num: number): boolean {

    /**
    1~nums 裡面去找
    看看是否可以找到 ^2 是 他自己
     */

    let l = 1
    let r = num
    let target = num
    while (l <= r) {
        let m = Math.floor((l + r) / 2)
        let mSquare = m * m
        if (mSquare > target) {
            r = m - 1
        } else if (mSquare < target) {
            l = m + 1
        } else if (mSquare === target) {
            return true
        } else {
            console.log("shall not happen?")
        }
    }
    return false

};

```

### [374. Guess Number Higher or Lower](https://leetcode.com/problems/guess-number-higher-or-lower/)



### [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)
給定一個 nums, sorted 遞增
找給定 target
不過這個target 可能會重複  in this nums arr
如果重複要找出上下 bound 的 idx

like below
![[IMG-Binary Search-20250810200102673.png]]
first 8 is idx 3
second 8 is idx 4



解法
**2 round to update** 

first round to find 右邊的可能
second round to find 左邊的可能
**ini default arr res [-1,-1] a**
找到後，繼續往右邊找end position, 如果找的到
update res[1]
找到後，繼續往左邊找starting position, 如果找的到
 update res[0]
```js fold

var searchRange = function(nums, target) {
    let l = 0
    let r = nums.length - 1
    
    let res = [-1,-1]
    
    while(l<=r){
        let m = Math.floor((l+r)/2)
        if (nums[m] > target) r=m-1 
        else if (nums[m] < target) l=m+1
        else {
            res[1] = m
            l=m+1
        }
    }
    
    l = 0
    r = nums.length - 1
    while(l<=r){
        let m = Math.floor((l+r)/2)
        if (nums[m] > target) r=m-1
        else if (nums[m] < target) l=m+1
        else {
            res[0] = m
            r=m-1
        }
    }
    
    return res
};
```






### [74. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)
```js fold

function searchMatrix(matrix, target) {
    let arr = []
    matrix.forEach(ele => {
        arr = arr.concat(...ele)
    })
    
    let l =0
    let r = arr.length-1
    while(l<=r){
        let m = Math.floor((l+r)/2)
        if(arr[m] > target) r=m-1
        else if (arr[m] < target) l=m+1
        else return true
    }
    
    return false
    
}

```

## search in rotated array
### [153. Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)
給一個sorted array, 是被 rotated 過的 
找最小值

need to solve in logN time → no sort(nlogn TC) → use BS

```js fold



/**


nums[m] >= nums[l] -> m at left part  
     m
[4,5,6,7,0,1,2]

find min-> go right

===
         
nums[m] <  nums[l] -> m at right part  
           m
[4,5,6,7,0,1,2]

find min-> go left

還有一個情況是
nums[l] < nums[r]
-> 第一個就是 min


*/


function findMin(nums) {
  // two end ptr for Binary Search
  let l = 0;
  let r = nums.length - 1;

  // find min basic setup for end result
  let res = Infinity;

  // while loop common setup for binary search
  while (l <= r) {


    // 當 subarr 本身已經 sorted 的時候，可以直接判斷, 不需要用 bs 切
    if (nums[l] <= nums[r]) {
      // res = nums[l] 錯誤記錄 -> 這樣寫，你會找到局部最小就 ret, 但我們要的是全局最小, 因為這邊也只是一個 subarr
      res = Math.min(res, nums[l]);
      break;  // 加上 break, 因為這個子數組已經找到最小，不需要繼續用 BS 切
    }

    // 下面就是要用 BS 去找
    let m = Math.floor((l + r) / 2);
    // 每切一次就先 update 一次 res
    res = Math.min(res, nums[m]);

    // 跟 33 題一下，用 nums[m] 跟  nums[l] 比較來知道 nums[m] 是在 左半邊還是右半邊
    /**
     * why nums[m] >= nums[l] 表示 mid 在 是左半邊
     * 因為 rotate 的結果造成
     * 如果 nums[m] 比右端點大，表示 nums[m] and nums[l] 都已經被 rotate 過了
     * 這時, nums[m] 在左半邊
     * 例子
     * 4 5 6 7 0 1 2
     * l   m <------------------- assume m here
     * (5,6,7 could be m)
     * ~~~~~~~  <- 4,5,6,7 都被 rotate 過了
     *
     * 如果 nums[m] 比右端點小，表示 only nums[l] 被 rotate 過
     * 這時, nums[m] 在右半邊
     * 例子
     * 4 5 6 7 0 1 2
     * l         m  <------------------- assume m here
     * (0,1,2 could be m)
     */
    if (nums[m] >= nums[l]) {
      // mid 在 是左半邊
      //  4 5 6 7 0 1 2 3
      //      m 
      // smaller(0,1,2,3) 要往右找
      l = m + 1;
    } else {
      // mid 在 是右半邊
      //  4 5 6 7 0 1 2 3 
      //              m 
      // smaller(0,1) 要往左找 
      r = m - 1;
    }
  }

  return res;
}


```


錯誤記錄
1.

`res = nums[l]` -> 我會這樣寫，然後因為後面也有 break, 表示我忘了這邊只是一個 subarr 下的最小
因為我這邊如果就ret, 我那就是 ret 一個已經被 subarr 下的最小，但未必是整個 arr 的最小

why here is only subarr? 
因為在 while(l<=r) 的 loop 中, so, l, r is keep changing over the whole for-loop, so the new subarr is keep forming

PS: 
這邊也一定要加上 break, 不然就無限loop, why?
因為這個時候 l 也還沒 大於 r, 因此不會跳出 while loop

```ts

    if (nums[l] <= nums[r]) {
      // res = nums[l] 錯誤記錄 -> 這樣小，你會找到局部最小就 ret, 但我們要的是全局最小, 因為這邊也只是一個 subarr
      res = Math.min(res, nums[l]);
      break;  // 加上 break, 因為這個子數組已經找到最小，不需要繼續用 BS 切
    }


```


2

看下面 comment
因為判斷 m 在左右邊的條件是這樣的...
m如果在左邊的條件是 `>=` 不是只有 `>`

    m 
`4 5 6 7 1 2 3`

上面的例子, m 落在 4567 時，找 min 都要往右邊找
因此 4 也包括在裡面, `hense nums[m] >= nums[l]`


```js fold

function findMin(nums: number[]): number {
    let l = 0
    let r = nums.length - 1
    let res = Infinity

    while (l <= r) {
        if (nums[l] <= nums[r]) {
            res = Math.min(res, nums[l])
            break
        } else {

            let m = Math.floor((l + r) / 2)
            res = Math.min(res, nums[m])

            if (nums[m] > nums[l]) {  // <-- 這邊要有等於
                l = m + 1

            } else {
                r = m - 1
            }


        }
    }
    return res
};

```



---

### [33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)
給定一個 arr 找 給定的target
但是這個 arr 提供前，會被 rotated!
一定要 logN 解 → 就是不可以跑 sort (nlogn)

→ use BS to solve
```js fold



/**
nums[m] >= nums[l] -> m at left part  
     m
[4,5,6,7,0,1,2]
       t

如果 taret >= m -> go right
如果 taret < m: 
    if target >= nums[l] -> go left
    if target < nums[l] -> right

         
nums[m] <  nums[l] -> m at right part  
           m
[4,5,6,7,0,1,2,3]
             t

if t < m:
    go left
if t > m:
    if nums[r] < t:
        go left
    if nums[r] >= t:
        go right
 */






function search(nums, target) {
  let l = 0
  let r = nums.length - 1

  while (l <= r) {
    let m = Math.floor((l + r) / 2)

    /**
     * why nums[m] >= nums[l] 表示 mid 在 是左半邊
     * 因為 rotate 的結果造成
     * 如果 nums[m] 比右端點大，表示 nums[m] and nums[l] 都已經被 rotate 過了
     * 這時, nums[m] 在左半邊
     * 例子
     * 4 5 6 7 0 1 2
     * l   m
     * (5,6,7 could be m)
     *
     * 如果 nums[m] 比右端點小，表示 only nums[l] 被 rotate 過
     * 這時, nums[m] 在右半邊
     * 例子
     * 4 5 6 7 0 1 2
     * l         m
     * (0,1,2 could be m)
     */
    if (nums[m] >= nums[l]) {
      // m at 左半邊

      // 然後又區分為 target 比 nums[m] 大 or 小兩種情況
      if (target > nums[m]) {
        l = m + 1 // 如果 target 比 nums[m] 大，往右找
      } else if (target < nums[m]) {
        if (target >= nums[l]) {
          // 如果 target 比 nums[m] 小，有兩種情況，要用nums[l]判斷
          r = m - 1 // 如果 target 比 nums[l] 大，往左找
        } else {
          l = m + 1 // 如果 target 比 nums[l] 小，往右找
        }
      } else {
        return m
      }
    } else {
      // m at 右半邊

      // 跟上面類似..
      if (target < nums[m]) r = m - 1
      else if (target > nums[m]) {
        if (target > nums[r]) r = m - 1
        else l = m + 1
      } else return m
    }
  }

  return -1 // 找不到
}


```


more on BS
[Notion – The all-in-one workspace for your notes, tasks, wikis, and databases.](https://www.notion.so/nture4388/Binary-Search-5f096c892ded48caa43ee96e42d7d328?pvs=4#10a9df69750f8035a55fce60b48ac6ec)