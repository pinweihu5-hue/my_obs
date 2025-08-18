


## 題目


### [1.two sum](https://leetcode.com/problems/two-sum/)

```js fold

/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
    const valToIdx = new Map();
    for(let i =0; i<nums.length; i++){
        const left = target - nums[i]
        if(valToIdx.has(left)){
            return [valToIdx.get(left),i]
        }

        valToIdx.set(nums[i], i)
    }
    
};


```


### [217. Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)

```ts fold

/**
 * @param {number[]} nums
 * @return {boolean}
 */
var containsDuplicate = function(nums) {
    const s = new Set()
    for(let i =0; i<nums.length; i++){
        if(s.has(nums[i])) return true
        s.add(nums[i]) 
    }
    return false
    
};

```


### [242. Valid Anagram](https://leetcode.com/problems/valid-anagram/)

```js fold

/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
var isAnagram = function(s, t) {
    
    // return way1(s, t)
    return way2(s, t)
    
    
    
//     function way1(s, t){
//         return s.split("").sort().join("") === t.split("").sort().join("")
//     }
    
    function way2(s, t){
        // create Counter Map
        let o1 = {}
        let o2 = {}
        for (let i =0; i<s.length; i++){
            o1[s[i]] = (o1[s[i]] || 0) + 1
        }
        for (let i =0; i<t.length; i++){
            o2[t[i]] = (o2[t[i]] || 0) + 1
        }
        
        return shallowCompareObj(o1, o2)
        
    }
    
    function shallowCompareObj(o1, o2){
        return Object.keys(o1).length === Object.keys(o2).length &&
            Object.keys(o1).every( o1Key => o1[o1Key] === o2[o1Key])
    }
   
};

```


### [ 219. Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/?envType=study-plan-v2&envId=top-interview-150)


直接用 hash map 記錄 val and idx, 用題目給的條件檢查
```js fold

var containsNearbyDuplicate = function (nums, k) {

    let m1 = {}

    for (let i = 0; i < nums.length; i++) {
        let ele = nums[i]
        if (ele in m1 && Math.abs(i - m1[ele]) <= k) {
            return true
        }
        m1[ele] = i
    }

    return false

};

```


下面這個解法比較繞
```ts fold


/**

只要有兩個 idx
一樣
且 相對位置 <= k
就是 True


ex1:

0  1 2 3   
i      j
[1,2,3,1]

3-0<=k=3

ex2:

     
 0 1 2 3
[1,0,1,1]
|2-3| <=k = 1


ex3
 
 0 1 2 3 4 5
[1,2,3,1,2,3]
 x     x
   x     x
     x     x
? <=k=2
都大於 2 => False


 */
function containsNearbyDuplicate(nums: number[], k: number): boolean {
    let obj = setupValToIdxMap(nums)
    let val = Object.values(obj) as number[][] // 用 TS, 這邊無法判定下面直接 compiler err, 需要 cast XD
    // [ [ 0, 3 ], [ 1, 4 ], [ 2, 5 ] ]


    // 兩個兩個檢查
    for (let ele of val) {
        let i = 0
        let j = i + 1
        while (j <= ele.length - 1) {
            const dif = Math.abs(ele[i] - ele[j])
            if (dif <= k) {
                return true
            }
            i++
            j++
        }
    }

    return false
};




/**

建立 hash map 如下

idx 0 1 2 3 4 5 6
   [1,2,3,1,2,3,3]
{
    0: 
    1: [0,2]
    2: [1,4]
    3: [2,5,6]
}
 */
function setupValToIdxMap(arr) {
    let res = {}
    for (let i = 0; i < arr.length; i++) {
        let ele = arr[i]
        if (!(ele in res)) {
            res[ele] = []
            res[ele].push(i)
        } else {
            res[ele].push(i)
        }
    }
    return res
}

```



---


### [128. Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/)


```js fold

/**
 * @param {number[]} nums
 * @return {number}
 */
var longestConsecutive = function(nums) {
	
	// covert array into set
    let s1 = new Set(nums)
    
		// a global max longest to update in below loop
    let res = 0

    for(let ele of nums){
        // check if we have the left neighbor
        // to know if it's the begin of the sequence
        if (!s1.has(ele-1)){
            let count = 0
            // then we can keep counting how long it is
            while (s1.has(ele+count)){
                count++
            }
			// update the longest after the while 
            res = Math.max(res, count)
        }
    }
    return res
};


下面寫法也可以，類似寫法，同樣邏輯

var longestConsecutive = function (nums) {
    if(nums.length===0) return 0
    // if(nums.length===1) return 1

    let set = new Set(nums)

    let res = 1
    for (let ele of nums) {
        if (!set.has(ele - 1)) {
            let count = 0
            while (set.has(ele)) {
                count++
                res = Math.max(res, count)
                ele++
            }
        }
    }
    return res
};


```

