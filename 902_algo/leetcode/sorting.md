related: 
- [[cs61b quick sort]]
- [[cs61b Basic Sorts]]


### [912. Sort an Array](https://leetcode.com/problems/sort-an-array/)
都用這題來測試 -> nlong 才會過




### quick sort

先做出 parition(nums: number[], pivotEle: number, smaller: number[], equal: number[], bigger:number[])

```ts fold

function sortArray(nums: number[]): number[] { 
    return quickSort(nums) 
}

function quickSort(nums: number[]){

    // 小於等於 1 就不用 sort 了, 不可以用 === 1, 有可能parition 後，會等於 []
    // 如果你忘記為何 <=1 is quick sort base case, 回去複習 CS61B quick sort and check ppt     
    if(nums.length <= 1) {
        return nums
    }

    // shall use random to pick pivot
    // 因為根據 nums 的 inversion situation, choose wrong pivot could lead to N^2, each partition just
    // sort one or very small amt of element
    let pivotEle = nums[Math.floor(nums.length * Math.random())]
    let smaller = []
    let equal = []
    let greater = []
    parition(nums, pivotEle, smaller, equal, greater);  // use simple 3 way pass to do partition

    // 左邊和右邊，持續的遞迴呼叫
    smaller = quickSort(smaller)
    greater = quickSort(greater);
    
    // 錯誤點 -> 我忘記 reassign, 這樣請問你下面要怎麼組 XDD
    //quickSort(smaller)
    //quickSort(greater);


    // return 之後，組起來
    return (smaller.concat(equal)).concat(greater)
};


function parition(nums: number[], pivotEle: number, smaller: number[], equal: number[], bigger:number[]){
    for (let i = 0; i < nums.length; i++) {
        let cur = nums[i]
        if(cur<pivotEle) smaller.push(cur)
        if(cur===pivotEle) equal.push(cur)
        if(cur>pivotEle) bigger.push(cur)
    }
};


```


### merge sort

寫法千百種，重點是，你不要連 getMin and MergeArr 都不會自己弄出來 XD
```ts fold

function sortArray(nums: number[]) {
    return mergeSort(nums)
    // return quickSort(nums)
}

function mergeSort(nums) {
    if (nums.length <= 1) {
        return nums
    }

    let m = Math.floor(nums.length / 2)
    let firstHalf = nums.slice(0, m)
    let secondHalf = nums.slice(m)

    firstHalf = mergeSort(firstHalf)
    secondHalf = mergeSort(secondHalf)

    return merge2Arr(firstHalf, secondHalf)
}


function merge2Arr(q1, q2) {
    let res = []
    while (q1.length > 0 && q2.length > 0) {
     
        const minVal = findMin(q1, q2)
    
        res.push(minVal)
    }

    if (q1.length !== 0) {
        res = res.concat(q1)
    }

    if (q2.length !== 0) {
        res = res.concat(q2)
    }
    return res
}

function findMin(q1, q2) {
    let a1_val = q1[0]
    let a2_val = q2[0]
    if (a1_val <= a2_val) {
        return q1.shift()
    } else {
        return q2.shift()
    }
}


/**

一直切一半
最後組起來

 */








```


錯誤記錄: 一個陷入局部最優的好例子
下面的 code 的錯誤都是很可笑的
除非你放空，不然你只要有檢查到就可以發現
為何我卻沒有發現?
因為我陷入 局部最優
我一直覺得是 上面那塊有問題，我一直沒有往下檢查到其他地方
通常一直找不到問題，很大時候就是很 silly 的 mistake
這時候，應該要全部都要看，小心陷入局部最優
```js fold
var sortArray = function (nums) {
    return mergeSort(nums)
};

function mergeSort(nums) {
    if (nums.length < 2) {
        return nums
    }

    let m = Math.floor(nums.length / 2)
    let first = nums.slice(0, m)
    let second = nums.slice(m)
    
    first = mergeSort(first)
    second = mergeSort(second)
    return mergeAndSort(first, second)

}

function mergeAndSort(l1, l2) {
    let res = []
    while (l1.length > 0 && l2.length > 0) {
        let smallest = findSmallest(l1, l2)
        res.push(smallest)
    }

    if (l1.length > 0) {
        res = res.concat(l1)
    }

    if (l2.length > 0) {
        res = res.concat(l2)
    }
    
     // return res  // 忘記加

}

function findSmallest(l1, l2) {
    let a1 = l1[0]   //  let a1 = l1[1]  // <-- 寫錯
    let a2 = l2[0]   //  let a2 = l2[1]
    if (a1 <= a2) {
        return l1.shift()
    } else {
        return l2.shift()
    }
}
```




### selection sort
```ts fold

/**
 * Selection sort:
 * 透過每一次，都把最小的 element 放到最前面
 */
class SelectionSort {
  /**
   * 1. 找到最小的 element 的 index
   * 2. 移動到最前面
   * 3. 重新 selection sort 剩下的 N-1 個 element (不會觸碰最前面的 element)
   */
  static sort(arr) {
    /**
     * 我們想要使用 recursive solution, 但是這個問題有一個 constraint
     * 必須不要增加 sort 的 parameters!
     * 但是我們需要一個 index 或是 something 來幫助我們在 sort 的時候動態的縮小 scope!
     * Solution? -> use a helper method! -> sortFromStart
     */
    this.sortFromStart(arr, 0)
  }

  static sortFromStart(arr, start) {
    // Reach the last one, return
    if (start === arr.length - 1) return

    const smallestIdx = this.getSmallestIdxOfElementFromStart(arr, start)

    // 找到最小 element 的 index, 移動到最前面, 這邊的最前面是指目前要 sort 的 front element
    this.swap(arr, start, smallestIdx)

    this.sortFromStart(arr, start + 1) // 往前移動, 不影響已經 sort 好的 front element(s)
  }

  static getSmallestIdxOfElementFromStart(arr, start) {
    let smallestIdx = start

    // go thru all element and to find the smallest one
    for (let i = start; i < arr.length; i++) {
      if (this.isXSmallerThanY(arr[i], arr[smallestIdx])) {
        smallestIdx = i
      }
    }

    return smallestIdx
  }

  static swap(arr, a, b) {
    const tmp = arr[a]
    arr[a] = arr[b]
    arr[b] = tmp
  }

  static isXSmallerThanY(x, y) {
    return x.localeCompare(y) < 0
  }
}

// Example usage:
const arr = ['banana', 'apple', 'cherry', 'date']
SelectionSort.sort(arr)
console.log(arr) // Output: ["apple", "banana", "cherry", "date"]


```