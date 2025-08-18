
## 總結
- meeting rooms: 是否可以參加所有的會議 return T/F
    - 最簡單的題型，就是判斷是否有重疊
- non-overlapping intervals: 要移除幾個區間，才可以讓給定的區間(s) 沒有重疊
    - 邏輯跟 meeting room 一樣，就只算移除區間的數量
- merge intervals: 把重疊的區間(s) 合併，返回新的 intervals
    - 大架構類似基本題
    - 會透過 update prev.endAt 的方式處理重疊的情況
    - 會建立一個 res 來推入沒重疊的 interval
	    - 沒重疊的 interval會包括：本來就沒重疊interval，和已經被 update 後因此沒重疊的 interval
- meeting room II: 要滿足這些  intervals 時間區間需要多少會議室
    - 大架構類似基本題
    - 但是這題比較特別，需要用兩個 sortBy start and end 的兩個 new intervals 來幫忙判斷
    - 要從 0 開始判斷，因為你可能第一個會議室就會衝突
- insert interval: 提供一個 new interval, 插入 給定 intervals, 維持沒有重疊的 intervals
    - 大架構跟上面的題目不太一樣：需要用 3個 while loop 的判定，插入 new interval
    - 會建立一個 res 來推入
    - 要稍微記住如何判斷 3 個 while loop 的條件




## 題目
### [920 · Meeting Rooms](https://www.lintcode.com/problem/920/)
![[IMG-Intervals-20250810200102704.png]]

```ts fold


// 直接把 prev 放在裡面loop 即可

export class Solution {
  /**
   * @param intervals: an array of meeting time intervals
   * @return: if a person could attend all meetings
   */
  canAttendMeetings(intervals) {
    intervals.sort((a,b)=>a.start-b.start)
    for (let i = 1; i < intervals.length; i++) {
      let prev = intervals[i-1]
      let cur = intervals[i]
      if(prev.end > cur.start) return false
    }
    return true
  }
}




//////////////////








export class Solution {
  /**
   * @param intervals: an array of meeting time intervals
   * @return: if a person could attend all meetings
   */
  canAttendMeetings(intervals) {
    intervals.sort((a, b) => a.start - b.start); // <-- 這邊寫錯了，沒看清楚題目，不是 [start, end], 是放在 object 裡面

    let prev = intervals[0]
    for (let i = 1; i < intervals.length; i++) {   // 從 1 開始判斷
      const cur = intervals[i];

      if (cur.start < prev.end) {
        return false;         // 有重疊直接 return 掉了
      } else {
        prev = intervals[i]   // 沒有重疊要 update prev
      }
    }

    return true;
  }
}

```








### [919 · Meeting Rooms II](https://www.lintcode.com/problem/919/)
要滿足這些  intervals 時間區間需要多少會議室

![[IMG-Intervals-20250810200106955.png|786]]


1. sort by start and end, why??
	2. 這個題目的核心邏輯是：
		1. 檢查**當一個會議開始時，是否另一個會議已經結束了**
		2. 如果沒有，則需要一個新的會議室
		3. 如果有，那就不需要新的會議室，繼續往下走
			1. 繼續往下走 -> sortByStart 由 for-loop控制
			2. 繼續往下走 -> sortByEnd 由 外部 idx 控制
2. 知道為何沒有重疊時不需要 endIdx ++
```ts fold

export class Solution {
  /**
   * @param intervals: an array of meeting time intervals
   * @return: the minimum number of conference rooms required
   */
  minMeetingRooms(intervals) {
    if (intervals.length === 1) return 1;

    let intervalsSortByStart = Array.from(intervals).sort((a, b)=>a.start-b.start)
    let intervalsSortByEnd = Array.from(intervals).sort((a, b)=>a.end-b.end)
      
      
    let res = 0;
    let endIdx = 0;

    /**
     * 這一題其他題不同喔，要從 0 開始判斷
     * 因為你可能第一個會議室就會衝突，需要 ++ 了
     */
    for (let i = 0; i < intervals.length; i++) {
      const curStart = intervalsSortByStart[i].start;
      const pevEnd = intervalsSortByEnd[endIdx].end;

      /**
       * 如果 pevEnd > curStart 就是衝突了, need more meeting room
       * 如果沒有衝突， start 透過上面的 increment, endIdx 要自己 ++, 因為 init at outside
       * 
       * 為何如果有衝突就不需要 endIdx++ 呢？
       * 因為有可能 current 跟下一個 interval 還是衝突的，不知道，那要讓下一個 iteration 去判斷
       * 如果沒有衝突，表示當下這個 interval, 不管是 sort by start 和 sort by end 這兩個 intervals都是沒衝突的
       * 因此 endIdx 就可以++
       */
      
      if (pevEnd > curStart) {
        res++;
      } else {
        endIdx++;
      }
    }
    return res;
  }
}


```




