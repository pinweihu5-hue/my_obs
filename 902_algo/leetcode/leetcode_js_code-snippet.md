---  
alias:  
creation_date: 2024-10-12 08:37  
related:
- 

---  


### why n+1 當建立 Array(n+1).fill(xxx)

- 因為我們要把 idx 來獲取**某個概念**的值, 這個概念 is 1-based, not 0-based
- 舉例，我們要知道 第一個node的顏色, 然後我們有10個球，我們用 idx 1~10來代表第一顆到第10顆球
- 可以直接用一個array 來表示, like `color[3]` -> get red, 表示第三顆球是紅色
- 或是我們要知道 第 20個node 是否 visited 過 -> `visited[20]` -> get `true` 
- 因為 array in JS is 0-based, 因此我們的 array 就要建立 n+1 個，idx 0 的位置就不用了，直接使用 這個 array 的 idx 1 ~ idx n


###  array, slice, concate, swap
```js fold

let a1 = Array.from({ length: 10 }, () => []);
// [ [], [], [], [], [], [], [], [], [], [] ]

let a2 = Array.from({ length: 10 }, () => 0);
// [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ]

// 也可以用來建立一個 shallow copy and sort by 屬性
let intervalsByStart =  Array.from(intervals).sort((a, b)=>a.start-b.start)

// 這個寫法還比較好寫，也快上面一個一點點點點
// 不過比較沒有彈性，只能塞val, 不能放cb
const dp = new Array(n).fill(1);

// 如果要用這個寫法把每一個 ele 放上 空 arr: 
let adj = new Array(numCourses).fill(null).map(() => []);

// 建立 2d matrix, 前面用 array.from, 後面用 map 搭配 Array弄出第二層的 0
const matrix = Array.from({ length: n }, () => Array(n).fill(0))

let adj = new Array(numCourses).fill([]) // <--這是錯誤寫法, 因為沒有 map, 這是每一個 ele 都共用同一個 []




// 有關 slice
// 是 copy and return 東西，不會 mutate 原來的 array

let a1 = [1, 2, 3, 4, 5];
let a2 = a1.slice(3); // 3~最後一個，包括最後一個
console.log("a1 -->", a1);  // a1 不變, [ 1, 2, 3, 4, 5 ]
console.log("a2 -->", a2);  // a2 變成 [ 4, 5 ]




// more on slice
//        0  1  2  3  4
let a1 = [1, 2, 3, 4, 5];
let a2 = a1.slice(1, 3); // 1到3，3不包括， [1,3)，半開設計
console.log("a1 -->", a1); // [ 1, 2, 3, 4, 5 ]
console.log("a2 -->", a2); // [ 2, 3 ]

//        0  1  2  3  4
let a1 = [1, 2, 3, 4, 5];
let a2 = a1.slice(0, 1); // [0, 1)
console.log("a1 -->", a1); // [ 1, 2, 3, 4, 5 ]
console.log("a2 -->", a2); // [ 1 ]


//        0  1  2  3  4
let a1 = [1, 2, 3, 4, 5];
let a2 = a1.slice(1, -1); // [1, last_one), -1 表示 last one, 不包括
console.log("a1 -->", a1); // [ 1, 2, 3, 4, 5 ]
console.log("a2 -->", a2); // [ 2, 3, 4 ]


//        0  1  2  3  4
let a1 = [1, 2, 3, 4, 5];
let a2 = a1.slice(-1); // [last_one]
console.log("a1 -->", a1); // [ 1, 2, 3, 4, 5 ]
console.log("a2 -->", a2); // [ 5 ]

//        0  1  2  3  4
let a1 = [1, 2, 3, 4, 5];
let a2 = a1.slice(-2); // [last_two, last_one]
console.log("a1 -->", a1); // [ 1, 2, 3, 4, 5 ]
console.log("a2 -->", a2); // [ 4,5 ]





// concat
let a1 = [1, 2, 3, 4, 5];
let a2 = a1.concat(11, 22); // 把 11, 22 加到 a1 裡面的後面
console.log("a1 -->", a1); // a1 不變, [ 1, 2, 3, 4, 5 ]
console.log("a2 -->", a2); // a2 變成 [ 1, 2, 3, 4, 5, 11, 22 ]
// concat 一樣是 是 return 東西，不會 mutate 原來的 array



// swap between 2 stuff

let x;
let y;
[x, y] = [2, 3];
console.log(x, y); // return 2,3

[x, y] = [y, x];
console.log(x, y); // return 3,2




```


