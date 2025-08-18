
OCP 關鍵的實作方法之一



# 上層不要依賴下層，要反過來，上下層都要依賴的是抽象

- 抽象不可以依賴細節 → 要細節依賴抽象
- high level 也不要依賴 low level → 要讓兩個都依賴抽象
- 會叫做 依賴轉換是不要讓下層直接依賴上層，箭頭要反過來，讓他去依賴某個抽象
    - 在JS的世界，這個抽象也可以是一個集合管理lib，類似 IoC container lib
![[IMG-SOLID_DIP 依賴反轉-20250807202919881.png|491]]

[my – FigJam](https://www.figma.com/board/4MD4T4IegKTKPRN3RdJGE5/my?node-id=125-269&t=tpiragUJDE1uwKEd-4)
很多設計模式就是這樣做的
![[IMG-SOLID_DIP 依賴反轉-20250807202920811.png]]

example
```java fold

// high level (drawRectangle, drawTriangle ) 呼叫 low level (draw) -> 直接依賴 low level (draw)
private static void drawRectangle (Rectangle rectangle){        
    rectangle.draw();
}

private static void drawTriangle  (Triangle triangle){        
    triangle.draw();
}

// 上面如果要改需求，rectangle 改為 circle
// 需要新增 circle 類
// 需要新增 drawCircle 類
private static void drawCircle (Rectangle rectangle){        
    circle.draw();
}


// 另一種寫法
// low level (draw) 不要直接依賴 高階的可能變化
// low level (draw) 依賴 的是一個抽象類 abstract Shape
// 如果需要新增 circle, 基本上就只需要新增 circle 類
private static void drawShape(Shape shape){
    shape.draw();
}

```



# 控制流與依賴方向
![[IMG-SOLID_DIP 依賴反轉-20250807202921972.png]]

例子 發票匯出服務 直接 依賴 S3 服務
- 其他人無法reuse
- 更嚴重的是，如果 外部服務 S3 改變了，你要來 “發票匯出服務” 這邊改, A 變了, 為何你要動到 B 的 code?
![[IMG-SOLID_DIP 依賴反轉-20250807202923289.png|953]]


依賴關係就是這樣
如果每一層都是這樣直接依賴
如果 LL8 改了，Main也要改 XD
![[IMG-SOLID_DIP 依賴反轉-20250807202924833.png]]

# 依賴反轉
![[IMG-SOLID_DIP 依賴反轉-20250807202926359.png|837]]


依賴 
createFile 這個穩定的抽象
![[IMG-SOLID_DIP 依賴反轉-20250807202928750.png]]


![[IMG-SOLID_DIP 依賴反轉-20250807202929873.png|815]]


程式碼 不要互相依賴
程式之間的依賴關係，應該都建立在 “業務預期” “穩定” 的物件上，一般就是用 抽象類 或 接口
不穩定的組件，再去實作這些抽象類或接口

![[IMG-SOLID_DIP 依賴反轉-20250807202930659.png]]

如果 LL8 改了
除非ML4的 interface 也改，不然你就只需要動 LL8
![[IMG-SOLID_DIP 依賴反轉-20250807202931398.png|783]]

![[IMG-SOLID_DIP 依賴反轉-20250807202932168.png]]

EX
像是 nest.js
每一個都是 class
controller 直接呼叫 service
沒有喔
我們一樣用依賴反轉
nest.js 底層有做好 IOC container
因此每一個 class 都會注入
另外每一個模組都要定義好需要注入哪些東西，如果出現 依賴cycle, 會跳錯誤(nest.js 也可以解，這是題外話)
因此 controller 裡面的 method 沒有直接呼叫
是呼叫注入 svc
只要 svc 接口沒變，class controller 那邊是不用動的



### 例子：一個業務需求A，為了給某個公司一個特定的輸入格式, 需要撈三個表去組合給他

- 如果我們直接撈，這樣這三個表就跟這個業務A耦合了
- 如果那天，其他工程師，改了這個表被上面這個需求用到的某個欄位，但是他也不知道或不負責上面那個業務A
- 那上面那個功能不就壞掉了？

![[IMG-SOLID_DIP 依賴反轉-20250807202932992.png|801]]
- 正確的作法: 
	- 這三個表要把需要的來資料，去拉出來放在另外一個表/view/db/mem等等等，然後再從這個地方去提供給客戶，不要讓客戶需求直接跟背後的 db 資料結構直接耦合在一起。
	- 這也符合 [[SOLID_ISP 介面隔離 interface segregation principle]]

a simple example using in code
```ts fold

function getUserAndShopAndCarV1() {
  return {
    user: userData,
    shop: shopData,
    car: carData
  }
}

/**
 * 
 * v2's pro and con
 * 
 * con: one more indirection and abstraction
 * pro: 
 * 1. if you change the raw data, you don't need to change v2 code, you change the getter function 
 * 2. v1 directly use the raw data, couple 3 raw data, by its name, it handle 3 things, you can say v2 only do one thing, which is gather the data
 * 2.1 if raw data and v1/v2(client) are in different place, this is more important since you always don't want to change the client code
 * 
 */
function getUserAndShopAndCarV2() {
  return {
    user: getUserData(),
    shop: getShopData(),
    car: getCarData()
  }
}

function getShopData() {
  return shopData
}

function getCarData() {
  return carData
}

function getUserData() {
  return userData
}

const userData = {
  name: '张三',
  age: 18,
  sex: '男'
}

const shopData = {
  name: '商城',
  add: '商城地址',
  desc: '商城描述'
}

const carData = {
  name: '小轿车',
  add: '小轿车地址',
  desc: '小轿车描述'
}

```






