
## Backtracking VS DP
- 這邊需要全部都跑過， 因此這邊都沒有設定 cache 機制 來跳過 
- 這邊需要跑過，path concat, 然後推入 res, 結果會像是 `[[2,2,3],[7]]`
	- 不同於很多 DP 題都要求最大/小/組合數/最長/ min cost, max rob, T/F
- 基本題分成三種
	- 算 superset
	- 算排列
	- 算組合,和組合起來要剛好等於某個加總

## pattern 彙整
[Notion – The all-in-one workspace for your notes, tasks, wikis, and databases.](https://www.notion.so/nture4388/Backtracking-41a330b072b74e9daec50b575885d5e6?pvs=4#030e0b25c7ea481aa76cbea30f63acc7)


## 題目

### superset
### [78. Subsets](https://leetcode.com/problems/subsets/)
![[IMG-Backtracking-20250810200102678.png]]


```ts fold
function subsets(nums: number[]): number[][] {
    let res = []

    function dfs(ds, path) {
        res.push(path)

        for (let i = 0; i < ds.length; i++) {
            // ds.slice(0,i) why 不行?
            // ds[i] 就是指拿一個, ds.slice(0, i), 如果 i = 2, 你一次拿兩個
            // 把決策樹畫出來， path 本來就應該是一次只挑一個
            // dfs(ds.slice(i+1), path.concat(ds.slice(0,i)))
            dfs(ds.slice(i+1), path.concat(ds[i]))
        }
    }

    dfs(nums, [])
    return res
};
```
TIL:
- 不需要base case, 因為 ds.slice(i+1) 會一直讓 ds getting smaller and smaller。最後 ds 空了， for loop 也不會跑了，就停了。


![[IMG-Backtracking-20250810200106920.png]]

---

### [90. Subsets II](https://leetcode.com/problems/subsets-ii/)
![[IMG-Backtracking-20250810200109832.png]]
```ts fold
/**
 * @param {number[]} nums
 * @return {number[][]}
 */
var subsetsWithDup = function (nums) {
    nums.sort((a, b)=>a-b)

    res = []
    function dfs(ds, path) {
        
        res.push(path)

        for (let i = 0; i < ds.length; i++) {
            
            // i>0 才可以繼續下一個 &&後面的邏輯判斷，不然 i-1 會變成 -1
            // ds[i]==ds[i-1], 目前這個跟上一個一樣
            //if(i > 0 && ds[i]==ds[i-1]) {  
            //    continue
            //}
            
            // JS lang，其實也不需要 i > 0, 因為 JS ds[-1] is undefined, 然後 ds[i]==ds[i-1] -> 就是 false
						if (ds[i] === ds[i - 1]) continue
            dfs(ds.slice(i+1), path.concat(ds[i]))
        }
    }
    
    dfs(nums, [])

    return res
};
```


### 排列
### [46. Permutations](https://leetcode.com/problems/permutations/)
![[IMG-Backtracking-20250810200112424.png]]


ds 就是把 pick 掉的那個拔掉, pick 的那個給 path
python: ds[:i] + ds[i+1:]
JS:     ds.slice(0, i).concat(ds.slice(i+1))

Why?
對於每一個element, 1,2,3
我們可以先 pick 一個出來排, 剩下的就放著
下一輪再從剩下的拿一個出來排, 剩下的放著
以此類推不斷的遞迴
直到 ds 沒東西可以選
最後就是一個組合

用上面的邏輯針對每一個 idx 去操作, 就是全部的組合數

```ts fold
/**
 * @param {number[]} nums
 * @return {number[][]}
 */
var permute = function(nums) {
    const res = []

    function dfs(ds, path){

        if(ds.length===0){
            res.push(path) // 只要 leaves
        }

        for (let i = 0; i < ds.length; i++) {
            // 不包括i, 前面那一段until i(not include), 後面那一段從 i+1開
            // path 選 ds[i]
            dfs([...ds.slice(0, i), ...ds.slice(i+1)], path.concat(ds[i]))
        }

    }

    dfs(nums, [])
    return res;
};
```


### [47. Permutations II](https://leetcode.com/problems/permutations-ii/)
![[IMG-Backtracking-20250810200113767.png]]
```ts fold
/**
 * @param {number[]} nums
 * @return {number[][]}
 
 跟 permutation 一樣，只是多了處理 duplicate 的操作
 處理 duplicate操作的作法跟 subsetII一樣
 只要 i >= 1 && ds[i] === ds[i-1] 就跳過避免重複
  */
var permuteUnique = function(nums) {
    const res = []
    nums.sort((a, b)=>a-b)

    function dfs(ds, path){
        if(ds.length===0){
            res.push(path)
        }

        for (let i = 0; i < ds.length; i++) {
            
            if(i>=1 && ds[i] === ds[i-1]){
                continue
            }
            dfs([...ds.slice(0, i), ...ds.slice(i+1)], path.concat(ds[i]))
        }

    }



    dfs(nums, [])
    return res
    
};
```


### 組合

### [77. Combinations](https://leetcode.com/problems/combinations/)
![[IMG-Backtracking-20250810200114517.png]]

```ts fold
/**
 * @param {number} n
 * @param {number} k
 * @return {number[][]}
 */
var combine = function(n, k) {
    const res = []


    function dfs(ds, path){
        if(path.length === k){
            res.push(path)
            return
        }

        for (let i = 0; i < ds.length; i++) {
            dfs(ds.slice(i+1), path.concat(ds[i]))
        }
    }


    const initNum = []
    for (let i = 1; i <= n; i++) {initNum.push(i)}

    dfs(initNum, [])
    return res
    
};
```


疑問 why i+1 in `ds.slice(i+1)?`
![[IMG-Backtracking-20250810200114957.png]]
如果看題目的 output, `[[1,2],[1,3],[1,4],[2,3],[2,4],[3,4]]`
可以重複選阿, 重複選不是  `ds.slice(i)`

這邊的重複選，是透過for-loop 中，每一個 ele 都可以重複選, 感覺會像是下圖
不同顏色類似不同次的 iteration
![[IMG-Backtracking-20250810200115501.png|287]]
 
### [39. Combination Sum](https://leetcode.com/problems/combination-sum/)

和 II 不同，這題，candidate都是distinct且可以重覆被選取來
![[IMG-Backtracking-20250810200115808.png]]
每一輪，都減去 ds[i]
檢查是否可以 push to res。
檢查條件就是是否 target 減掉 ds[i]  PS: target 會隨著每一個 recur 一直被減掉
減到0 的那個時候的path 就找到了，可以推入 res

```ts fold

var combinationSum = function(candidates, target) {

    
    let res = []
    
    function dfs(ds, path, target){
        // overshot case, 下面用slice(i), 會一直全跑，需要有扣到負的 base case
        if(target<0) {
	        return
        }
        
        if(target === 0){ // 剛好命中才是我們要的, 要 return, 因為 ds.slice(i) 會一直跑下去
            res.push(path)
            return
        }
        
        
        // 這一個 for-loop 是依序想要把每一個 element 作為開始的減項
        // 因為誰作為第一個減項 matter, 類似如果 target是7, 如果第一個減項是 7, 那就馬上找到了
        for(let i=0;i<ds.length;i++){ 
            // 因為每一輪都可以選已經選過的，因此用 ds.slice(i), 這個 i 不會+1, 因此每一輪都會是0,1,2...
            // 可以想像成，最左邊的那個 dfs calls 會一直可以用 每一個 element 作為減項
            // 每一輪減掉 ds[i], 加入 path
            // target 減掉 ds[i])
            dfs(ds.slice(i), path.concat(ds[i]), target-ds[i])
        }
    }
    
    dfs(candidates, [], target)
    return res
   
    
};
```

### [40. Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)
![[IMG-Backtracking-20250810200115941.png]]
跟1的差異是
- candidate裡面的ele只可以被用一次
- candidate element會重覆。
![[IMG-Backtracking-20250810200116108.png]]

```ts fold
/**
 * @param {number[]} candidates
 * @param {number} target
 * @return {number[][]}

 element 會重複:
 一樣要 當 i>=1 && ds[i] === ds[i-1] 需要 skip



 不可重複使用:   
 本來是每一次 recur 都是 ds[i], 這邊改為 ds[i+1], 這樣下一次 recur call
 就會無法用到上一輪已經走過的減項
  */
var combinationSum2 = function(candidates, target) {

    candidates.sort((a,b)=>a-b)


    const res = []

    function dfs(ds, path, target){
       
        if(target <0 ) {   // overshot case <--- 錯誤記錄，忘記加上 overshot case -> TLE
            return
        }

        // 剛好達到我們要的 sum
        if(target === 0){
            res.push(path)
            return
        }

        for (let i = 0; i < ds.length; i++) {

            // 已經選過就跳過，避免重複
            if(i>=1 && ds[i] === ds[i-1]){
                continue
            }

            // 選過下一輪就不選，因此i需要+1
            dfs(ds.slice(i+1), path.concat(ds[i]),target-ds[i])
        }
    }
    dfs(candidates, [],target)
    return res
    
};
```



### [216. Combination Sum III](https://leetcode.com/problems/combination-sum-iii/)
- 跟 comb一樣， k 一樣是 combo size
- n 是 sum的結果 （跟 77 combo不同，77的n是到n這個數字都要包括在combo中），這題固定了，可以選的就是1到9

- 兩個條件
    - 加起來要剛好是 n
    - 每一組需要是 k 個

跟 77題 的差異就是多一個條件需要在 recur 中track

77題 只需要 track n

![[IMG-Backtracking-20250810200116270.png]]

```ts fold
/**
 * @param {number} k
 * @param {number} n
 * @return {number[][]}
 */
var combinationSum3 = function(k, n) {
    const res = []
    function dfs(ds, path, target){
        if(target <0){
            return // overshot
        }
        if(target ===0 && path.length===k){
            res.push(path)
            return
        }

        for (let i = 0; i < ds.length; i++) {
            // Each number is used at most once. -> so i+1
            dfs(ds.slice(i+1), path.concat(ds[i]), target - ds[i])
        }
    }
    const initNum = []
    // Only numbers 1 through 9 are used
    for (let i = 1; i <=9; i++) {initNum.push(i)}
    dfs(initNum, [], n)

    return res
    
};
```



### 其他

### [17. Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)
- input 是一個 digits in string
- 只會是 [2~9]  2 end is inclusive
- each digit 對應到電話上的那些 char, like 2→ “abc”, 3 → ”def”…etc
- 請給我 digits 的各種可能的組合
![[IMG-Backtracking-20250810200116432.png]]

不用拘泥於 ds 的思維

我們要的就是一個 idx 去走 dfs, 然後再把 digit → chars 然後去遞迴的組出很多 path+char 放進去 res
```ts fold

function letterCombinations(digits: string) {
    if (digits === "") return []

    let digitToChars = {
        "2": 'abc',
        "3": 'def',
        '4': 'ghi',
        '5': 'jkl',
        '6': 'mno',
        '7': 'pqrs',
        '8': 'tuv',
        '9': 'wxyz'
    }
    
    
    let res = []
    
    function dfs(i, path) {
        if (i === digits.length) {
            res.push(path)
            return
        }

      
        let digit = digits[i]
        let chars = digitToChars[digit]

        for (let char of chars) {
            dfs(i + 1, path + char)
        }

    }
    dfs(0, "")
    return res
};
```

### [131. Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/)

給一個 string s
把 s 切割，然後讓切完後的 substring 都是 pali
請返回所有可能的 pali substring

![[IMG-Backtracking-20250810200116634.png]]

```ts fold
function partition(s: string){

    let res = []

    function dfs(ds, path) {
        if (ds.length===0){
            res.push(path)
            return
        }

        for (let i = 0; i < ds.length; i++) {
            /**
         
        idx 0 12
            a|ab
            上面切這一刀
            左邊: path
            右邊: ds

            path  是 slice(0, i+1), i=0時 slice(0, 1) -> a
            ds    是 slice(i+1),    i=0時 slice(1)    -> ab
        
            
             */
            const candidate = ds.slice(0, i+1)
            if(isPali(candidate)){
                dfs(ds.slice(i+1), path.concat(candidate))
            }
        }
    }

    dfs(s, [])
    return res
};


function isPali(s) {
    let i = 0
    let j = s.length - 1
    while (i <= j) {
        if (s[i] !== s[j]) return false
        i++
        j--
    }
    return true
}
```

---


### [1239. Maximum Length of a Concatenated String with Unique Characters](https://leetcode.com/problems/maximum-length-of-a-concatenated-string-with-unique-characters/)
給一個 arr, 裡面有很多 string
請問可以返回多少個 subsequence?
subsequence是由arr裡面的string連接組成
組成結果的char需要unique(有un就不可以有ue了，因為都有u)    (所以題目要求 **unique characters**)
且要維持order(可以 un+iq, 不可以 iq+un) → 因為是subsequence
![[IMG-Backtracking-20250810200116760.png]]


考你會不會判斷 unique character
這一題求 max length
感覺很像是DP題, 那要加上 mem 嗎?
因為本質還是在求 path, 只是要找最長的 path
因此還是要組path, 因此還是需要跑進去(不可以被 mem 擋住?)
因此還是比較算是backtracking 題目


```ts fold
var maxLength = function(arr) {
    
    let res = 0
    
    function dfs(ds, path){

        //error point:
        // if you return when ds.length === 0 (aka: if(ds.length===0) return)
        // the path `charact` will not go below to update its length
        // cuz we will have ds.length === 0 at last round
        // and we want to get the new path length and update in this round
        let pathSet = new Set(path)
        
        if(path.length !== pathSet.size) return
        
        res = Math.max(res, path.length)
        
        for (let i =0; i<ds.length;i++){
            dfs(ds.slice(i+1), path + ds[i])
        }
    }
    
    dfs(arr, "")
    
    return res
    
};
```
### [22. Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)
![[IMG-Backtracking-20250810200116926.png]]

```ts fold

var generateParenthesis = function(n) {
    
    let res = []
    
    function dfs(left, right, path){
    
        if (left == n && right == n){
            res.push(path)
            return
        }
        
        
        // 兩個條件
        // left < n -> 可加 left
        // right < left -> 才可加 right
        if (left < n) {
	        dfs(left+1, right, path + "(")
        }
        if (right < left) {
	        dfs(left, right+1, path + ")")
        }


        /*
        這個寫法一定就只有一個 dfs per round
        if left is 2 and right is 1
        and in this logic, we will call the first dfs and finish this iteration
        and this will not get us other path
        the result is be only one path = > ["()()()"]
        */
//        錯誤的寫法 用 if else  <-- 這個寫法一定就只有一個 dfs per round
//         if(left > right) 
//             dfs(left, right+1, path+")")
//         else 
//             dfs(left+1, right, path+"(")
//     }
    }
    
    dfs(0,0, "")
    return res
    
};
```




更多 backtracking 請看 notion
[Notion – The all-in-one workspace for your notes, tasks, wikis, and databases.](https://www.notion.so/nture4388/Backtracking-41a330b072b74e9daec50b575885d5e6?pvs=4#7cc20241beb14b429f1f3c1b3cb61d94)

