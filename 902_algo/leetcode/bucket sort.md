


## 題目


### [347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)
給一個 arr
裡面的 element 會 duplicate
另外會給你一個 k
找出 最長出現的 element in arr
提供前k個 最常出現的ele, put it into arr and return it

![[IMG-bucket sort-20250810200102708.png]]


```ts fold

/**

bucket sort variation
 [1,1,9,9,6,6,6,2]

 0   1    2    3      <-- idx 是 freq
[[],[2],[1,9],[6]]

freqObj
{
    1: 2
    9: 2
    6: 3
    2: 1
}

init a arry, called it idxFreq -> length 需要至少 原本 arr + 1 (因為 0 不會用到)
iterative freqObj -> 塞入 idxFreq

idxFreq 從後面拿，直到 result.length === k
 */
function topKFrequent(nums: number[], k: number): number[] {
  // 建立 freqObj
  let freqObj = {};
  for (let num of nums) {
    if (!(num in freqObj)) freqObj[num] = 1;
    else freqObj[num] = ++freqObj[num];
  }
  // console.log(freqObj) // { '1': 3, '2': 2, '3': 1 }

  // ini idxFreq
  const idxFreq = Array.from({ length: nums.length + 1 }, () => []);
  // console.log(idxFreq) [ [], [], [], [],[], [], []]
  for (let [key, val] of Object.entries(freqObj)) {
    idxFreq[val as number].push(Number(key));
  }
  //   0    1   2    3   4  5   6
  // [ [], [3], [2], [1],[], [], []]
  // console.log(idxFreq)

  // 從後面一個一個拿
  let res = [];
  for (let i = idxFreq.length - 1; i >= 0; i--) {
    for (let j = idxFreq[i].length; j >= 0; j--) {
      if (idxFreq[i][j] !== undefined) {
        // if(idxFreq[i][j]) 不可以，0 會被濾掉 XD
        res.push(idxFreq[i][j]);
      }
      if (res.length === k) break;
    }
  }
  return res;
}


```



### [692. Top K Frequent Words](https://leetcode.com/problems/top-k-frequent-words/)
![[IMG-bucket sort-20250810200106959.png]]

也是可以用 pq /heap 來解


```ts fold

function topKFrequent(words: string[], k: number) {

    // 建立 freq obj
    let m1 = toFreqObj(words)

	// idx is freq, word 放在每個 idx 下的 arr
    let freqArr = Array.from({length: words.length+1}, ()=>[])
    for (let [word, freq] of Object.entries(m1)){
        freqArr[String(freq)].push(word)
    }

	// sort lexi order
    freqArr.forEach(ele => {
        if(ele.length!==0){
            ele.sort((a, b)=> b.localeCompare(a)) 
        }
    })

    // 從後面一個一個撈出來
    let res = []
    for(let i = freqArr.length-1; i>=0; i--){
        let ele = freqArr[i]
        if(ele.length===0) continue
        for(let j = ele.length-1; j>=0; j--){
            if(res.length===k) break
            res.push(ele[j])
        }
    }

    return res
};



function toFreqObj(words){
    let m1 = {}
    for (let i = 0; i < words.length; i++) {
        let word = words[i]
        m1[word] = m1[word]? m1[word]+1: 1
    }
    return m1
}

```

### [sherlock-and-valid-string](https://www.hackerrank.com/challenges/sherlock-and-valid-string/problem?isFullScreen=true)

```ts fold

function isValid(s) {
    // Write your code here
    let freqObj = toFreqObj(s)
    // console.log(feeqObj)
    let values = Object.values(freqObj)
    // console.log(a1)
    
    // use val as idx and val is like ['a', 'b]
    // we use this to find most freq val
    let arrFreq = Array.from({length: Math.max(...values)+1}, ()=>[])
    for(let [k, v] of Object.entries(freqObj)){
        arrFreq[String(v)].push(k)
    }
    
    // find more freq val
    let mostFreqVal = -1
    let lenInit = -Infinity
    for (let i = 0; i < arrFreq.length; i++) {
        let charArr = arrFreq[i]
        let len1 = charArr.length
        if(len1 > lenInit){
            lenInit = len1
            mostFreqVal = i
        }
    }
    
    // first check, if >=2 notFreqVal exist -> NO
    let notFreqValArr = values.filter( ele => ele !== mostFreqVal)
    if(notFreqValArr.length >= 2) return 'NO'
    
    // second check, if only 1 notFreqVal but dif more than 1 -> NO
    if(notFreqValArr.length===1){
        let notFreqVal = notFreqValArr[0]
        if(notFreqVal-mostFreqVal > 1) return 'NO'
    }
    
    return 'YES'
}

function toFreqObj(s){
    let m1 = {}
    for (let char of s) {
        m1[char] = m1[char]? m1[char]+1: 1
    }
    return m1
}

```