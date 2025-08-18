

[[gRPC 分散系統下的進階功能]]




---


### use optional?

兩個作法
- server use null -> client 檢查如果是 sgtring is 是 "" -> 就是沒有
- server use wrapper type -> 如果 client 沒有收到 -> 就是沒有




什麼是正確的作法
- don't use optional, it's proto2, will be remove in the future
- 如果要用，用proto3 提供的 wrapper type
- 建立一個共識， server 如果設定 null -> 那就是 fall back to its primitive default ->  client 端要那麼去接
	- 如果 default 有意義，like 0 有意義，那就要用 optional

現在要怎麼做? (考慮到時間壓力?或是要改的很多)
- 我這邊會想要不動， client 那邊判斷 disabled At 如果是 '' -> 那就是ret undef
- 或是 client 那邊如果改很多 -> 那我這邊先改改看use wrapper type, 如果無法，那就用 optional
	- 我這邊也要改成 code, null 要調整 undefined







### proto3 中， 你沒預定的 primitive type 會 fallback 到 primitive 的 default  value, 要小心


proto 如下
```proto

message Stock {
    int32 productId = 1;   /* 產品編號 */
    int32 count = 2;       /* 庫存量 */
}

```

你這樣送
`{productId: 1}`

你沒有送, primitive default int32 to 0，會收到：
`{productId: 1, count: 0}`

你會誤會產品 1 的庫存是 0
其實你根本沒送

為何會這樣設計?
因為proto 設計是不會轉型 為 nulll
因為這也是很多語言的邏輯，like C, Java, Go
如果 pirmititve type 沒有設置，他就回到本來那個型別的 default value (boolean -> false, int->0, string-> '')
(寫 JS TS 會不習慣，因為 JS , TS 的 number, boolean string 是 wrapper/object type, 也可以有null)

好處是兩點
- 對 Java (或 Go 或 C, …) 的使用者來說，protobuf 產生出來的類別更接近其原生 structs，也就是說會更輕量，更好整合
- 所有的 default 值都可以不用儲存了


題外話, JS, C, Go 這種設計，可以避免下面的錯誤
```js

/** 統計總庫存量 */
function countStock (stocks) {
    let total = 0;
    stocks.forEach(stock => {
        total += stock.count; // 如果 stock.count 是 null or undefined, 這裡就噴了, Java, Go, 等 IDE 就會提示了。
    }
    return total;
}

```


那如果你希望那個是可以不給的，你要這樣寫
```proto

import "google/protobuf/wrappers.proto";

message Test {
 int32 productId = 1;                  /* int32 = required */
 google.protobuf.Int32Value count= 2;  /* Int32Value = optional */
}


```






ref: 
[設計思考 — Protocol Buffers 3 為什麼. 簡單是一件非常難的事! 而深思熟慮過的簡單，可以給我們最多的思考與學習 | by Leon Tsai | Medium](https://medium.com/@leon740727/%E8%A8%AD%E8%A8%88%E6%80%9D%E8%80%83-protocol-buffers-3-%E7%82%BA%E4%BB%80%E9%BA%BC-49219fc87bb7)



### why proto2 -> 3 拿掉 required and optional

如果這樣定義
```proto
message Stock {
    required int32 productId = 1;   /* 產品編號 */
    required int32 mtime = 2;       /* 統計時間 */
    optional int32 count = 3;       /* 庫存量 */
}
```


有 required 的 productId 很安心不用檢查
optional的你要去檢查
```js 

const pids = stocks
    .filter(stock => stock.count !== null && stock.count > 0)
    .map(stock => stock.productId);

```


why proto3 要拿掉 optional 關鍵字

因為 proto3 想要專心處理 encode, decode, 他想要讓  app 自己去檢查

因為google 認為，使用的人自己有責任去把 type 定義好，不是 proto3的職責(only handle decode and encode)
why?
因為就算是同一個 type, 不同的使用的人，不同的使用方法，就會有不同的 type

譬如上面的 stock, 統計時間 如果從 db 撈，就可能會是 required
如果你是塞入 db, 那你就可能是 optional, 因為 db 會 auto gen createdAt

那如果你不同的 api, 使用不同的 query, like `select * from stock` 跟 `select productId, count from stock`
這樣也有不同的 type
因此應該是讓 app, 使用者自己去管理這個職責


ref: 
[設計思考 — Protocol Buffers 3 為什麼. 簡單是一件非常難的事! 而深思熟慮過的簡單，可以給我們最多的思考與學習 | by Leon Tsai | Medium](https://medium.com/@leon740727/%E8%A8%AD%E8%A8%88%E6%80%9D%E8%80%83-protocol-buffers-3-%E7%82%BA%E4%BB%80%E9%BA%BC-49219fc87bb7)




---


### proto3 的 primitive 沒設定就是預設值

[GitHub - stephenh/ts-proto: An idiomatic protobuf generator for TypeScript](https://github.com/stephenh/ts-proto): 把 proto  轉 ts 的 lib 

在 In core Protobuf (and so also `ts-proto`), 

如果你沒有設定 value or 是 default value => 都不會 sent over the wire (like Jack said, 為了省空間)
**values that are _unset_ or equal to the default value are not sent over the wire.**

message 的 default val 就是 undefined in js
Primitive 的 使用  的 default value, e.g. `string` is `''`, `number` is `0`, 等等等

這意味著，你無法區別 pritive type 的沒設定 and 預設值



ref: [GitHub - stephenh/ts-proto: An idiomatic protobuf generator for TypeScript](https://github.com/stephenh/ts-proto?tab=readme-ov-file#default-values-and-unset-fields)



## why this proto -> 變成下面的 ts interface, 會多了 undefined
```proto

syntax = "proto3";

import "google/protobuf/wrappers.proto";

message ExampleMessage {  
google.protobuf.StringValue name = 1;  
}


```

```ts
interface ExampleMessage {
  name: string | undefined;
}
```


ANS: 
在 Protobuf 3 中，所有欄位都是預設可選的 (optional)。
雖然 Protobuf 2 使用 `optional` 關鍵字來表示可選欄位，但在 Protobuf 3 中，`optional` 關鍵字已被移除，所有欄位預設都是可選的。

當你使用 `google.protobuf/wrappers.proto` 中的訊息類型（例如 `StringValue`、`Int32Value` 等）作為欄位類型時，你實際上是在利用這些 wrapper message 來表示一個值可能存在也可能不存在。Wrapper message 的存在與否，就代表了 Protobuf 欄位的值是否存在。

因此，當 Protobuf 編譯器將 `.proto` 檔案轉換成 TypeScript 程式碼時，它會將 `google.protobuf.StringValue name = 1;`  這樣的欄位轉換成 `name: string | undefined;`，因為 `name` 欄位的值可能是一個字串，也可能不存在 (undefined)。


## angelia.Proto ticket, GRPC postman設定 [notion link](https://www.notion.so/nture4388/angelia-Proto-ticket-GRPC-postman-53a46ce57a1b4d72a5f5887c533332fb?pvs=4)


ps: DDIA 也有介紹， see notion DDIA link



## more link
- [GRPC: The Good and the Bad](https://kmcd.dev/series/grpc-the-good-and-the-bad/)