TIL
- 先找出當下這個element 是 seq 的第一個，然後再用 while loop 一個一個 count++
	- 全部都塞在 set, 如果 element - 1 not in the set -> 就是第一個
- 關鍵是找頭，然後用 while loop 往後找 ++
	- 不是找到頭以外的，然後 while -- 這樣會有很多要找


錯誤記錄:
這邊如果找左邊有的更小的，反過來 count，會有很多計算 -> TLE
[100,4,200,1,3,2]
找左邊的更小的
4, 3, 2, 1
3, 2, 1
2, 1
會有很多重複的 iteration in the while loop
```js fold

// 錯誤記錄
var longestConsecutive = function (nums) {

    let set = new Set(nums)

    let res = 0
    let count = 0
    for (let i = 0; i < nums.length; i++) {
        let ele = nums[i]
        count = 0

        while (set.has(ele)) {
            count++
            res = Math.max(res, count)
            ele--
        }

    }
    return res
};

```



```js fold

/**
如果有上一個，去set 找，算 count
-> TLE
這樣會 TLE 是因為
[100,4,200,1,3,2]
這邊你會檢查 4,1,3,2
 */
// var longestConsecutive = function(nums) {
//     if(nums.length===0) return 0

//     let s1 = new Set(nums)
//     let res = 0

//     for(let ele of nums){

//         let ele2 = ele - 1
//         let count = 0

//         while(s1.has(ele2)){
//             count++
//             res = Math.max(res, count)
//             ele2--
//         }
//     }

//     return res+1

// };

// 如果有上一個，表示不是頭，放棄
// 這個比較快，因為很多都不會進入 while，你只會找到頭，然後去 while
// 因為一個 Longest Consecutive Sequence 只會有一個頭
var longestConsecutive = function (nums) {
    if (nums.length === 0) return 0

    let s1 = new Set(nums)
    let res = 0

    for (let ele of nums) {

        let count = 0

        if (s1.has(ele-1)) continue
        else {
            while (s1.has(ele)) {
                count++
                res = Math.max(res, count)
                ele++
            }
        }


    }

    return res

};

```



---

### [187. Repeated DNA Sequences](https://leetcode.com/problems/repeated-dna-sequences/)
```ts fold

function findRepeatedDnaSequences(s: string): string[] {
    
    let l = 0
    let r = 9
    let res = []
    
    // we use set to help us idenitfy the repeat pattern
    let s1 = new Set()
    
    // as long as the last char is below s.length
    while (r < s.length) {
        // get the 10 string using slice
        // r+1 since second arg is non-exclusive, need to +1 to for 10 char
        let seg = s.slice(l, r + 1)
        
        // if the 10 string seg got repeated one
        // push to the res (and no need to add to s1, cannot add into set anyway)
        if(s1.has(seg)){
            res.push(seg)
        } else {
            // o.w keep adding to set
            s1.add(seg)
        }

        l += 1
        r += 1
    }
    
    // get only the unqie one
    // in s = "AAAAAAAAAAAAA" case, we will have res as s =
    // AAAAAAAAAAAAA, AAAAAAAAAAAAA, AAAAAAAAAAAAA
    // and we only need to get one for result
    // (since the above algo give us all of the repeated string, not only one )
    let res2 = [...new Set(res)]
    return res2
};

```

### [49. Group Anagrams](https://leetcode.com/problems/group-anagrams/)
```ts fold

var groupAnagrams = function(strs) {
    
    let m1 = {}
    for (const w of strs){
        let key = w.split('').sort().join()
        if(!(key in m1)){
            m1[key] = [w]
        } else {
            m1[key] = m1[key].concat(w)
        }
    }
    
    return Object.values(m1)
    
    
}

```



### [36. Valid Sudoku](https://leetcode.com/problems/valid-sudoku/)
```ts fold

var isValidSudoku = function (grid) {
    
    let s1 = new Set()
    
    for (let r=0;r<9;r++){
        for (let c =0 ;c<9;c++){

            if(grid[r][c] === ".") continue
						// 關鍵就是用字串"sq, r c"建立出三種不同的檢查需要
            // 另外透過 Math.floor(r/3) + Math.floor(c/3) 和 動態的 r 和動態的 c 建立出每一個只包括1-9的子set
            // 重覆就是 false

            let sqVisited = "Sq" + Math.floor(r/3) + Math.floor(c/3) + grid[r][c]
            let rVisited = "r" + r + grid[r][c]
            let cVisited = "c" + c + grid[r][c]
            
            if(s1.has(sqVisited) || s1.has(rVisited) || s1.has(cVisited)) return false
            
            s1.add(sqVisited)
            s1.add(rVisited)
            s1.add(cVisited)
            
        }   
    }
    
    return true

};

```

### [Array Nesting - LeetCode](https://leetcode.com/problems/array-nesting/description/)

可以從每一個idx 開始
![[IMG-hash and Set-20250810200102713.png]]


