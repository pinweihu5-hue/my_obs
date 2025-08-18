[[_ts_index]]

在 TypeScript 中，可以使用以下幾種方法來定義枚舉（enum）。每種方法根據不同的需求和應用場景有其特點。

### 1. 數字枚舉（Numeric Enum）

這是最常見和基本的枚舉類型，其中成員自動分配增量值或手動指定數值。

#### 自動分配值

```typescript

enum NumericEnum {
	First, // 0
	Second, // 1
	Third // 2
}

console.log(NumericEnum.First); // 輸出: 0
console.log(NumericEnum.Second); // 輸出: 1
console.log(NumericEnum.Third); // 輸出: 2

```

#### 手動指定值

```typescript

enum NumericEnum {
	First = 1,
	Second = 3,
	Third = 7
}

console.log(NumericEnum.First); // 輸出: 1
console.log(NumericEnum.Second); // 輸出: 3
console.log(NumericEnum.Third); // 輸出: 7

```

### 2. 字符串枚舉（String Enum）

在字符串枚舉中，每個枚舉成員的值是字符串常量，這在某些使用場景中更為直觀。
```typescript

enum StringEnum {
	First = 'FIRST',
	Second = 'SECOND',
	Third = 'THIRD'
}

console.log(StringEnum.First); // 輸出: 'FIRST'
console.log(StringEnum.Second); // 輸出: 'SECOND'
console.log(StringEnum.Third); // 輸出: 'THIRD'

```

### 3. 異構枚舉（Heterogeneous Enum）

異構枚舉允許枚舉成員既可以是數字也可以是字符串，但這種方式不常用，需要注意統一性。

```typescript

enum HeterogeneousEnum {
	No = 0,
	Yes = 'YES'
}

console.log(HeterogeneousEnum.No); // 輸出: 0
console.log(HeterogeneousEnum.Yes); // 輸出: 'YES'

```

### 4. 常量枚舉（Constant Enum）

常量枚舉是數字枚舉的一種特殊情況，當枚舉成員值是計算或需要在編譯期間確定的值，**常量枚舉可以提高性能**。
它在編譯後會被內聯到使用的地方，而不會生成獨立的對象。

```typescript

const enum ConstantEnum {
	First = 1,
	Second = 2,
	Third = 3
}

const value = ConstantEnum.First;

// 編譯後，會直接替換為:
// const value = 1;

```

### 5. 成員初始值為計算值

枚舉成員的初始值也可以是一些計算值：

```typescript

enum CalculatedEnum {
	First = 1,
	Second = First + 1,
	Third = Second * 2

}

console.log(CalculatedEnum.First); // 輸出: 1
console.log(CalculatedEnum.Second); // 輸出: 2
console.log(CalculatedEnum.Third); // 輸出: 4

```

### 小結

TypeScript 中提供了多種定義枚舉的方式，每種方式根據不同的需求和應用場景是有其優缺點的。總結如下：

1. 數字枚舉（預設值或手動指定值）
2. 字符串枚舉
3. 異構枚舉
4. 常量枚舉
5. 包含計算值的枚舉

選擇哪種方式取決於具體應用場景和需求。





# 數字枚舉 和 字符串枚舉有效能差異嗎？

這兩種 TypeScript 的 `enum`（枚舉）寫法具有不同的用途和內部表示方式，因而在性能上也會有所差異。讓我們詳細比較它們。

### 枚舉的定義

1. **數字枚舉（Numeric Enum）**：

```typescript

export enum ScooterStatus {
	Service = 0,
	Servicing = 1,
}

```

2. **字符串枚舉（String Enum）**：

```typescript

export enum ScooterStatus {
	Service = 'Service',
	Servicing = 'Servicing',
}

```

### 用途

- **數字枚舉** 使用數字來表示每個枚舉成員的值，這些值的初始值可以手動指定或由 TypeScript 自動分配。

- **字符串枚舉** 使用字符串來表示每個枚舉成員的值，這意味著每個成員的值是其字符串本身。

### 編譯後的JavaScript代碼

#### 數字枚舉

```javascript

"use strict";

var ScooterStatus;

(function (ScooterStatus) {

ScooterStatus[ScooterStatus["Service"] = 0] = "Service";

ScooterStatus[ScooterStatus["Servicing"] = 1] = "Servicing";

})(ScooterStatus = exports.ScooterStatus || (exports.ScooterStatus = {}));

```

#### 字符串枚舉

```javascript

"use strict";

exports.__esModule = true;

exports.ScooterStatus = void 0;

var ScooterStatus;

(function (ScooterStatus) {

ScooterStatus["Service"] = "Service";

ScooterStatus["Servicing"] = "Servicing";

})(ScooterStatus = exports.ScooterStatus || (exports.ScooterStatus = {}));

```

### 性能差異

1. **數字枚舉** 優點：

- 更高效：數字枚舉在運行時的性能會比字符串枚舉高，因為比較數字比比較字符串更快。
- 雙向映射：生成的 JavaScript 代碼中，數字枚舉支持枚舉名到數值的雙向映射。這對於某些需要逆向查找的場合很有用。

2. **字符串枚舉** 優點：

- 更易讀：調試或日誌記錄時更容易理解，因為枚舉的值就是其字符串本身，無需進行數字到名稱的映射。

- 比較直觀：適用於需要明確字面值的場景，如序列化或外部 API 通訊中。

### 何時選擇哪種枚舉

- **數字枚舉**：適用於內部純粹用來進行邏輯判斷的情形，性能是主要考量。

- **字符串枚舉**：適用於需要與外部系統交互、或者需要更可讀和自解釋的場景。

### 總結

這兩種枚舉在性能上有一定差異，數字枚舉會相對更高效，但字符串枚舉更易於調試和理解。根據你的應用場景需求來選擇合適的枚舉是最佳策略。




# 逆向查找例子

首先，我們定義一個數字枚舉：

```typescript

export enum ScooterStatus {
	Service = 0,
	Servicing = 1,
}

```

### 正向映射




你可以使用枚舉的鍵來獲取其值：

```typescript

console.log(ScooterStatus.Service); // 輸出: 0
console.log(ScooterStatus.Servicing); // 輸出: 1

```

### 逆向查找

你也可以使用枚舉的值來逆向查找其鍵：

```typescript

console.log(ScooterStatus[0]); // 輸出: 'Service'
console.log(ScooterStatus[1]); // 輸出: 'Servicing'

```