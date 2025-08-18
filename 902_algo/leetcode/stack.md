

## 題目
### [232. Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)

``` ts fold

class MyQueue {
    s1
    s2
    constructor() {
        this.s1 = []
        this.s2 = []
    }



    push(x: number): void {
        this.s1.push(x)
    }

    // 關鍵就是這個函數  -> s2 空了再去 s1 全拿
    s1Tos2IfS2Empty() {
        if (this.s2.length === 0) {
            while (this.s1.length !== 0) {
                this.s2.push(this.s1.pop())
            }
        }

    }

    

    pop(): number {
        this.s1Tos2IfS2Empty()
        return this.s2.pop()

    }

    peek(): number {
        this.s1Tos2IfS2Empty()
        return this.s2[this.s2.length - 1]

    }

    empty(): boolean {
        if (this.s1.length === 0 && this.s2.length === 0) return true
        else return false
    }
}

/**
 * Your MyQueue object will be instantiated and called as such:
 * var obj = new MyQueue()
 * obj.push(x)
 * var param_2 = obj.pop()
 * var param_3 = obj.peek()
 * var param_4 = obj.empty()
 */


```


TIL:
- push to s1
- s1 -> s2, using stack push and pop, s2 的 top 就會是 queue 要拿的第一個
- s1 -> s2 的時機：s2 空了再去 s1 全拿


---

### [225. Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/)

```ts fold


class MyStack {
    q1
    q2
    constructor() {
        this.q1 = []
        this.q2 = []
    }

    push(x: number): void {
        this.q1.push(x)
    }

    q2ToQ1(){
        while(this.q2.length > 0){
            this.q1.push(this.q2.shift())
        }
    }
    

    pop(): number {
    
        // q1 to q2, 只留一個，就是我們要 ret 的
        while (this.q1.length > 1) {
            this.q2.push(this.q1.shift())
        }
        let val = this.q1.shift()
        
        this.q2ToQ1()  // 倒回來, q2 to q1
        return val
    }

    top(): number {
	    
	    // q1 to q2, 只留一個，就是我們要 ret 的
        while (this.q1.length > 1) {
            this.q2.push(this.q1.shift())
        }
        let val = this.q1.shift()
        
        this.q2.push(val) // 這邊只要要看 top, 沒有要拿掉，push to q2
        
        this.q2ToQ1()     // 倒回來, q2 to q1
        
        return val
    }

    empty(): boolean {
        return this.q1.length === 0 && this.q2.length === 0
    }
}



```

TIL:
- 以 q1 為主體，q1 -> q2 留一個來 return, 然後再把 q2 -> q1 倒回來。


另一個寫法，但上面哪個寫法比較 clean
```js fold



class MyStack {
    q1
    q2
    isq1
    constructor() {
        this.q1 = []
        this.q2 = []
    }

    q1Toq2UntilLastOne() {
        // 轉到 q2 until the last one, this last one is for stack pop
        while (this.q1.length > 1) {
            this.q2.push(this.q1.shift())
        }
    }

    q2Toq1() {
        while (this.q2.length !== 0) {
            this.q1.push(this.q2.shift())
        }
    }


    push(x: number): void {
        this.q1.push(x)

    }
    pop(): number {
        this.q1Toq2UntilLastOne()
        const top = this.q1.shift()
        // 這時候 q1 應該是 空的

        // 再全部推回q1
        this.q2Toq1()

        return top
    }

    top(): number {
        this.q1Toq2UntilLastOne()
        const top = this.q1.shift()
        // 這時候 q1 應該是 空的

        this.q2.push(top) // top 多這一行，沒有真的砍掉

        // 再全部推回q1
        this.q2Toq1()

        return top
    }

    empty(): boolean {
        if (this.q1.length === 0 && this.q2.length === 0) return true
        else return false
    }
}



```

---


### [155. Min Stack](https://leetcode.com/problems/min-stack/)
建立一個 stack 機制

可以讓 getMin 是 constant time
PS: 題目的 getMin 只是要 get, 沒有 remove smallest from the stack


