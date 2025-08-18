
- view-sonic
	- 練習題解答
		```jsx fold
		// 1.curring 實作 add(2)(5) // 7
		// 1.1 add2(10) //12
		// 1.2 add3(10) //15
		
		// 實作 helloSat 這個函式：
		// helloSat(1, 2); // 3
		// helloSat(1)(2); // 3
		
		// add(2,3)
		//
		// let res = add(2)(5)
		// console.log("=====> res: ", res);
		//
		//
		// let res2 = add(2)(5)(3)
		// console.log("=====> res2: ", res2);
		
		//   2.
		//   fun2([1,2,3,4,5]) //相加為 10
		
		// function fun2(arr) {
		//   let a1 = [...arr].slice(0,-1)
		//   console.log("=====> a1: ", a1);
		//   return a1.reduce((prev, cur) => prev+cur, 0)
		//
		// }
		//
		// let res = fun2([1,2,3,4,5])
		//   console.log("=====> res: ", res);
		
		//   3.
		//   fun3([1,2,2,2,3,3,4,4,5,5,5,]) // 去重為[1,2,3,4,5]
		
		// function fun3(arr) {
		//   return [...new Set([...arr])]
		// }
		
		// function fun3_v2(arr) {
		//   let s1 = new Set()
		//   for (const ele of arr) {
		//     if(s1.has(ele)) continue
		//     else s1.add(ele)
		//   }
		//
		//   return [...s1]
		// }
		
		// let res = fun3_v2([1,2,2,2,3,3,4,4,5,5,5,])
		// console.log("=====> res: ", res);
		
		// // 4.
		// fun4([1,[2,[3,[4,[5]]]]]) // 不用 flat 攤平 為[1,2,3,4,5]
		
		// function fun4(arr) {
		//   return arr.reduce((prev, cur) => prev.concat(Array.isArray(cur)? fun4(cur):cur),[])
		// }
		//
		// let res1  = fun4([1,[2,[3,[4,[5]]]]])
		// console.log("=====> res1: ", res1);
		
		//   5.
		//   function a (callback){
		//     setTimeout(() => { console.log('a')}, 500) }
		
		//   function b (callback){
		//     setTimeout(() => { console.log('b')}, 200)
		//   }
		
		//   function c (callback){
		//     setTimeout(() => { console.log('c')}, 300)
		//   }
		
		// function tobind (callback) { //修改這裡的內容，讓執行結果出來為 a,b,c
		//   a(callback)
		//   b(callback)
		//   c(callback)
		// }
		
		// 最後我做的是下面的解法
		// 還是有改 原本函數
		// 因為我需要把 resolve 放在 setTimeout 的 cb 邏輯 裡面
		//   5.
		
		function a(callback) {
		  return new Promise((resolve, reject) => {
			setTimeout(() => {
				resolve()
				console.log('a')
			  }, 500
			)
		  })
		}
		
		function b(callback) {
		  return new Promise((resolve, reject) => {
			setTimeout(() => {
			  resolve()
			  console.log('b')
			}, 200)
		  })
		}
		
		function c(callback) {
		  return new Promise((resolve, reject) => {
			setTimeout(() => {
			  resolve()
			  console.log('c')
			}, 300)
		  })
		}
		
		// async function run1() {
		//     await a()
		//     await b()
		//     await c()
		// }
		
		// run1()
		
		function tobind(callback) { //修改這裡的內容，讓執行結果出來為 a,b,c
		  //   a().then(()=>{
		  //     b().then(()=>{
		  //         c().then()
		  //     })
		  //   })
		
		  a().then(
			()=>{
			  return b()
			}
		  ).then(
			()=>{
			  return c()
			}
		  )
		
		  // (async () => {
		  //     await a()
		  //     await b()
		  //     await c()
		  // })
		
		}
		
		tobind()
		
		function a() {
		  setTimeout(() => {
			console.log('a')
		  }, 500)
		}
		
		function b() {
		  setTimeout(() => {
			console.log('b')
		  }, 200)
		}
		
		function c() {
		  setTimeout(() => {
			console.log('c')
		  }, 300)
		}
		
		function promisifyV2 (fn) {
		  return new Promise( (resolve, rejected) => {
			resolve(fn)
		
		  })
		}
		
		function promisify(fn) {
		  return new Promise((resolve, reject) => {
			try {
			  fn()
			  resolve()
			} catch (error) {
			  reject(error)
			}
		  })
		}
		
		  function a (callback){
			setTimeout(() => { console.log('a')}, 500) }
		
		  function b (callback){
			setTimeout(() => { console.log('b')}, 200)
		  }
		
		  function c (callback){
			setTimeout(() => { console.log('c')}, 300)
		  }
		
		function promisify(fn) {
		  return new Promise((resolve, reject) => {
			try {
			  resolve()
			  fn()
			} catch (error) {
			  reject(error)
			}
		  })
		}
		
		function tobind (callback) { //修改這裡的內容，讓執行結果出來為 a,b,c
		  ( async () => {
			await promisify(a)
			await promisify(b)
			await promisify(c)
		  })();
		}
		
		tobind()
		```
		
	- 練習題
		
		```js fold
		1.curring 實作 add(2)(5)
		{
		1.1 add2(10) //12
		1.2 add3(10) //15
		}
		2. fun2([1,2,3,4,5]) //相加為 10
		3. fun3([1,2,2,2,3,3,4,4,5,5,5,]) // 去重為[1,2,3,4,5]
		4. fun4([1,[2,[3,[4,[5]]]]]) //不用 flat 攤平 為[1,2,3,4,5]
		5.
		function a (callback){
		 setTimeout(() => { console.log('a')}, 500)
		}
		function b (callback){
		 setTimeout(() => { console.log('b')}, 200)
		}
		function c (callback){
		 setTimeout(() => { console.log('c')}, 300)
		}
		function tobind (callback) {
		 //修改這裡的內容，讓執行結果出來為 a,b,c
		 a(callback)
		b(callback)
		c(callback)
		}
		```
		
	
	轉職的切入點：
	
	- wemo沒 但 vs 有的
		- global 產品去歷練, 800m 使用者
	- 更大規模的體制
		- 深度
	
	薪資結構
	
	N x 14 + avg. 10~12% (獎金)
	