short version
```js fold  
export class Solution {
  minMeetingRooms(intervals) {
    if (intervals.length === 1) return 1;

    let intervalsStart = Array.from(intervals).sort((a, b)=>a.start-b.start)
    let intervalsEnd = Array.from(intervals).sort((a, b)=>a.end-b.end)

    let endIdx = 0;
    let res = 0

    for(let i = 0; i<intervals.length; i++){
      
      let cur = intervalsStart[i]
      let prev = intervalsEnd[endIdx]

      if(prev.end > cur.start){
        res++
      } else {
        endIdx++
        
      }
    }

    return res 
  }

}
```


- sort by start and end, why??  
- 這個題目的核心邏輯是：  
	- 檢查**當一個會議開始時，是否另一個會議已經結束了**  -> 因此需要  sortByStart and sortByEnd 兩個 intervals
		- 如果沒有，則需要一個新的會議室  
		- 如果有，那就不需要新的會議室，繼續往下走  
	- 繼續往下走 -> sortByStart 由 for-loop控制  
	- 繼續往下走 -> sortByEnd 由 外部 idx 控制  
	- 為何有衝突時不需要 endIdx ++  ?
		- 因為當下的 interval 要繼續跟下一個 interval 確認是否有衝突



錯誤記錄
```js

// this is wrong, 因為你會 mutate 原本的 arr
// let intervalsStart = intervals.sort((a, b) => a.start - b.start)
// let intervalsEnd = intervals.sort((a, b) => a.end - b.end)


// 這樣才不會mutate原本的，你會 copy 一份出來再去 sort
let intervalsStart = Array.from(intervals).sort((a, b)=>a.start-b.start)
let intervalsEnd = Array.from(intervals).sort((a, b)=>a.end-b.end)

```



錯誤版本
```js fold

  minMeetingRooms(intervals) {
    let intervalsStart = intervals.sort((a, b) => a.start - b.start)
    let intervalsEnd = intervals.sort((a, b) => a.end - b.end)

    let prev = intervalsEnd[0]
    let res = 0

    for(let i = 0; i<intervalsStart.length; i++){
      let cur = intervalsStart[i]
      if(prev.end > cur.start){
        res++
      } else {
        prev = intervalsEnd[i] // <-- 這邊 idx 要自是另一個, 不能用 i, 因此上面的 code 也要調整
      }
    }

    return res 
  }

```


---

### [435. Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)
給定 區間 arr (沒有說 sorted)
這個區間arr 裡面有可能會有 overlapping的情況 (邊界一樣 不算overlap,  see ex3)
請求出，最少要移除幾個，才可以讓 區間arr non-overlapping

![[IMG-Intervals-20250810200110379.png]]


```ts fold

//  less comment code 2024_09_28-09_40_42
function eraseOverlapIntervals(intervals: number[][]): number {
    // 每次遇到重疊區間時，我們總是選擇移除結束時間較晚的區間。(so we sort by endAt)
    // 這是因為結束時間較晚的區間可能會與後續的區間重疊，移除它可以為後續區間騰出更多空間
    // 從而減少需要移除的總區間數量
    intervals.sort((a,b)=> a[1]-b[1]) 

    let prev = intervals[0]
    let res = 0

    for (let i = 1; i < intervals.length; i++) {
        let cur = intervals[i]
        if(prev[1]>cur[0]){
            res++
        } else {
            prev = intervals[i]   // 如果沒衝突才會update prev, 不然下一個iter繼續檢查同一個
        }
    }
    
    return res
};





function eraseOverlapIntervals(intervals: number[][]): number {
  // No overlaps possible with one or zero intervals
  if (intervals.length <= 1) return 0;

  // Sort intervals by their "end times" , why end time? 看上面解釋
  intervals.sort((a, b) => a[1] - b[1]);

  let removeCount = 0; // Number of intervals to remove

  // 記錄 上一個 interval 的 end time, 我們這樣才可以判斷是否下一個是否重疊
  let prevEnd = intervals[0][1]; 

  // Iterate through the sorted intervals
  // 從第二個 interval 開始, 因此第一個沒有上一個
  // 我們只需要從第二個 interval 開始, 檢查有沒有跟上一個重疊
  for (let i = 1; i < intervals.length; i++) {
    const curStart = intervals[i][0];

    // 3. 如果現在開始大於上一個結束, 這裡有重疊
    if (curStart < prevEnd) {
      // Overlap detected, removeCount++
      removeCount++;
    } else {
      // No overlap, update prevEnd to the current interval's end
      prevEnd = intervals[i][1];
    }
  }

  return removeCount;
}

```


