[[source code reading]]

---



ref: 
- [GitHub - typicode/json-server: Get a full fake REST API with zero coding in less than 30 seconds (seriously)](https://github.com/typicode/json-server)
- [lowdb](https://github.com/typicode/lowdb)
	- ligh way json db, in mem, in localstorage, session storage 等 adoptor
	- 還可以客製化 adpator
- [GitHub - tinyhttp/tinyhttp: 🦄 0-legacy, tiny & fast web framework as a replacement of Express](https://github.com/tinyhttp/tinyhttp)
	- lighet way express
- [GitHub - paulmillr/chokidar: Minimal and efficient cross-platform file watching library](https://github.com/paulmillr/chokidar)
	- 更好的 watch file tool




# json-server

提供一個 json 檔案, 會讀到 lowdb, 用 tinyHttp一個 簡單的 http server, 讓你可以打



這邊有一個機制值的學習
就是確保  寫入 db  "後" -> 才會從 db 讀新的配置
(就是你還在編輯，沒必要重讀)

```ts

  // Watch file for changes
  watch(file).on("change", () => {
    if (!writing) { // 用這個控制 when to db.read
      db.read().catch((e) => {
        if (e instanceof SyntaxError) {
          return console.log(
            chalk.red(["", `Error parsing ${file}`, e.message].join("\n"))
          );
        }
        console.log(e);
      });
    }
  });


```


```ts

if (process.env["NODE_ENV"] !== "production") {
  let writing = false; // true if the file is being written to by the app
  let prevEndpoints = "";

  obsAdaptor.onWriteStart = () => {
    writing = true;
  };
  obsAdaptor.onWriteEnd = () => {
    // 2. writing is false when onWriteEnd is called
    writing = false;   // <--- 寫完後的 fn hook -> 會把 writing turn to false
  };


```


obsAdaptor.onWriteEnd 從哪裡來?

lowedb allow you to create custom adaptor
```ts

import { Adapter } from "lowdb";

// Lowdb adapter to observe read/write events
export class Observer<T> {
  #adapter;

  constructor(adapter: Adapter<T>) {
    this.#adapter = adapter;
  }

  // hooks
  onReadStart = function () {
    return;
  };
  onReadEnd: (data: T | null) => void = function () {
    return;
  };
  onWriteStart = function () {
    return;
  };
  onWriteEnd = function () { // <-- here
    return;
  };

  async read() {
    this.onReadStart();
    const data = await this.#adapter.read();
    this.onReadEnd(data);
    return data;
  }

  async write(arg: T) {  // each write we will have before and after hook
    this.onWriteStart();
    await this.#adapter.write(arg);
    this.onWriteEnd();
  }
}



```



here, we "inject" this adptor into lowdb
related doc, can see :  [GitHub - typicode/lowdb: Simple and fast JSON database](https://github.com/typicode/lowdb?tab=readme-ov-file#writing-your-own-adapter)
```ts

const obsAdaptor = new ObsAdaptor(adapter);

// 3. create lowdb instance and load with observer instance
const db = new Low<Data>(obsAdaptor, {});
await db.read();

```