```ts fold

/**

這一題的機制就是要讓 min stack and 一般 stack 可以盡量行為一樣
可以看下面的 code, pop 一樣
關鍵就是如果你要push stuff into stack
你要推一個進入 mini stack
只要達到這個邏輯，這樣你的 pop 就是基本上一樣的 code
掉達到這個邏輯，你推入 mini stack 的 ele 需要是 min (因為你要 pop)
你推入 mini stack 的 ele 就至少要跟目前 mini stack top 上的 ele 一樣小

**/

class MinStack {
    m1
    s1
    constructor() {
        this.m1 = []
        this.s1 = []
    }

    push(val: number): void {
       // 如果推進來的 val 大於 this.m1 的 top, 我們當然不可以蓋掉 m1 的 top
       // 那就改推一個替代
       // 替代: m1 top 的東西
       // 如果小於等於，那就也可以推入 m1
       
       
       if(val > this.m1[this.m1.length-1]){
        let tmp = this.m1[this.m1.length-1]
        this.m1.push(tmp)
       } else {
        this.m1.push(val)
       }
    
	   this.s1.push(val)
    }

    pop(): void {
        this.m1.pop() // 一起 pop
        return this.s1.pop()
    }

    top(): number {
        return this.s1[this.s1.length-1]
    }

    getMin(): number {
        return this.m1[this.m1.length-1]
        
    }
}

/**
 * Your MinStack object will be instantiated and called as such:
 * var obj = new MinStack()
 * obj.push(val)
 * obj.pop()
 * var param_3 = obj.top()
 * var param_4 = obj.getMin()
 */


```



### [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)
![[IMG-stack-20250810200102730.png]]

```ts fold


var isValid = function(s) {

    let stack = []

    for (let i = 0; i < s.length; i++) {
        
        const lastOne = stack[stack.length-1]
        if(
            (lastOne === "[" && s[i] === "]") ||
            (lastOne === "(" && s[i] === ")") ||
            (lastOne === "{" && s[i] === "}")
            ) {
            stack.pop()
            continue
        }
        
        stack.push(s[i])
        
    }

    if(stack.length == 0) return true
    else return false
};

```


### [Basic Calculator II - LeetCode](https://leetcode.com/problems/basic-calculator-ii/description/)
跟 Basic Calculator 的差異是，這題不需要處理 (, )
但是你需要處理 乘和除
![[IMG-stack-20250810200106973.png]]

```ts fold

function calculate(s: string): number {
    
    // 觀念：第一個不管怎樣都是 +, 因為第一個 number 都是會用 + 去作用下去
    let curSign = '+'


    let curNum = 0
    let s1 = []
    
    s = s.replace(/\s+/g, '');


    for (let i = 0; i < s.length; i++) {
        let curChar = s[i]

        if (curChar >= "0" && curChar <= "9") {
            curNum = curNum * 10 + Number(curChar)
        }

        if (isNaN(Number(curChar)) || i === s.length - 1) {
            // 錯誤點：下面的判斷是放 curSign 喔
            if (curSign === "+") {
                s1.push(curNum)
            } else if (curSign === "-") {
                s1.push(curNum * -1)
            } else if (curSign === "*") {
                let poped = s1.pop()
                s1.push(poped*curNum)
            } else if (curSign === "/") {
                let poped = s1.pop()
                // poped 放前面，因為前面是先推的
                s1.push(Math.trunc(poped/curNum)) // 順序 check
            }

            // 錯誤點：這邊 curChar 要先 update, 下一輪才會用
            // e.g. `2/3` ->  / 要先 save to here,  下一輪到3了，才可以再上面進行操作
            curSign = curChar 

            curNum = 0 // reset 因為已經推入 s1
        }
    }
    
	let res = 0
    for (let i = 0; i < s1.length; i++) {
        res += s1[i]
    }
    return res
};



```






錯誤記錄
```js fold

var calculate = function (s) {
    let curSign = '+'
    let curNum = 0
    let s1 = []

    // 忘記加這個
    s = s.replace(/\s+/g, '')
    let operator = ['+', '-', '*', '/']

    for (let i = 0; i < s.length; i++) {
        let char = s[i]

        if (!isNaN(char)) {
            curNum = curNum * 10 + Number(char)
        }


        // 忘記加上後面 || 的條件
        // 因為最後一個 number, 你還要要進來去用上一個 curSign 去進行運算
        if (operator.includes(char) || i === s.length - 1) {
            if (curSign === "+") {
                s1.push(curNum)
            } else if (curSign === "-") {
                s1.push(curNum * -1)
            } else if (curSign === "*") {
                s1.push(s1.pop() * curNum)
            } else if (curSign === "/") {
                s1.push(Math.trunc(s1.pop() / curNum))
            }
            curNum = 0
            curSign = char
        }
    }

    return s1.reduce((prev, cur) => prev + cur)
};


```

