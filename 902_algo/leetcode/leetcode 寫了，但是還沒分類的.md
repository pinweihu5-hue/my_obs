


## [Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/)

```js fold


var removeDuplicates = function (nums) {
    // 維護一個 我等等要砍掉的 idx 放在這個 marked
    let marked = []
    let count = 0
    for (let i = 0; i < nums.length; i++) {
        // 第一個略過, 如果這一個和上一個不同， count 歸零
        if (i > 0 && nums[i] !== nums[i - 1]) {
            count = 0
        }

        count++
        


        if (count > 2) {
            marked.push(i)
        }
    }
    


    const m1 = shiftMarked(marked)
    deleteFromMarked(nums, m1)
}


// 把上面標記的 marked idx 從 nums in-place 刪掉
function deleteFromMarked(arr1, marked) {
    for (let i = 0; i < marked.length; i++) {
        arr1.splice(marked[i], 1)
    }
    return arr1
}


// 因為砍掉會移動 idx, 因此我們需要把 marked 調整
// input: marked = [1, 2, 3]
// output: marked = [0, 1, 2], 
// idx 0, 減一, idx1, 減二, idx2, 減三
function shiftMarked(mark) {
    let increment = 0
    for (let i = 0; i < mark.length; i++) {
        mark[i] = mark[i] + increment
        increment--
    }
    return mark
}


```



### [Excel Sheet Column Number](https://leetcode.com/problems/excel-sheet-column-number/description/)
```js fold

var titleToNumber = function (colStr) {
    let result = 0

    for (let i = 0; i < colStr.length; i++) {
        let char = colStr.charAt(i)
        const charASCII = char.charCodeAt(0) // get 當前字母的 ASCII 碼

        // 將字母轉換為對應的數字, 以 A 的 base -> A -> 1, B -> 2, C -> 3, D -> 4, E -> 5, F -> 6, G -> 7, H -> 8, I -> 9, J -> 10, K -> 11, L -> 12, M -> 13, N -> 14, O -> 15, P -> 16, Q -> 17, R -> 18, S -> 19, T -> 20, U -> 21, V -> 22, W -> 23, X -> 24, Y -> 25, Z -> 26
        // 會需要加 1 是因為 'A' 要對應到 1, 從 1開始
        const base = 'A'.charCodeAt(0)
        const curNum = charASCII - base + 1

        // 每次迴圈，將之前的結果乘以 26，然後加上當前字母對應的數字。
        // 這相當於將 26 進制轉換為 10 進制的操作
        result = result * 26 + curNum // 計算結果
    }

    return result
}


```
TIL:
- step1: 需要把 ptr 下的 char 轉為 number
- step2: 如何把 26 進制轉為 10 進制
- 中間需要處理 off 1 的差異


### [Excel Sheet Column Title](https://leetcode.com/problems/excel-sheet-column-title/description/)
```js fold

var convertToTitle = function (colNum) {
    let result = ''

    while (colNum !== 0) {
        // colNum - 1，因為Excel列號從 1 開始，而我們需要將其轉換為 0-based 的索引
        colNum--

        // 計算這一輪的字母
        const baseChar = 'A'.charCodeAt(0)
        const curChar = baseChar + (colNum % 26)

        // fromCharCode 可以把 ASCII 碼轉換成字母, e.g. 65 -> 'A'
        // 然後因為我們上面用取餘數的方式，我們是從後面開始，新的字母就會在前面
        result = String.fromCharCode(curChar) + result

        // 更新 colNum 26
        colNum = Math.floor(colNum / 26)
    }

    return result
}

```
TIL:
- 要記得 -1 因為 excel 從 1 開始，但我們要從 0 開始處理
- 要記得這兩個內建函數
	- `'A'.charCodeAt(0)` :  from 'A' -> 65
	- `String.fromCharCode(curChar)`: from 65 -> 'A'
- 透過取 reminder and baseChar 拿到 curChar, 這個是從後面開始處理的。
- 每一輪也要 floor 加上 除 -> 來進入下一輪 


## [Add Binary](https://leetcode.com/problems/add-binary/description/)
Q: Given two binary strings a and b, return their sum as a binary string.
example:
Input: a = "1010", b = "1011"
Output: "10101"


```js fold

function addBinary(a, b) {
    // i 和 j 分別指向 a 和 b 的最後一個字元
    let i = a.length - 1
    let j = b.length - 1

    let carry = 0 // 儲存進位值，初始為 0
    let result = ''

    // 只要 a 或 b 還有未處理的數字，或者還有進位值，迴圈就會繼續執行。
    while (i >= 0 || j >= 0 || carry) {
        // digitA 和 digitB 分別取得 a 和 b 的當前位數字。如果已經超出字串邊界(還有進位值時)，則視為 0。
        const digitA = i >= 0 ? parseInt(a[i]) : 0
        const digitB = j >= 0 ? parseInt(b[j]) : 0


        // 這邊的操作跟 Linked List 那題 add number 幾乎一樣，只是這邊是 binary 而不是 decimal。
        const sum = digitA + digitB + carry
        result = (sum % 2) + result // 將餘數 concat 到結果字串的前面
        carry = Math.floor(sum / 2) // 更新進位值

        i--
        j--
    }

    return result
}


```