---

### [56. Merge Intervals](https://leetcode.com/problems/merge-intervals/)

給定 區間arr
把有 overlapping 的 區間 進行合併, 返回 合併後的 區間arr
 邊界一樣的算 overlapping like ex2


![[IMG-Intervals-20250810200112816.png]]

```ts fold


// new comment
function merge(intervals: number[][]): number[][] {

    intervals.sort((a, b) => a[0] - b[0])

    let prev = intervals[0]
    
	// 建立一個新的來放
	// 第0個要先塞進去, 因為下面從 i = 1 開始
    let res = [prev]
    
    

    for (let i = 1; i < intervals.length; i++) {
        let cur = intervals[i]
		
		// 如果重疊
        if (prev[1] >= cur[0]) {  
            // 要 extned, extned onto prev or cur, 看 example 1, 應該是 prev 的尾巴
            prev[1] = Math.max(cur[1], prev[1]) 
            // 這邊沒有 push 到 res, 因此有可能持續被 extend, 要 extend until no 重疊，下面再推入res
            // 然後這邊也會 update 到 res 裡面的 prev, 因為這邊都是 ptr 的 type, 這也是我們預期的
        } else {
            // 上面一直 update in place until no 重疊
            // 然後這邊再推入，也會推入已經被 update過了然後已經沒有重疊的
            res.push(intervals[i])
            
            // update prev ptr, 因為 prev 的 increment is maintain outside this for-loop
            prev = intervals[i]
        }
    }
    return res
};




// 錯誤記錄
// 不確定是否要建立一個新的 res 來返回，還是要 update current one -> 其實應該是都可以，就看怎麼做你比較好實作而已
function merge(intervals: number[][]): number[][] {
    if(intervals.length===1) return intervals
    
    let prev = intervals[0]
    for (let i = 1; i < intervals.length; i++) {
        let cur = intervals[i]
        if (prev[1] >= cur[0]) {
            prev[1] = Math.max(cur[1], prev[1])

        } 
    }
    return intervals
};




// 錯誤記錄
function merge(intervals: number[][]): number[][] {

    intervals.sort((a, b) => a[0] - b[0])

    let res = [intervals[0]]
    let prev = intervals[0]



    for (let i = 1; i < intervals.length; i++) {
        let cur = intervals[i]
        if (prev[1] >= cur[0]) {
            res[i-1] = [Math.min(cur[0], prev[0]), Math.max(cur[1], prev[1])]
            // 我卡在不知道要如何 merge 多個 into one, 上面的 code 只會 merge 一次
            // 然後後續就不會持續merge 到  第一個了
        } else {
            res.push(intervals[i])
            prev = intervals[i]
        }
    }
    return res
};











// 另一個版本，邏輯一模一樣，只是用比較 concise 的 variables
function merge(intervals: number[][]): number[][] {
    // 題目沒有說有 sort 好，我們下面的操作 based on sorted at start time
    intervals.sort((a,b)=>a[0]-b[0])

    // 我們需要持續追蹤上一個interval, 才可以知道是否有 overlapping
    let prev = intervals[0]
    // setup result and 把第一個放進去
    let res = [prev]

    for (let i = 1; i < intervals.length; i++) {
        let cur = intervals[i]

        if (prev[1] >= cur[0]) {
            // why this line error?
            // intervals[i-1][1] = Math.max(cur[1], intervals[i-1][1])
            // 不一定剛好是 i-1是 prev, prev 只有進入下面條件也才會 update喔
            // 也就是說，如果 i 走了好幾輪，都沒有 else, 那i當然不是i-1
            prev[1] = Math.max(cur[1], prev[1])
        } else {
            // 如果沒有 overlapping, 推入結果
            res.push(cur)
            // update previousInterval
            prev = intervals[i]
        }
    }
    return res

};



// 第一版，完整 comment 和清楚的變數 naming
function merge(intervals: number[][]): number[][] {
  // No need to merge if there's only one or zero intervals
  if (intervals.length <= 1) return intervals;

  // 1. Sort intervals by their start times
  // PS: 題目沒有說有 sort 好，我們下面的操作 based on sorted at start time
  intervals.sort((a, b) => a[0] - b[0]);

  // 我們會建立一個新的 mergedIntervals 放結果 and 把第一個放進去
  const mergedIntervals: number[][] = [intervals[0]];
  // 我們需要持續追蹤上一個interval, 才可以知道是否有 overlapping
  let previousInterval = mergedIntervals[0];

  for (let i = 1; i < intervals.length; i++) {
    const currentInterval = intervals[i];

    // 如果 prev 的 end >= cur 的 start, 那麼 prev 跟 cur 有 overlapping

    if (previousInterval[1] >= currentInterval[0]) {
      // 如果有 overlapping, 我們 update prev 的 end by 取 兩者 end 的 Max
      previousInterval[1] = Math.max(previousInterval[1], currentInterval[1]);
    } else {
      // 如果沒有 overlapping, 推入結果
      mergedIntervals.push(currentInterval);
      // update previousInterval
      previousInterval = currentInterval;
    }
  }

  return mergedIntervals;
}






```