---


### [150. Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)
```ts fold


function evalRPN(tokens: string[]): number {
    let s1 = []
    for (let i = 0; i < tokens.length; i++) {
        let char = tokens[i]

        if (!isNaN(Number(char))) {
            s1.push(Number(char))

        } else if (char === "+") {
            let pop1 = s1.pop()
            let pop2 = s1.pop()
            s1.push(pop2 + pop1)
        } else if (char === "-") {
            let pop1 = s1.pop()
            let pop2 = s1.pop()
            s1.push(pop2 - pop1)

        } else if (char === "*") {
            let pop1 = s1.pop()
            let pop2 = s1.pop()
            s1.push(pop2 * pop1)

        } else if (char === "/") {
            let pop1 = s1.pop()
            let pop2 = s1.pop()
            s1.push(Math.trunc(pop2 / pop1))
        }
    }

    return s1[s1.length - 1]
};
```
TIL:
- stack 是拿來存 數字，遇到運算符號 pop 出來進行計算，再塞回去
- 可以用 `!isNaN(Number(char))` 來判斷 char 是否是 number, 且不限於 '0' 到 '9' 的判斷,  e.g. `'12'`, `'13'`, `'4'` 等等皆可

### [394. Decode String](https://leetcode.com/problems/decode-string/)
```ts fold

const decodeString = s => {
    
    let stack = [];
    let curString = ""
    let curNum = 0;
    
    for (const char of s) {
        if (char === "[") {
            // Just finished parsing this k, save current string and curNum
            stack.push([curString, curNum]);
            // Reset current_string and k
            curString = ""
            curNum = 0;
        } else if (char === "]") {
            // We have completed this frame, get the last current_string and k from when the frame 
            // opened, which is the k we need to duplicate the current current_string by
            let [prevString, prevNum] = stack.pop()
            curString = prevString + curString.repeat(prevNum); // note:  "a".repeat(3) => "aaa"

					
// or don't use built-in repeat

//             let [prevString, prevNum] = stack.pop()
            
//             let repeatedStr = ""
//             for(let i=0; i<prevNum;i++){
//                 repeatedStr+=curString 
//             }
            
//             curString = prevString + repeatedStr


        } else if (char >= '0' && char <= '9') {
            /*
            say c is 43
            init curN = 0
            
            first round: 
            curN is 0*10 + 4 => curN become 4
            
            second round
            curN is 4*10 + 3 => curN become 43
            
            curN * 10 is to get the current one 到下一個十進位的位置
            Number(c) is to covert char to number
            
            */
            curNum = curNum * 10 + Number(char);
        } else {
            curString += char;
        }
    }
    return curString;
};

```
TIL:
- stack 是拿來存遇到 `[` 時之前的 string, 然後遇到 `]` 可以拿出來使用
- 遇到 multiple digit 如何存 curNum: `curNum = curNum * 10 + Number(char)`




### [32 Longest Valid Parentheses ](https://leetcode.com/problems/longest-valid-parentheses/description/)
```js fold


function longestValidParentheses(s) {
    const s1 = [-1];
    let res = 0;

    for (let i = 0; i < s.length; i++) {
        // when we meet "(", push the idx onto stack
        if (s[i] === "(") {
            s1.push(i);
        } else { // when we meet mathcing one ")"
            s1.pop();

            // 如果 pop 後 stack空了。我們需要記錄當下的 ")" 的 idx
            // so we push index of ')' onto stack
            if (s1.length === 0) {
                s1.push(i);
            // 如果 pop 後 stack 沒空....表示還有  unmatched '(' available 在裡面
            // 我們可以計算目前最長 via i - s1[s1.length - 1]
            // 這裡不是 r-l+1, 是只有r-l, 不需要 + 1, 因為 r-l 的 l 是不要的， 是 unmatched one 
            } else {
                res = Math.max(res, i - s1[s1.length - 1]);
            }
        }
    }

    return res
}


```