### obj
```ts fold


// shallow compare obj
function shallowEqual(o1, o2) {
  // 比 key length 一樣
  if (Object.keys(o1).length !== Object.keys(o2).length) {
    return false;
  }
  
  // 確認 key 裡面的 value 是否相同
  return Object.keys(o1).every((key1) => o1[key1] === o2[key1]);
}


// 建立 freqMap from string
let s1 = "aabbcc";
function toFreqObj(s) {
    let res = {}
    for (let char of s) {
        res[char] = res[char] ? res[char] + 1 : 1
    }
    return res
}
// {
//    a: 2,
//    b: 2,
//    c: 2,
//  }





// 建立 freqMap from string
let s2 = "aabbcc";
let freqMap = new Map();

for (let char of s2) {
  freqMap.set(char, (freqMap.get(char) || 0) + 1);
}
// freqMap --> Map(3) {
//    "a": 2,
//    "b": 2,
//    "c": 2,
//  }




```


### for-loop 語法
```ts fold

// 基本款
for (let i = 0; i < 5; i++) {
  console.log(i); 
}


// for...of -> 只能拿 ele
// 用於遍歷可迭代對象（如陣列、字串、Map、Set 等）。
let arr = [1, 2, 3, 4, 5];
for (let ele of arr) {
  console.log(ele);
}


// 如果要拿到 idx, use `.entries()`
for (const [index, value] of arr.entries()) {
  console.log(`Index: ${index}, Value: ${value}`);
}

不過上面這種語法有幾一些無法處理
1. 類似你要從後面往前面 loop
for (let i = arr.length-1; i >= 0; i--) {}

2. 類似你要從特定 idx 開始
for (let i = j; i < arr.length; i++) {}   -> like 最長子序列(LIS)的 DP 題
            ^^^

3. 類似你要一次 走 x step
for (let i = 0; i < arr.length; i=i+2) {} //走兩步



// for...in
// 用於遍歷對象的可枚舉屬性（包括對象本身的屬性和從原型鏈繼承的屬性）。
let obj = { a: 1, b: 2, c: 3 };
for (let key in obj) {
  console.log(key, obj[key]);
}
// Output: a 1, b 2, c 3


```



### set operation
```ts fold

const obj = {
  foo: 'bar',
};
const mySet = new Set();
mySet.add(1); // [1]
mySet.add(5); // [1, 5]
mySet.add(5); // [1, 5]，重複的元素不會被加進去，依然是
mySet.add(obj); // [ 1, 5, { foo: 'bar' } ]

mySet.has(5); // true
mySet.has(obj); // true
mySet.has({
  foo: 'bar',
}); // false

mySet.delete(5); // true，刪除成功  // PS: map 也是 delete
mySet.delete(2); // false，刪除失敗
mySet.size; // 2

// for ... of
mySet.entries(); // [key, value] 內容相同
mySet.values(); // 和 mySet.keys 得到相同的內容
mySet.keys(); // 和 mySet.values 得到相同的內容

mySet.forEach((item) => console.log('item', item));
mySet.clear();


```


### 優化手段
- mem 的 obj -> 改為 Map
- mem 的key:
`let key = i + ',' + sum -> 原本寫法        // -> 兩個 +, 需要自動轉型`
`let key = String(i) + ',' + String(sum)  // -> 這個還是有點慢，你有兩個 + 來 concate`
`let key = String(i) +  String(sum)       // -> 這個是比較快的寫法，少一個 +, 另外也不需要自動轉型`  -> 但是這個寫好有時候會出錯，因為沒有把兩個 i and sum 區隔，會不會出錯看題目
let key = `${i}-${sum}`  //  好像更快

