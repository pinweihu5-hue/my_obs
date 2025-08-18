
當然，我很樂意為您介紹 JavaScript 的 `Array.prototype.reduce()` 方法，並提供一些實用的例子。

JavaScript Array Reduce 介紹與示例


```js

// JavaScript Array Reduce 介紹

/*
reduce() 方法對數組中的每個元素執行一個由您提供的 reducer 函數（升序執行），
將其結果彙總為單個返回值。

語法：
array.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue])

參數：
- callback：用於處理數組中每個元素的函數，包含四個參數：
  - accumulator：累加器，累加回調的返回值
  - currentValue：數組中正在處理的元素
  - index（可選）：數組中正在處理的元素的索引
  - array（可選）：調用 reduce() 的數組
- initialValue（可選）：作為第一次調用 callback 函數時的第一個參數的值 <-- 重要，就是 acc 的 init value
*/




// 示例 1：數組求和
const numbers = [1, 2, 3, 4, 5];
const sum = numbers.reduce((acc, curr) => acc + curr, 0);  // init is 0
console.log(sum); // 輸出：15




// 示例 2：計算數組中每個元素出現的次數
const fruits = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple'];
const fruitCount = fruits.reduce((acc, fruit) => {
  // acc is obj, so we can use obj related way to do things...
  acc[fruit] = (acc[fruit] || 0) + 1;
  return acc; // 記得要 return
}, {}); // init is a object
console.log(fruitCount); // 輸出：{ apple: 3, banana: 2, orange: 1 }




// 示例 3：數組扁平化 (one level deep)
const nestedArray = [[1, 2], [3, 4], [5, 6]];

// init is [], each iter, we concat into empty
const flattenedArray = nestedArray.reduce((acc, curr) => acc.concat(curr), []);
console.log(flattenedArray); // 輸出：[1, 2, 3, 4, 5, 6]




// 示例 4：按條件分組
const people = [
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 30 },
  { name: 'Charlie', age: 35 },
  { name: 'David', age: 25 }
];
const groupedByAge = people.reduce((acc, person) => {

  // acc is init as {}
  // acc["25"] = 那個 25 key 的 value 或是 空 []
  // 然後 push person 進去
  (acc[person.age] = acc[person.age] || []).push(person);
  
  return acc;
}, {});
console.log(groupedByAge);

輸出
{
  "25": [
    {
      name: "Alice",
      age: 25,
    }, {
      name: "David",
      age: 25,
    }
  ],
  "30": [
    {
      name: "Bob",
      age: 30,
    }
  ],
  "35": [
    {
      name: "Charlie",
      age: 35,
    }
  ],
}


// 示例 5：找出最大值
// init 0
// 每一輪都只取 max 的結果
const numbers2 = [10, 5, 8, 3, 12, 7];
const max = numbers2.reduce((acc, curr) => Math.max(acc, curr));
console.log(max); // 輸出：12



// 示例 6：將多個數組合併為一個對象
// init 是 {}, 
const keys = ['name', 'age', 'city'];
const values = ['Alice', 30, 'New York'];
const person = keys.reduce((acc, key, index) => {
  // 用 key 來 reduce, so, key 就是  ['name', 'age', 'city']
  // values[index], 就對應到 value 的0,1,2 idx -> 對應到 ['Alice', 30, 'New York']
  acc[key] = values[index];
  return acc;
}, {});
console.log(person); // 輸出：{ name: 'Alice', age: 30, city: 'New York' }


// 示例 7：去除數組中的重複元素
const numbers3 = [1, 2, 2, 3, 4, 4, 5];
const uniqueNumbers = numbers3.reduce((acc, curr) => {

  // acc init as empty arr
  // 不包括才可以推進去
  if (!acc.includes(curr)) {
    acc.push(curr);
  }
  return acc;
}, []);
console.log(uniqueNumbers); // 輸出：[1, 2, 3, 4, 5]



// 示例 8：計算購物車總價
const cart = [
  { item: 'Apple', price: 0.5, quantity: 3 },
  { item: 'Banana', price: 0.3, quantity: 5 },
  { item: 'Orange', price: 0.6, quantity: 2 }
];
const totalPrice = cart.reduce((acc, item) => acc + item.price * item.quantity, 0);
console.log(totalPrice); // 輸出：3.7


// 示例 9：將字符串轉換為對象

const str = 'key1=value1&key2=value2&key3=value3';
// split 切為 arr
const obj = str.split('&').reduce((acc, pair) => {
  // split 切為 key val 處理
  const [key, value] = pair.split('=');
  acc[key] = value;
  return acc;
}, {});
console.log(obj); // 輸出：{ key1: 'value1', key2: 'value2', key3: 'value3' }

// 示例 10：計算數組中所有偶數的平方和
const numbers4 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const sumOfSquaresOfEvenNumbers = numbers4.reduce((acc, curr) => {
  if (curr % 2 === 0) { // 只看偶數
    return acc + curr * curr;  // 平方和 curr*curr, 然後累加
  }
  return acc;
}, 0);
console.log(sumOfSquaresOfEvenNumbers); // 輸出：220 (4 + 16 + 36 + 64 + 100)

// 示例 11：將數組轉換為樹形結構
const paths = [
  'root/folder1/file1.txt',
  'root/folder1/file2.txt',
  'root/folder2/file3.txt',
  'root/folder2/subfolder/file4.txt'
];

// 兩層reduce
const tree = paths.reduce((acc, path) => {
  path.split('/').reduce((subAcc, part) => {
    if (!subAcc[part]) {
      subAcc[part] = {};
    }
    return subAcc[part];
  }, acc);
  return acc;
}, {});

console.log(JSON.stringify(tree, null, 2));


```