solution is [ref](https://leetcode.com/problems/longest-valid-parentheses/solutions/5895691/simple-solution-with-explanation/)

TIL
- stack 變成 記錄 idx, 不是記錄 value
- 邏輯
	- 如果遇到 left -> push idx to stack
	- 如果遇到 right -> pop
		- 如果 stack 空了, 我們需要記錄當下的 right 的 idx, push right 的 idx to stack
		- 如果 stack 還有，我們記 l - r(left) 來拿到目前最長是多少
- pop 不用確認 peak 是否是 `)`, 因為stack 裡面放的是 idx
- 差異算的是 當下 idx 和 stack peak 的 idx
- 需要  `stack = [-1]`
	- 我們要算 len, 初始化 -1 idx, 算是 0 的前一個
	- 因為我們要算差異


---

## mono stack 題目

### [739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)

```md

stack 經典題
for 每一個 loop , 再進行判斷 的後半段會有東西推進去 stack
下一個 iteration 的前半段會去 peak 上一個iteration 後半段推進去的東西，
再進行判斷


另外就是不要忘了要 init all 0 -> why

利用 init 的 res, 我們才可以直接拿 top[1] 中的 idx 去 access
已經 init 好的 res 的 idx 放入 dif

```

```js fold

/**
 * @param {number[]} temperatures
 * @return {number[]}

 */
var dailyTemperatures = function (temperatures) {
    const res = []
    for (let i = 0; i < temperatures.length; i++) { res.push(0) }

    const stack = []
    for (let i = 0; i < temperatures.length; i++) {
        const temp = temperatures[i]

        // 錯誤點，這邊要用 while
        while (stack[stack.length - 1] && stack[stack.length - 1][0] < temp) {
            let top = stack.pop()
            // 利用 init 的 res, 我們才可以直接拿 top[1] 中的 idx 去 access
            // 已經 init 好的 res 的 idx 放入 dif
            res[top[1]] = i - top[1]
        }

        stack.push([temp, i])
    }

    return res
};

```


### [496. Next Greater Element I](https://leetcode.com/problems/next-greater-element-i/)

Q:
去 num1 裡面的每個值，去nums2裡面找 next greater num (就是往右，第一個比自己大的）
找到的話，放那個數，找不到放-1
```js fold

var nextGreaterElement = function (nums1, nums2) {
    
    let valToNge = {}
    let stack = []
    
    for (const n of nums2){
        while(stack.length > 0 && n > stack[stack.length-1]){
            let toped = stack.pop()
            valToNge[toped] = n 
        }
        stack.push(n)
    }
    
    for (let i=0; i< nums1.length;i++){
        if(nums1[i] in valToNge) nums1[i] = valToNge[nums1[i]]
        else nums1[i] = -1
    }
    
    
    return nums1

};

```



錯誤記錄：
`while (stack[stack.length - 1] && stack[stack.length - 1] < ele)`

但應該是
`while (stack.length > 0 && stack[stack.length - 1] < ele)`

上面那個寫法不太好，因為你會撞到當你最後一個值是 0 -> 變成 false XDDD

不然你就這樣寫就會對了
`while (stack[stack.length - 1] !== undefined && stack[stack.length - 1] < ele)`



### [503. Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/)

```js fold

var nextGreaterElements = function(nums) {
    
    let res = Array(nums.length).fill(-1)
    
    stack = []
    
    for (let [idx, ele] of nums.entries()){
        while(stack.length > 0 && ele > stack[stack.length-1][1]){
            let [topIdx, topEle] = stack.pop()
            res[topIdx] = ele
        }
        stack.push([idx, ele])
    }
    
    for (let [idx, ele] of nums.entries()){
        while(stack.length > 0 && ele > stack[stack.length-1][1]){
            let [topIdx, topEle] = stack.pop()
            res[topIdx] = ele
        }
        // stack.push([idx, ele])
    }
    
    return res
};

```

錯誤記錄
`if(stack[stack.length-1]!==undefined && stack[stack.length-1][1] < curEle)`

應該是
`while(stack[stack.length-1]!==undefined && stack[stack.length-1][1] < curEle)

我用 if, 應該是 while
這樣你只會處理一個
但是這邊的關鍵是，只要 curEle 比較大，就會一路把 stack 上的比他小的都 update to res, 不是只有看 toped一個
只要條件符合，就會用 while loop 把 *全部* 都 update to res




其他 stack -> see notion [Notion – The all-in-one workspace for your notes, tasks, wikis, and databases.](https://www.notion.so/nture4388/stack-ca49dff26d374f80865d7abf7320c2a3?pvs=4#1199df69750f803484ddd0b785ad3006)