---

### [57. Insert Interval](https://leetcode.com/problems/insert-interval/)
給定 non-overlapping 的, sorted by startAt 的 區間 arr

和
一個要求插入的區間
把新區間 insert into 區間 arr
結果需要 維持 non-overlapping的 區間 arr
如果需要 merge 就 merge

![[IMG-Intervals-20250810200113919.png]]


[2,5] 跟第一個 區間overlap → 因此第一個區間變成 1, 5
![[IMG-Intervals-20250810200114667.png]]

這一題的只要邊界一樣就算是 overlap, like 下面的 8 of [4,8]  對應到 8 of [8,10]
[4,8] 橫跨/overlap  [3,5] , [6,7], [8,10] 三個 arr
因此 these 3 區間 直接變成一個新的區間 3, 10

![[IMG-Intervals-20250810200115114.png]]


```ts fold

function insert(intervals: number[][], newInterval: number[]): number[][] {
    if(intervals.length===0) return [newInterval]

    intervals.sort((a, b) => a[0] - b[0])
    let res = []

    for (let i = 0; i < intervals.length; i++) {

        // 只要 i at front -> 先推入
		//   i -------
		//              new ------
        while (i < intervals.length && intervals[i][1] < newInterval[0]) {
            res.push(intervals[i])
            i++
        }
				
		// 這邊要用 [i][0], i 前面來進行判斷，去看有沒有重疊 
		// why?
		// 因為這樣看的角度是： new 在前面, i在後面
		//        i ------
		//   new -----
		// 為何不要用 i 在後面的角度去看?
		//   i -------
		//      new ------
		// 因為這個情況上面那個 while loop 已經處理掉了
        while (i < intervals.length && intervals[i][0] <= newInterval[1]) {   // 這題邊界也是重疊, 所以有等號
            newInterval[0] = Math.min(intervals[i][0], newInterval[0])
            newInterval[1] = Math.max(intervals[i][1], newInterval[1])
            i++ 
            // 先不推， push 放 while 外面，因為可能要處理很多個
        }
        res.push(newInterval)


		// 剩下的推進去
		// why 下面沒有等號? 因為i === intervals.length 時，intervals[i] 就 idx access err 囉
        while (i < intervals.length) {
            res.push(intervals[i])
            i++
        }
    }
    return res
};

```




另一個是兩組 intervals 求 重疊, 比較偏向用 ptr -> [[Two Pointer#[986 Interval List Intersections ](https //leetcode.com/problems/interval-list-intersections/description/)]]