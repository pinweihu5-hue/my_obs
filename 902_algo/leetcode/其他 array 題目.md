

### [118. Pascal's Triangle](https://leetcode.com/problems/pascals-triangle/)
題目給一個 N

求 1~ N  row 的 pascal 三角形的 `number[][]` representation
ex:

N=3 
`[ [1],[1,1],[1,2,1] ]` 
N=5?

`[ [1],[1,1],[1,2,1],[1,3,3,1],[1,4,6,4,1] ]`
![[IMG-其他 array 題目-20250810200102735.png]]



這個版本是一次搞定，很久沒都寫的情況
我試著拆邏輯出來一個一個實現，就可以做到！
這就是抽象好的威力!


seonc try (code 跟 first try 邏輯一樣，比較 concise)
```ts fold

function generate(numRows: number) {
    let firstRow = [1]
    let secondRow = [1, 1]
    let res = [firstRow, secondRow]
    
    if(numRows===1) return [firstRow]
    if(numRows===2) return res

    for(let i = 0; i<numRows-2;i++){
        res.push(getNextRow(res[res.length-1]))
    }
    return res
};


function getNextRow(arr) {
    let middle = getMiddle(arr)
    let nextRow = addTwoOneAtTwoSide(middle)
    return nextRow
}

function addTwoOneAtTwoSide(arr: number[]) {
    return [1, ...arr, 1]
}

function getMiddle(arr) {
    let res = []
    for (let i = 0; i <= arr.length - 2; i++) {
        res.push(arr[i] + arr[i + 1])
    }
    return res
}


```

first try
```ts fold

function generate(n: number): number[][] {
		// 先弄兩個 base case 出來
    let res: number[][] = [[1], [1, 1]]

    // if(n===1) return [[1]]
    // if(n===2) return [[1], [1,1]]
    
    if (n === 1) return [res[0]]
    if (n === 2) return res

    const runTimes = n - 2 // 已經有兩個 base case so `-2`
    for (let i = 0; i < runTimes; i++) {
        const nextRow = genNextRow(res)
        res.push(nextRow)
    }

    return res

    ////////////////////////////////////////////////////////
    //////////////////////////// helper///////////////////////////////
    ////////////////////////////////////////////////////////

    function genNextRow(res){
        // 第一次我這邊用 res.slice(-1) -> 這樣會拿到最後一個 ele, 然後放到一個 arr 裡面
        // 但是我要的是下面這樣，我要的是拿最後一個 ele, 沒有要放到一個 arr 裡面
        const lastOne = res[res.length - 1]
        const middle = gatherMiddle(lastOne)
        return addOneOnTwoSide(middle)
       
    }



    // 拿 n=2 的 last ele, 把 i, and i+1 的 ele addup -> iterative -> until i+1 === arr.length-1
    // 這樣會拿到 2, 另外兩變 塞兩個 [1],[1]
    // if(n===3) return [[1], [1,1], []]
    function gatherMiddle(arr){
        // console.log('arr', arr)

        let i = 0
        let j = 1
        let res = []
        while(j<=arr.length-1){
            res.push(arr[i]+arr[j])
            i++
            j++
        }
        return res
    }
    
    function addOneOnTwoSide(arr){
        return [1].concat(arr).concat(1)
    }
};

```


### [287. Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)


```ts fold

function findDuplicate(nums: number[]) {

    let s = 0
    let f = 0
    while (true) {
        s = nums[s]
        f = nums[nums[f]]
        if (s === f) break
    }

    let newStart = 0
    while (true) {
        s = nums[s]
        a = nums[a]
        if (s === a) break
    }
    return s
};

```
關鍵思想
- s, f pointer, 一起出發，f 要比s快兩倍，先找到交會點-> 這邊可以判定是否有 cycle  or duplicate
- 另外用一個 ptr 從原點出發, 跟 s 一樣速度，找到的交會點就是 cycle 的點 -> 這題就是找到那個 duplicate number
- 背後算法思想，跟141. Linked List Cycle 和 Linked List Cycle II 一樣


下面這個解法用 set, 違背題目說的 `using only constant extra space.`
因為你 set 大小會隨著 n 變大而變大
```ts fold


// 利用 idx 迭代 access, 如果有重複就會遇到 → 題目保證會有重複
// 利用 set 來找到是那個 val 重複


function findDuplicate(nums: number[]): number {

    let set = new Set()
    let cur = 0
    let found = undefined
    while(true){
        if(set.has(cur)){
            found=cur
            break
        }
        let oldCur = cur
        cur = nums[cur]
        set.add(oldCur)
    }

    return found


    
};

```




### [ 38 Count and Say ](https://leetcode.com/problems/count-and-say/description/)

遞迴的把上一次的結果算 RLE
遞迴次數是 n


RLE 例子
3322251 -> 23321511
2個3
3個2
1個5
1個1


```js fold




function countAndSay(n) {
    if (n === 1) return '1'
    if (n === 2) return '11'
    if (n === 3) return '21'
    if (n === 4) return '1211'

    let s = '1211'
    for (let i = 0; i < n-4; i++) {
        s = getRLE(s)
    }


    return s
};


function getRLE(s) {
    let a1 = splitString(s)
    let a2 = encode(a1)
    return a2
}


// 把下面這個 string 切成下面那個 arr, 方便後續處理
// intput "3322251"
// output  [["3", "3"], ["2", "2", "2"], ["5"], ["1"]];
function splitString(str) {
    let res = []
    let tmp = [str[0]]
    for (let i = 1; i < str.length; i++) {
        let char = str[i]
        if (char === tmp[tmp.length - 1]) {
            tmp.push(char)
        } else {
            res.push(tmp)
            tmp = []
            tmp.push(char)
        }
    }
    res.push(tmp)
    return res
}


// input [["3", "3"], ["2", "2", "2"], ["5"], ["1"]];
// output 23321511
// 邏輯：拿第一個 ele, 和長度，組起來
function encode(ss) {
    let s = "";

    for (let i = 0; i < ss.length; i++) {
        let subArr = ss[i];
        let char = subArr[0];
        let len = subArr.length;
        s += len + char;
    }

    return s;
}

```

TIL:
這題先這樣切，後續就好處理了
`"3322251"` -> `` [["3", "3"], ["2", "2", "2"], ["5"], ["1"]]`


另一種寫 step1 的方法
```js fold

function step1(s) {
    let res = []
    let prevOne = -1
    let subA = []
    for (let i = 0; i < s.length; i++) {
        if (i === 0) subA.push(s[i])

        if (i >= 1) {
            if (s[i] === prevOne) {
                subA.push(s[i])
            } else {
                // 這邊要用 slice 才可以, 因為下面會清空
                // 不然推過去 res 的會被reference 到，也被清空
                res.push(subA.slice()) 
                subA.length = 0
                subA.push(s[i])
            }
        }
        prevOne = s[i]
    }
    res.push(subA.slice())
    return res
}

```


---