js code, same logic, 比較精簡
```js fold

var arrayNesting = function (nums) {
    let s1 = new Set()
    let res = 0
    for (let i = 0; i < nums.length; i++) {
        res = Math.max(res, getMaxLen(i, nums, s1))
    }
    return res
};

function getMaxLen(startIdx, nums, set) {
    let idx = startIdx
    let count = 0
    while (true) {
        idx = nums[idx]
        if (set.has(idx)) break

        count++
        set.add(idx)
    }
    return count
}

```


```ts fold

function arrayNesting(nums: number[]) {
    let s1 = new Set()
    let res = -Infinity
    // 另一層 for-loop, try out all starting idx
    // 這邊的優化是，我們不需要每一個 inner loop 都有一個自己的set
    // 我們可以全部共用一個 set
    // why?
    // 每一個獨立的 set, 你會有很多 scan 都是重複的
    for (let i = 0; i < nums.length; i++) {
        const temp= arrayNestingHelper(nums, i, s1)
        res = Math.max(temp, res)
    }
    return res
};

// 一個 for loop 建, 用 set 協助判斷
function arrayNestingHelper(nums: number[], idx: number, set) {
    let res = []
    let j = idx
    // let set = new Set()
    for (let i = idx; i < nums.length; i++) {
        let ele = nums[j]
        if(set.has(ele)) break

        set.add(ele)
        res.push(ele)
        
        j = ele
    }
    return res.length
};

```



另一個解法, 類似解
```js fold

var arrayNesting = function (nums) {
    let res = 0
    let s1 = new Set()

    for (let i = 0; i < nums.length; i++) {
        res = Math.max(res, findLongest(i, s1, nums))
    }

    return res
};


function findLongest(startIdx, s1, nums) {
    let idx = startIdx
    let count = 0
    while (!s1.has(nums[idx])) {
        idx = nums[idx]
        s1.add(idx)
        count++
    }
    return count
}

```


### rolling freq obj
### [567. Permutation in String](https://leetcode.com/problems/permutation-in-string/)
```js fold

function checkInclusion(s1, s2) {
    let s1FreqObj = toFreqObj(s1)

  /**
   *
   * why s2.slice(0, s1.length)?
   *
   * 01234567
   * ab
   * eidbaooo
   * ~~ <--- window  (0, s1.length=2)
   */
    let winFreqObj = toFreqObj(s2.slice(0, s1.length))

    if (isTheSame(s1FreqObj, winFreqObj)) return true // // 開始前就先確認是否已經找到了

    for (let i = 0; i < s2.length - s1.length; i++) {
        let eleToDelete = s2[i]

        //如果有了就砍掉，不然就--
        if (winFreqObj[eleToDelete] === 1) {
            delete winFreqObj[eleToDelete]
        } else {
            winFreqObj[eleToDelete]--
        }

        let eleToadd = s2[i + s1.length]
        // 如果有了就++, 不然 init as 1
        if (!(eleToadd in winFreqObj)) {
            winFreqObj[eleToadd] = 1
        } else {
            winFreqObj[eleToadd]++
        }
        if (isTheSame(s1FreqObj, winFreqObj)) return true
    }
    return false
}


function isTheSame(o1, o2) {
    if (Object.keys(o1).length !== Object.keys(o2).length) return false
    return Object.keys(o1).every(o1Key => o1[o1Key] === o2[o1Key])
}

function toFreqObj(s) {
    let res = {}
    for (let char of s) {
        res[char] = res[char] ? res[char] + 1 : 1
    }
    return res
}



```

### [438. Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)
```ts fold


function findAnagrams(s: string, p: string): number[] {
    let res = []

    let winObj = freqObj(s.slice(0, p.length))
    let tarObj = freqObj(p)

    if (isSameObj(winObj, tarObj) === true) res.push(0)

    for (let i = 0; i <= s.length - p.length; i++) {
        // remove char
        let char = s[i]
        if (winObj[char] === 1) {
            delete winObj[char]
        } else {
            winObj[char]--
        }

        // add char
        let newChar = s[i + p.length]
        if (newChar in winObj) {
            winObj[newChar]++
        } else {
            winObj[newChar] = 1
        }
        // check if the same
        if (isSameObj(winObj, tarObj) === true) {
            res.push(i + 1) // 這邊是看下一輪的，so i+1
        }
    }

    return res
};

function isSameObj(o1, o2) {
    if (Object.keys(o1).length !== Object.keys(o2).length) return false
    return Object.keys(o1).every(key => o1[key] === o2[key])
}

function freqObj(s) {
    let res = {}
    for (let char of s) {
        res[char] = res[char] ? res[char] + 1 : 1
    }
    return res
}

```



---


other see notion [Notion – The all-in-one workspace for your notes, tasks, wikis, and databases.](https://www.notion.so/nture4388/hashMap-Set-8e7aa2b995524d96901908e901d8f4f1?pvs=4#10b9df69750f80239cade2bb5c2090a4)