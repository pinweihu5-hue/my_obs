繼承規則的限制

對使用者來說，要求繼承出來的子類的不同接口行為的一致性和穩定性

實現 SRP的 方法



small talk 開始了繼承，可以讓組件之間有關係
![[IMG-SOLID_LSP 里氏替換-20250807202920638.png|822]]

# LSP as 繼承規則
- 建立繼承前，先請思考:
    - 先盡量用組合，不要用繼承
    - 真的要多態去繼承的話，用抽象和 interface 來解耦
    - 繼承要符合 LSP: 實作的子類們之間，要可以彼此替換
    - 不然就不要用
- 定義? 所有使用 parent class 的地方，都可以使用其 child class
- 如何做到? 你只要都是面相 抽象(abstract) 和 接口 (interface) 去 extends, 那就會符合
- 如何知道沒做到？ 如果你看到 code 需要用到 isInstanceOf 之類的方法去判斷，然後需要轉型(casting)等等就是違背了

example
```js fold
// base class
public abstract class Shape {
    public abstract void draw();
}


// Rectangle kid
public class Rectangle extends Shape {
    @Override
    public void draw() {
        System.out.println("绘制矩形");
    }
}


// Triangle kid
public class Triangle extends Shape {
    @Override
    public void draw() {
        System.out.println("绘制三角形");
    }
}


private static void justDraw(Shape shape){
    System.out.println("开始画图");
    shape.draw();
    System.out.println("结束画图");
}

public static void main(String[] args) {    
    //  justDraw 的參數定義是 Shape, 但我們也可以放入 Shape 的子類，也是可以 work, 表示符合 LSP 
    //  可以 work 是因為子類也要實作 draw
    justDraw(new Triangle());
}


```

這個用 abstract class 當做例子
一樣的概念也套用在 interface 上





# 擴展解釋


design pattern 也有體驗出來
客戶端使用的那邊依賴的那個穩定的 interface 都應該跟當初跟客戶承諾的一樣
除非客戶需要調整需求，這樣那個就會動到，不然應該都一樣


src: /Users/re4388/project/personal/nodets/ts-playground-0/src/design_pattern/strategy1.ts
```ts fold

/**
 * 目的是 open-closed
 * 加上新的功能，且不會動的原本的code!
 * 下面的例子，你可以增加新的payment, 但是你不會動的原本的邏輯
 * 類似，你可以加上一個 MoMoPay試試看！
 */

/**
 * 關鍵概念
 *
 * 你想要可以保持彈性的那個維度，建立一個共有的 interface
 * 以下面的例子來說， that's Payment
 *
 * 然後你如果有新需求，建立新的 class 去 implement 這個 interface
 *
 * 你要用的使用，"注入" or "set" 這個 新的class 即可
 * 下面的 code 是說 set的方式
 *
 */

// 0. 定義 interface
interface Payment {
  pay(amount: number): void
}

// 1. 根據 interface 去實作不同的策略
class CreditCard implements Payment {
  pay(amount: number): void {
    console.log(`Paid ${amount} using credit card.`)
  }
}

class PayPalPay implements Payment {
  pay(amount: number): void {
    console.log(`Paid ${amount} using PayPal.`)
  }
}

// 這個就是 client code
// 依賴穩定的 Payment
// 實作可以替換掉，變成不同類型的 payment
// 但是 Checkout 都不會需要調整
class Checkout {
  // private payment: Payment

  constructor(private payment: Payment) {
    // this.payment = payment
  }

  setPayment(payment: Payment) {
    this.payment = payment
  }

  public pay(amount: number): void {
    this.payment.pay(amount)
  }
}

// 3. 根據情況，想要用那種payment都可以用
const payPal = new PayPalPay()
const checkout = new Checkout(payPal)
checkout.pay(50)

const creditCard = new CreditCard()
const checkout1 = new Checkout(creditCard)
checkout1.pay(50)

export {}


```


這個例子也顯示出 DI 原則
Checkout 依賴 payment 去使用
creditCard and paypalpay 都依賴 payment 去實作



![[IMG-SOLID_LSP 里氏替換-20250807202921817.png|922]]



![[IMG-SOLID_LSP 里氏替換-20250807202923135.png|882]]


![[IMG-SOLID_LSP 里氏替換-20250807202924505.png|881]]