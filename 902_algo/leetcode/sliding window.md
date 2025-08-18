- 搭配一個 res 來 Math.max / min 來 update global 的res
- Use while (某個條件）to shrink (l++) by 某個條件 ( 可能會需要搭配的 hash/map/obj/set幫忙)
- 常見 pattern (常見，但不是都一定適用，自己斟酌)

```ts
let l = 0
for (let r = 0; i < r.length; r++) {
	
	while(shrinking_condition){
		// ...
		l++
	}
	
}

```


### [28 Find the Index of the First Occurrence in a String](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/description/) 稻草找針

基本題型 
只有 window 沒有 shrinking condition的 while operation

```ts fold

function strStr(haystack: string, needle: string): number {
    let l = 0
    let r = needle.length
    for (let i = 0; i < haystack.length; i++) {
        let seg = haystack.slice(l, r)
        if(seg === needle) return i
        l++
        r++
    }

    return -1
};
```




### [209 Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)
Q: 求滿足 subarr sum ≤ target 的subarr最小長度


有 shrink 的 operation
在 while loop 中, 會 shrink window , 只要條件符合， 就一直shrink 
這一題要找的就是符合條件下的shrink 後的最小區間長度
```ts fold
function minSubArrayLen(target, nums) {

/**
 * 使用滑動窗口的概念來尋找滿足條件的子陣列。
 * 
 * 滑動窗口的左邊界 (l) 和右邊界 (r) 都從索引 0 開始。

   l
   r
  [2,3,1,2,4,3]


             l
             r
  [2,3,1,2,4,3]


 * 
 * 首先，右邊界 (r) 向右移動，直到窗口內的元素總和 (windowSum) 大於或等於目標值 (target)。
 * 
 * 然後，記錄當前窗口的長度 (windowLen)。
 * 
 * 接著，左邊界 (l) 向右移動一步，並檢查窗口內的元素總和是否仍然大於或等於目標值：
 *     - 如果滿足條件，則記錄當前窗口的長度。
 *     - 如果不滿足條件，則右邊界 (r) 向右移動一步，直到窗口內的元素總和再次大於或等於目標值。
 * 
 * 重複上述步驟，直到右邊界 (r) 到達陣列的末尾。
 * 
 * 使用一個全域變數來追蹤最小窗口長度 (minLen)。
 * 
 * 注意：第一次計算時，我忘記使用滾動總和的概念，導致每次判斷都需要重新計算總和，效率較低。
 */
    
    let l=0
    let res = Infinity // 求最小長度，初始化無窮大
    let subArrSum = 0
    
    // 這種 winsum 題目，都是一個在外面走，另外一個window inner loop 在裡面

    // r 一直走，直到最後一個 idx
    for (let r = 0; r < nums.length; r++) {
        subArrSum += nums[r]
 
        while (subArrSum >= target) { // 一旦條件符合
            res = Math.min(res, r-l+1)
            
            // 試試看 shrink window by l 也走
            // (如果也是 subArrSum >= target，會繼續在 while 裡面, update res)
            subArrSum -= nums[l]
            l+=1
        }
        
    }
    
    if(res === Infinity) return 0 // where we just can't reach the target
    else return res
};
```




錯誤記錄:
一直return Inifinity -> 一直 runtime error

然後我用自己的 IDE 又測試不出問題
原來是不同的 test case 造成的

因為有 edge case 是，最後都會進不去那個 while loop: like this case
`target = 11, nums = [1,1,1,1,1,1,1,1]`

然後就會 ret 無限大
然後系統就認定 runtime error
其實就是題目也有說，如果找不到，那要 ret 0



---


### [ 713. Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/)
Q: 給定arr 和 k, 要找出有多少個subarray 符合其products(積) 小於等於 k



===
這題是要縮完才會符合目標，所以更新結果是在while loop後面
跟上一題不同，上一題是縮的過程中就要去抓那個最小的長度，所以更新結果就是在while loop裡面
```ts fold


function numSubarrayProductLessThanK(nums: number[], k: number): number {
    let l = 0
    let res = 0
    let p = 1

    for (let r = 0; r < nums.length; r++) {
        p = p * nums[r]


        /**
         寫錯的邏輯：while(p > k)
         要找的是 all the elements in the subarray is strictly less than k
         就是要找的 subarr 需要小於k
         因此如果 "p >= k" 就要 shiink
         
         我錯在沒有理解好英文，strictly less than k 是 p<k, 反過來是 p>=k

         另外 忘記加上 l <= r 這個條件
         你如果沒加上， l 會超過r
         say l=5, r= 2, r-l+1 就是負數一直下去
         因此你拿掉試試看，就會看到負數的答案
         邏輯上，你要找的也是 l到r 區間，不能讓l超過r

         */
        // while(p > k){
        while(l <= r && p >= k){
            p = p / nums[l]
            l++
        }
        res += r-l+1
    }
    return res
};






function numSubarrayProductLessThanK(nums: number[], k: number): number {
    let res = 0
    let l =0
    let p =1
    
    for(let r=0 ; r<nums.length; r++){
        
        // why need to put here not below while?
        // 放上面表示，要先乘，然後再檢查
        p = p * nums[r]
        
        while(l<=r && p >= k){
            p = p / nums[l]
            l+=1
        }
        
        // 因為這一輪的r, l 指針需要都走到符合條件的下的位置(p<k)
        // 然後r-l+1? see below
        res += (r-l+1)
        
    }
    
    return res

};

/**

The formula to conunt the child contiguous subarrays in a big contiguous subarrays,
is the same as the length of a subarray, both are `r-l+1`

r and l is window 2 side
see below Exs

     ass idx     4   5   6     6-4+1=3  
for a subarray: (5, (2, (6))):
You got below 3 contiguous subarrays
        (6)
     (2, 6)
  (5, 2, 6)
  
         ass idx  0   1        1-0+1=2
for a subarray: (10, (5)):,  
You got below 2 contiguous subarrays
         (5)
     (10, 5)




// ref: https://leetcode.com/problems/subarray-product-less-than-k/discuss/108861/JavaC%2B%2B-Clean-Code-with-Explanation


**/



```


### [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters)


這題更新結果在 while loop 後，why?
因為我們要透過 while shrinking 讓區間符合條件， 然後我們再 Update result
然後如果用set, add set operation is usually after the shrinking, otherwise, you  just check what you add
```ts fold



/**

l
 r
012345    
pwwkew
{pw}
maxLen=2

r++
add to the set {}
check if already in the set
    no  -> add char into set and record max
    yes -> no add record to max
           while loop -> l++

有重複，不可以記入
window shrnk -> l++

l
r    
pwwkew

 */
function lengthOfLongestSubstring(s: string): number {
    if(s==="") return 0

    let l=0
    let set1=new Set()
    let maxLen=-Infinity

    for (let r = 0; r < s.length; r++) {

        while(set1.has(s[r])){
            set1.delete(s[l])
            l++
        }

        maxLen=Math.max(maxLen, r-l+1)
        set1.add(s[r])
    }

    return maxLen
};

```



---

### [1695 Maximum Erasure Value](https://leetcode.com/problems/maximum-erasure-value/)

Q
移除的 subarray 的 element 需要是 unique
你的分數就是你移除的 element 的 sum
element 來自於你只能移除的那一個特定的 subarray
subarr 的定義：要連續element in arr
請問你能獲得的最高分數是多少




這一題多了一個新的元素，就是要持續累積新的分數
另外這一題的縮放主要就是利用set來檢查是否要進行縮放
```ts fold
/**

移除的 subarray 的 element 需要是 unique
你的分數就是你移除的 element 的 sum
element 來自於你只能移除的那一個特定的 subarray

subarr 的定義：要連續element in arr

請問你能獲得的最高分數是多少



 */
function maximumUniqueSubarray(nums: number[]): number {
/**
確定了沒有再加入set

  l
        r
4,2,4,5,6
curScore 11 
maxScore 11
{2,4,5,6}
*/
    let l=0
    let r=0
    let maxScore = -Infinity
    let curScore = 0
    let s = new Set()

    while(r<=nums.length-1){

        while(s.has(nums[r])){
            s.delete(nums[l])
            curScore-=nums[l]
            l++
            
        }
        
        curScore+=nums[r]
        maxScore = Math.max(maxScore, curScore)
        s.add(nums[r])
        r++
    }

    return maxScore
    
};



// or

function maximumUniqueSubarray(nums: number[]): number {
    let res = -Infinity
    let s1 = new Set()

    let l = 0
    for (let r = 0; r < nums.length; r++) {
        let ele = nums[r]

        while (s1.has(nums[r])) {
            s1.delete(nums[l])
            l++
        }
        // 上面 shink 後 算 sum and update res
        let sum1 = sumup(nums.slice(l, r + 1))  // <--- 應該要像是上面用 累加的
        res = Math.max(sum1, res)
        
        // 最後 add
        s1.add(ele)
    }
    return res
};

function sumup(arr) {
    return arr.reduce((cur, prev) => cur + prev)
}
```


### [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)
Q:
給定大寫 string  like ABAB 和 k
你可以把其中B 改成 A or A 改成 B, 改一個算一個操作
k 是最多可以操作次數
求改完後的相同字連起來的字串，最長有多少

ex1
ABAB, k=2

改兩次 AAAA  or BBBB 

ans: 4

ex2
AABABBA k=1

AA**A**ABBA  →ans 4
or
AAB**B**BBA  → ans4



```ts fold

function characterReplacement(s, k) {
    let upperCaseChar = 'abcdefghijklmnopqrstuvwxyz'.toUpperCase()
    let charFreq = {}
    for (let i = 0; i < upperCaseChar.length; i++) {
        let char = upperCaseChar[i]
        charFreq[char] = 0
    }

    let res = -Infinity
    let l = 0
    let currentCharLen = -Infinity

    for (let r = 0; r < s.length; r++) {
        let currentChar = s[r]
        charFreq[currentChar]++

        // 追蹤目前 currentCharLen 是多少
        currentCharLen = Math.max(currentCharLen, charFreq[currentChar])

        /**
        l  r    
        AABABBA
        {A: 3, B: 1}
        currentCharLen: 3  -》 useCharLen might be a better naming 
        winlen: 4 

        winlen-currentCharLen 表示用當下的 A 來做為基礎
        需要多少個 char 被調整才會變成一樣的字
        這個例子是 1

        如果超過 k, 就需要 shrink

         */
        while ((r - l + 1) - currentCharLen > k) {  // 你這邊如果要用 while, 你就要把 winLen 放在這邊，才會跳出去
            charFreq[s[l]]--
            l++
        }

        res = Math.max(r - l + 1, res)
    }

    return res
}


```


---

### [Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/)
Q:

給一個 fruits arr
裡面的 element 值代表的是水果類型
一個 element 就一個水果

規則
- 只能從 arr idx 0 開始拿
- 拿了就要從下一個 idx 一直拿, 類似idx 0 拿了idx1,2,3 都要連續拿, 直到拿到兩個種類後，就無法拿
求**最多**可以**拿幾個**水果

====


這一題比較特別的地方就是在於那個陣列的元素竟然是種類而不是水果的數量，這個有點違反常見用法
然後我們就可以用Maps來幫助我們控制水果的種類有多少透過Maps的size

```js fold

var totalFruit = function (arr) {
  let l = 0;
  let m1 = new Map();
  let res = 0;

  for (let r = 0; r < arr.length; r++) {
    if (m1.has(arr[r])) {
      m1.set(arr[r], m1.get(arr[r]) + 1);
    } else {
      m1.set(arr[r], 1);
    }

    while (m1.size > 2) {
      if (m1.get(arr[l]) === 1) {
        m1.delete(arr[l]);
      } else {
        m1.set(arr[l], m1.get(arr[l]) - 1);
      }

      l++;
    }

    res = Math.max(res, r - l + 1);
  }

  return res;
};


```


---

### [Max Min](https://www.hackerrank.com/challenges/angry-children/problem?isFullScreen=true)

給一個 arr 和 k
找出 k 個 element  的 arr 組合, 然後可以拿到最小的 Unfairness
Unfairness是 arr 的 Max val  - arr的 Min Val 算出來的

```ts fold


function maxMin(k, arr) {
  arr.sort((a, b) => a - b);

  let minUnfairness = Infinity; 
  

  for (let i = 0; i <= arr.length - k + 1; i++) {
    const subArray = arr.slice(i, i + k);

    const unfairness = Math.max(...subArray) - Math.min(...subArray);

    minUnfairness = Math.min(minUnfairness, unfairness);
  }

  return minUnfairness;
}

```



關鍵思想：
unfairness 是 subArr 最大和最小的差異
最後我們要找的是 *最小的差異*
因此如果你把  arr 排序，最小 unfairness 就一定會在兩兩排好的區間中



這一題基本上就是最簡單的 稻草找針題
unfairness 題目給了, gloabl update就可以了
關鍵在於要 sort, 才可以用 subarr 來找

如果你沒有 sort, 直接用 backtracking 真的去找出所有的 combination 然後再去算 -> 大多數的 test 都 TLE

```ts fold

// 這種解法，大多數都的 test 都 TLE
function combo(nums, k) {
  let res = Infinity;
  
  dfs(nums, []);
  
  return res;
  
  function dfs(ds, path) {
    if (path.length === k) {
      const unfairness = Math.max(...path) - Math.min(...path);
      res = Math.min(res, unfairness);
    }
    for (let i = 0; i < ds.length; i++) {
      dfs(ds.slice(i + 1), path.concat(ds[i]));
    }
  }
}


```




### [1838 Frequency of the Most Frequent Element](https://leetcode.com/problems/frequency-of-the-most-frequent-element/description/)
Q:
給定 nums 和 integer k
k 是可以操作幾次
每一次操作可選定一個 ele  +1
給定 k 次，求最大的 feq 可以是多少?

ex:
[1,2,4], k=5
1  add 3次 =>4
2 add2次  =>4
-> 最後變成 [4,4,4] -> freq is 3

```ts fold

function maxFrequency(nums: number[], k: number): number {
  
  nums.sort((a, b) => a - b); // 对数组进行排序 -> 才可 sliding window
  
  let curSum = 0;
  let res = 0; 

  let l = 0;
  for (let r = 0; r < nums.length; r++) {
    curSum += nums[r]; 


    // window_len * nums[r] : 窗口內 all ele 都變成 nums[r] 的總和
    // curSum + k, 窗口加總加上k
    // 如果 all element 都變成 nums[r] > 窗口加總加上操作數量(每一次操作數量就是 1 ) -> need to shrink
    while (nums[r] * (r - l + 1) > curSum + k) {
      curSum -= nums[l];
      l++;
    }
    // 上面 while 過了，目前的窗口就是全部都可以變成 nums[r]

    // 因為這個區間內都變成一樣的數字 (nums[r]) -> 因此區間長度就是 freq
    res = Math.max(res, r - l + 1);
  }

  return res;
}

```
TIL
- 思考角度要轉換為:
	- how about 我們架設把一個窗口內的值都變成一個值 (e.g. `nums[r]`) 需要多少 sum
	- 那我們有多少 sum 可以用? curSum + k
	- 如果超過，就 shrink!
	- 當窗口內都是同一個值，算 freq 也就是 win_len 了


## pali 題
### [647. Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)
Q:
給定 一個 string, like `sss`
找出 string 裡面有多少個 substring 是 pali

===


```ts fold

function countSubstrings(s: string): number {

    let res = 0

    /**

    outter loop:
	    go thru each char of string
	inner while loop:
	    l/r inside out 的 while loop 去判斷 pali
    
    然後 pali 可以從自己本身出發
    也可以從 l,r 兩個 ptr 出發

     */




    for (let i = 0; i < s.length; i++) {
        /**
            <-l->
            <-r->
            01234  
            aaanc
        */
        let l = i
        let r = i
        while (l >= 0 && r <= s.length - 1 && s[l] === s[r]) {
            res++
            l--
            r++
        }

    }





    for (let i = 0; i < s.length; i++) {
        /**
            012345  
            <-lr->
            aabbcc
        */
        let r = i
        let l = i - 1
        while (l >= 0 && r <= s.length - 1 && s[l] === s[r]) {
            res++
            l--
            r++
        }
    }

    return res
};

```


### [5. Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
給定一個 string s
找出 最長的 pali substring

```ts fold

function longestPalindrome(s) {
   let longest = 0; // keep track of the longest palindrome len
   let res = ""; // keep track of the longest palindrome

   /**
    * if s length is odd
    *   l
    *   r
    * babad
    *
    */
   for (let i = 0; i < s.length; i++) {
      let l = i;
      let r = i;
      // 邊界檢查 and pali 條件 ok
      while (l >= 0 && r < s.length && s[l] === s[r]) {
         let w = r - l + 1;
         if (w > longest) {
            longest = w;
            res = s.slice(l, r + 1);
         }
         r += 1;
         l -= 1;
      }
   }

   /**
    * if s length is even
    *   lr
    * babadd
    *
    */
   for (let i = 0; i < s.length; i++) {
      let l = i;
      let r = i + 1;
      while (l >= 0 && r < s.length && s[l] === s[r]) {
         let w = r - l + 1;
         if (w > longest) {
            longest = w;
            res = s.slice(l, r + 1);
         }
         r += 1;
         l -= 1;
      }
   }

   return res;
}


```



