proposal
- hermes level and pricing svc level
	- strcit: true
		- [TypeScript: TSConfig Reference - Docs on every TSConfig option](https://www.typescriptlang.org/tsconfig/#strict)
			- alwaysStrict
			- strictNullChecks
			- strictBindCallApply
			- strictFunctionTypes
			- strictPropertyInitialization
			- noImplicitAny
			- noImplicitThis
			- useUnknownInCatchVariab
	- strictPropertyInitialization: false  目前 hermes 設定是 false
		- [TypeScript: TSConfig Reference - Docs on every TSConfig option](https://www.typescriptlang.org/tsconfig/#strictPropertyInitialization)
		- 當設置為 true 時，如果一個 class 的屬性被聲明但未在構造函數中被賦值，TypeScript 會跳錯。

```ts

class UserAccount {
  name: string;
  accountType = "user";
 
  // error Property 'email' has no initializer and is not definitely assigned in the constructor.
  email: string;
  address: string | undefined;
 
  constructor(name: string) {
    this.name = name;
    // Note that this.email is not set
  }
}


```


how to fix it?
```ts

class UserAccount {
  name: string
  accountType = 'user'

  email: string
  address: string | undefined

  constructor(name: string) {
    this.name = name
    this.email = 'example@gmail.com' // <--- add this line, 也可以從外面傳進來
  }
}


```


- add noUncheckedIndexedAccess to true
	- 先不要加入到 Project Template 
	- 先在 new micro 上打開試試看，是否會有雷，確認不有太大的坑。




# noUncheckedIndexedAccess


未啟用 noUncheckedIndexedAccess，可能會發生以下三種類型的錯誤：

陣列索引超出範圍：

```ts

const numbers = [1, 2, 3];

// 陣列索引超出範圍, 編譯器不會警告，但這裡會得到 undefined
const number = numbers[4]; 

// 執行時會拋出錯誤，因為 undefined 沒有 toFixed 方法
console.log(number.toFixed(2)); 

```

物件存取不存在的屬性：
```ts

const user: { [key: string]: string } = { name: "Alice", age: "25" };

// 存取不存在的屬性, 編譯器不會警告，但這裡會得到 undefined
const value = user["address"];

// 執行時會拋出錯誤，因為 undefined 沒有 toUpperCase 方法
console.log(value.toUpperCase()); 
```

動態鍵值存取：
```ts

const fruits = { 
	apple: "red", 
	banana: "yellow" 
}; 

function getColor(fruit: string) {
  //動態鍵值存取, 編譯器不會警告，當 fruit 是不存在的鍵值時會得到 undefined
  return fruits[fruit]; 
}

const color = getColor("cherry");
console.log(color.length); // 執行時會拋出錯誤，因為 undefined 沒有 length 屬性

```


How to fix it?



Tuple & const assertions

how to define a fix length array

tuple
```ts


const x: [number, number, number] = [1, 2, 3]

// `x` type is `number`
const x = arr[0] 


// error: Tuple type '[number, number, number]' of length '3' has no element at index '9'.
const y = arr[1000] 


```


as const / readonly
```ts

const arr = [1, 2, 3] as const // `arr` type is `readonly [1, 2, 3]`

const x = arr[0] // `x` type is `number`
const y = arr[1000] // error: Tuple type '[number, number, number]' of length '3' has no element at index '9'.

```

使用這種型別斷言之後，型別會自動加上 readonly 關鍵字，ts 編譯器就知道這個資料型別是 immutable 的，自然長度也不會變了
以上兩種方法不管 noUncheckedIndexedAccess 有沒有配置，都能正確辨識索引溢位的錯誤





# hermes config
```json

{
  "compilerOptions": {
    "skipLibCheck": true, // Do not check node_modules .d.ts
    "target": "es6" /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017','ES2018' or 'ESNEXT'. */,
    "module": "commonjs" /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'. */,
    "sourceMap": true /* Generates corresponding '.map' file. */,
    "rootDir": "." /* Specify the root directory of input files. Use to control the output directory structure with --outDir. */,



    "strict": true /* Enable all strict type-checking options. */,
    "strictPropertyInitialization": false /* Enable strict checking of property initialization in classes. */,



"esModuleInterop": true /* Enables emit interoperability between CommonJS and ES Modules via creation of namespace objects for all imports. Implies 'allowSyntheticDefaultImports'. */,
    "experimentalDecorators": true, /* Enables experimental support for ES7 decorators. */
    "forceConsistentCasingInFileNames": true
  }
}


```



# pricing config
```json
{
  "compilerOptions": {
    "module": "commonjs",
    "declaration": true,
    "removeComments": true,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "allowSyntheticDefaultImports": true,
    "target": "ES2021",
    "sourceMap": true,
    "outDir": "./dist",
    "baseUrl": "./",
    "incremental": true,
    "skipLibCheck": true,
    
    "strict": true,
    "strictPropertyInitialization": false
  }
}

```


嚴格檢查
```json

    /* Strictness */
    "strict": true, // <-- hermes 已經有

    "noUncheckedIndexedAccess": true,  // <-- 下面解釋
	
	// 可以考慮加入，可以讓 EMS and CLS 兩個模組系統同時存在上更好處理。簡單來說，TS 會幫我們墊一層在舊的 CLS 模組上，讓我們盡量都可以用 EMS 語法
	"esModuleInterop": true,

```





ref:
- [the TSConfig Cheat Sheet - YouTube](https://youtu.be/eJXVEju3XLM?si=d3eOlBzQeuh6tDGa)
- [Build a Node App with TypeScript | Total TypeScript](https://www.totaltypescript.com/typescript-and-node)
- [TSC Base · GitHub](https://github.com/tsconfig)
- [简单聊一聊 typescript 中的 noUncheckedIndexedAccess · Issue #21 · hacker0limbo/my-blog · GitHub](https://github.com/hacker0limbo/my-blog/issues/21)