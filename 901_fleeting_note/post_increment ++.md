[[code_idx]]

if `++` at end, it assign and then increment
if `++` at front, it increment and then assign


```ts


let a1 = 23;
let a2 = a1++;
console.log('a2 -->', a2); // 23


// 在這個例子中,a1++ 是 post-increment 操作,也就是先將 a1 的值賦值給 a2，然後再將 a1 自增 1。
// 所以 a2 的值是 a1 原本的值 23。



let a1 = 23;
let a2 = ++a1;
console.log('a2 -->', a2);
// 在這個例子中,++a1 是 pre-increment 操作,也就是先將 a1 自增 1，然後再將新的值賦值給 a2。
// 所以 a2 的值是 a1 增加後的值 24。



```