這些例子展示了 `reduce()` 方法的多樣性和強大功能。它不僅可以用於簡單的數值計算，還可以用於複雑的數據轉換和結構化。

以下是一些使用 `reduce()` 的建議：

1. 當需要將數組轉換為單一值（如求和、查找最大值）時，`reduce()` 是一個很好的選擇。
2. 對於需要遍歷整個數組並累積結果的操作，`reduce()` 通常比循環更簡潔。
3. 使用 `initialValue` 參數可以讓你的 `reduce()` 操作更加靈活，特別是當你想要返回一個與原數組類型不同的結果時（如數組到對象的轉換）。
4. 對於複雜的操作，考慮將 reducer 函數單獨定義，以提高代碼的可讀性。
5. 記住，雖然 `reduce()` 功能強大，但對於簡單的操作（如僅遍歷數組），使用 `map()`, `filter()`, 或普通的 `for` 循環可能更清晰。
6. `reduce()` 可以與其他數組方法（如 `map()`, `filter()`）結合使用，以實現更複雜的數據處理。


---


## JavaScript Array Reduce 介紹

`reduce()` 是 JavaScript 陣列中一個非常強大的方法，它可以讓你迭代陣列中的每個元素，並將其「累加」成一個單一的值。這個值可以是任何資料類型，例如數字、字串、物件，甚至是另一個陣列。

**語法：**

```javascript
array.reduce(callback(accumulator, currentValue, currentIndex, array), initialValue);
```

**參數：**

* **callback:**  一個函數，它會在陣列中的每個元素上執行。這個函數接受四個參數：
    * **accumulator:**  累加器，用於儲存每次迭代後的結果。
    * **currentValue:**  當前正在處理的元素。
    * **currentIndex (可選):**  當前元素的索引。
    * **array (可選):**  正在被迭代的陣列。
* **initialValue (可選):**  作為第一次迭代時 `accumulator` 的初始值。如果沒有提供，則會使用陣列中的第一個元素作為初始值，並且迭代會從第二個元素開始。

**回傳值：**

`reduce()` 方法會回傳一個單一的值，也就是最後一次迭代後 `accumulator` 的值。

## 10 個 JavaScript Array Reduce 範例：

**1. 計算陣列中所有數字的總和：**

```javascript
const numbers = [1, 2, 3, 4, 5];
const sum = numbers.reduce((acc, curr) => acc + curr, 0);
console.log(sum); // Output: 15
```

**2. 找出陣列中的最大值：**

```javascript
const numbers = [5, 12, 8, 130, 44];
const max = numbers.reduce((acc, curr) => (curr > acc ? curr : acc), numbers[0]);
console.log(max); // Output: 130
```

**3. 將陣列中的字串合併成一個字串：**

```javascript
const words = ["Hello", " ", "world", "!"];
const sentence = words.reduce((acc, curr) => acc + curr, "");
console.log(sentence); // Output: "Hello world!"
```

**4. 計算陣列中每個元素出現的次數：**

```javascript
const fruits = ["apple", "banana", "apple", "orange", "banana", "apple"];
const fruitCount = fruits.reduce((acc, curr) => {
  acc[curr] = (acc[curr] || 0) + 1;
  return acc;
}, {});
console.log(fruitCount); // Output: { apple: 3, banana: 2, orange: 1 }
```

**5. 將二維陣列扁平化為一維陣列：**

```javascript
const matrix = [[1, 2], [3, 4], [5, 6]];
const flatArray = matrix.reduce((acc, curr) => acc.concat(curr), []);
console.log(flatArray); // Output: [1, 2, 3, 4, 5, 6]
```

**6. 根據物件的屬性對物件陣列進行分組：**

```javascript
const people = [
  { name: "Alice", age: 30 },
  { name: "Bob", age: 25 },
  { name: "Charlie", age: 30 },
];
const groupedByAge = people.reduce((acc, curr) => {
  const key = curr.age;
  acc[key] = (acc[key] || []).concat(curr);
  return acc;
}, {});
console.log(groupedByAge); 
// Output: { 25: [{ name: 'Bob', age: 25 }], 30: [{ name: 'Alice', age: 30 }, { name: 'Charlie', age: 30 }] }
```

**7.  過濾陣列中的元素：**

```javascript
const numbers = [1, 2, 3, 4, 5, 6];
const evenNumbers = numbers.reduce((acc, curr) => {
  if (curr % 2 === 0) {
    acc.push(curr);
  }
  return acc;
}, []);
console.log(evenNumbers); // Output: [2, 4, 6]
```

**8.  實現 `map()` 方法：**

```javascript
const numbers = [1, 2, 3, 4, 5];
const doubledNumbers = numbers.reduce((acc, curr) => {
  acc.push(curr * 2);
  return acc;
}, []);
console.log(doubledNumbers); // Output: [2, 4, 6, 8, 10]
```

**9.  計算購物車中商品的總價格：**

```javascript
const cart = [
  { product: "apple", price: 1, quantity: 2 },
  { product: "banana", price: 0.5, quantity: 3 },
  { product: "orange", price: 1.2, quantity: 1 },
];
const totalPrice = cart.reduce((acc, curr) => acc + curr.price * curr.quantity, 0);
console.log(totalPrice); // Output: 4.1
```

**10.  建立一個 Promise 鏈：**

```javascript
const urls = ["url1", "url2", "url3"];
const promiseChain = urls.reduce((acc, curr) => {
  return acc.then(() => fetch(curr));
}, Promise.resolve());

promiseChain.then(() => console.log("All URLs fetched!"));
```

這些只是 `reduce()` 方法的一些基本範例，它還有許多其他應用場景。希望這些例子可以幫助你更好地理解和使用這個強大的方法！
