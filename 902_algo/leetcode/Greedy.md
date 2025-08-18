

### [55. Jump Game](https://leetcode.com/problems/jump-game/)
一開在 first idx,  means 0 idx
val 代表你可以 最大 jump 的 距離
如果可以最後 arrive last idx (length-1) → True  
o.w. → False

![[IMG-Greedy-20250810200102686.png]]

```md

init pot
|
V
2 3 1 1 4


0 1 2 3 4
2 3 1 1 4
x

0 1 2 3 4
2 3 1 1 4
        x




0 1 2 3 4
3 2 1 0 4

```

```ts fold

function canJump(nums) {
  // 從最後一個 idx 開始，判斷是否可以跳到第一個的 idx
  let curIdx = nums.length - 1

  // why -2? 
  // 這邊的觀念是 如果目前在的這個 i + nums[i] 大於等於 curIdx, 表示就可以跳的到 curIdx, 然後我們往前走
  // 因此我們要開始判斷的起點是 nums.length - 2
  for (let i = nums.length - 2; i >= 0; i--) {
    if (i + nums[i] >= curIdx) {
      curIdx = i   // 如果可以跳的到，用 i 來往前移動, so update curIdx to i
    }
  }

  if (curIdx === 0) return true
  else return false
}


```

### [45. Jump Game II](https://leetcode.com/problems/jump-game-ii/)
從 0 idx 出發，走到 last idx 的最小 jump數
arr 值就是你在每一個 idx 上可以跳的 max jump 距離
![[IMG-Greedy-20250810200106924.png]]


![[IMG-Greedy-20250810200110032.png]]


```ts fold

/**
 * 類似 BFS 的感覺
 *
 *  2 3 1 1 4
 *  l
 *  r
 *
 *
 *  2 3 1   1 4 4  5  2  3
 *    l r
 *        newL    newR
 *
 * newL = r+1
 * newR = max(newR, nums[i] + i)
 *
 */
function jump(nums) {
  let l = 0
  let r = 0
  let res = 0

  // why -1? 因為如果當 r 抵達最後一個 的時候, 就是已經抵達。如果你再進去跑，res會多算一個
  // 另外你下一輪一定都是可以抵達或超過最後一個
  while (r < nums.length - 1) {

    let newR = -Infinity

    // find newR, 在 原本的 l 到 r 的區間內
    for (let i = l; i <= r; i++) {
      newR = Math.max(newR, nums[i] + i)
    }

    // update to new l and r
    l = r + 1
    r = newR
    res++
  }

  return res
}



```







other 
- [**134. Gas Station**](https://leetcode.com/problems/gas-station/)
- [**846. Hand of Straights**](https://leetcode.com/problems/hand-of-straights/)
- [**1899. Merge Triplets to Form Target Triplet**](https://leetcode.com/problems/merge-triplets-to-form-target-triplet/)
- [**763. Partition Labels**](https://leetcode.com/problems/partition-labels/)
- [Maximum Length of Repeated Subarray - LeetCode](https://leetcode.com/problems/maximum-length-of-repeated-subarray/)