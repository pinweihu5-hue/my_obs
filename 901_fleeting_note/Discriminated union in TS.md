---  
alias:  
creation_date: 2024-06-11 19:21  
tags: 
related:
- [[_ts_index]]

---  

在 TypeScript 中，**區別聯合（Discriminated Union）** 是一種強大的模式，用於處理具有多種可能類型的變量。每種可能類型都有一個共通的區別屬性（discriminant property），用來區分具體是哪一種類型。

好處？
- 可避開 flag variable (what is flag variable? -> `const isPending, isProcessing, isConfirmed, isExpired;` 等等)
- 可以避開 optional argument -> 複雜度高  example see [TypeScript Style Guide |](https://mkosir.github.io/typescript-style-guide/#args-as-discriminated-union)

```ts


type Circle = { kind: 'circle'; radius: number };
type Square = { kind: 'square'; size: number };
type Triangle = { kind: 'triangle'; base: number; height: number };

// Create discriminated union 'Shape', with 'kind' property to discriminate the type of object.
type Shape = Circle | Square | Triangle;

// TypeScript warns us with errors in calculateArea function
const calculateArea = (shape: Shape) => {
  // Error - Switch is not exhaustive. Cases not matched: "triangle"
  switch (shape.kind) {
    case 'circle':
      return Math.PI * shape.radius ** 2;
    case 'square':
      return shape.size * shape.width; // Error - Property 'width' does not exist on type 'square'
  }
};



```



不使用聯合類型的例子
```ts

interface Circle {
  radius: number;
}

interface Rectangle {
  width: number;
  height: number;
}

type Shape = Circle | Rectangle;

function getArea(shape: Shape): number {
  if ("radius" in shape) {
    return Math.PI * shape.radius ** 2; // 圓形面積公式
  } else if ("width" in shape) {
    return shape.width * shape.height; // 矩形面積公式
  }
  throw new Error("Invalid shape");
}

const myCircle: Circle = { type: "circle", radius: 5 };
const myRectangle: Rectangle = { type: "rectangle", width: 10, height: 5 };

console.log(getArea(myCircle)); // 78.53981633974483
console.log(getArea(myRectangle)); // 50



```



使用聯合類型的例子
```ts

interface Circle {
  type: "circle";
  radius: number;
}

interface Rectangle {
  type: "rectangle";
  width: number;
  height: number;
}

type Shape = Circle | Rectangle;

function getArea(shape: Shape): number {
  switch (shape.type) {
    case "circle":
      return Math.PI * shape.radius ** 2; // 圓形面積公式
    case "rectangle":
      return shape.width * shape.height; // 矩形面積公式
    default:
      throw new Error("Invalid shape");
  }
}

const myCircle: Circle = { type: "circle", radius: 5 };
const myRectangle: Rectangle = { type: "rectangle", width: 10, height: 5 };

console.log(getArea(myCircle)); // 78.53981633974483
console.log(getArea(myRectangle)); // 50

export {};


```



ref: [TypeScript Style Guide |](https://mkosir.github.io/typescript-style-guide/#args-as-discriminated-union)