- 另外base case 的判斷順序也會影響速度


### splice
```js

// splice
// modify in place
// array.splice(start[, deleteCount[, newItem1[, newItem2[, ...]]]])

// 插入

//           0    1    2    3
const m1 = ['a', 'b', 'c', 'd'];
// at index 1, Inserts ele
m1.splice(1, 0, 'qqq');
console.log(m1); 
// ["a", "qqq", "b", "c", "d"]


// remove
// 從索引 3 的位置開始，刪除 1 個元素
                 0        1       2           3         4
var myFish = ["angel", "clown", "drum", "mandarin", "sturgeon"];
var removed = myFish.splice(3, 1);

// removed 為 ["mandarin"]
// myFish 為 ["angel", "clown", "drum", "sturgeon"]


// 置換

//           0    1    2    3
const m2 = ['a', 'b', 'c', 'd'];
// at index 2, replaces 1 element with 'ggg'
m2.splice(2, 1, 'ggg');


console.log(m2); 
// ["a", "b", "ggg", "d"]


// 從索引 0 的位置開始，刪除 2 個元素並插入「parrot」、「anemone」和「blue」
var myFish = ["angel", "clown", "trumpet", "sturgeon"];
var removed = myFish.splice(0, 2, "parrot", "anemone", "blue");

// myFish 為 ["parrot", "anemone", "blue", "trumpet", "sturgeon"]
// removed 為 ["angel", "clown"]


// 從索引 2 的位置開始，刪除 2 個元素
var myFish = ["parrot", "anemone", "blue", "trumpet", "sturgeon"];
var removed = myFish.splice(myFish.length - 3, 2);

// myFish 為 ["parrot", "anemone", "sturgeon"]
// removed 為 ["blue", "trumpet"]





// 從索引 -2 的位置開始，刪除 1 個元素
var myFish = ["angel", "clown", "mandarin", "sturgeon"];
var removed = myFish.splice(-2, 1);

// myFish 為 ["angel", "clown", "sturgeon"]
// removed 為 ["mandarin"]


// 從索引 2 的位置開始，刪除所有元素（含索引 2）
var myFish = ["angel", "clown", "mandarin", "sturgeon"];
var removed = myFish.splice(2);

// myFish 為 ["angel", "clown"]
// removed 為 ["mandarin", "sturgeon"]

https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/splice


```



### other
ref: https://regexone.com/
```ts


function getASCIIS(s:string){
    let res = 0

    // get a ASCII value for a char: 'a'.charCodeAt(0)
    for(let char of s){
        res+=char.charCodeAt(0)
    }
    return res
}


console.log(Math.random());
// Expected output: a number from 0 to <1


// generate a random number between [1, 10] in JS
// 因為random 定多就是 0.999 -> * 10 -> 9.999 -> floor -> 9 因此要 + 1
function getRandomNumber() {
  return Math.floor(Math.random() * 10) + 1;
}





// 移除 s 的 whitespace
s = s.replace(/\s+/g, "");

regex 用 `//` 包起來
裡面 `\s+`  表示 1 or many 的 white space



// includes
// 概念：array 是否 includes ele
const array1 = [1, 2, 3];

console.log(array1.includes(2));
// Expected output: true

const pets = ['cat', 'dog', 'bat'];

console.log(pets.includes('cat'));
// Expected output: true



// string sort in lexicographical order
let str1 = ["a", "c", "b"];

str1.sort((a, b) => a.localeCompare(b));

console.log("str1 -->", str1); //  [ "a", "b", "c" ]

str1.sort((a, b) => b.localeCompare(a));  

console.log("str1 -->", str1);// [ "c", "b", "a" ]





```



