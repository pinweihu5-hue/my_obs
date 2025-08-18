
Java Lambda 表達式是 Java 8 引入的一個重要特性，它提供了一種簡潔的方式來表示函數式介面（Functional Interface）的實例。函數式介面是只包含一個抽象方法的介面。Lambda 表達式允許您直接編寫函數的實現，而無需定義一個新的類別或使用匿名內部類別。

**語法：**

Lambda 表達式的基本語法如下：

```java
(parameters) -> expression

// 或

(parameters) -> { statements; }
```

* `parameters`:  方法的參數列表，可以省略參數類型，如果只有一個參數，也可以省略括號。
* `->`:  Lambda 運算符，將參數列表和表達式或語句塊分開。
* `expression`:  單個表達式，其結果將作為 Lambda 表達式的返回值。
* `statements`:  多個語句，包含在大括號中，類似於方法體。如果需要返回值，則必須使用 `return` 語句。


**例子：**

```java
// 函數式介面
interface MyInterface {
    int calculate(int a, int b);
}

public class LambdaExample {
    public static void main(String[] args) {

        // 使用 Lambda 表達式實現 MyInterface
        MyInterface add = (a, b) -> a + b;
        MyInterface subtract = (int a, int b) -> a - b;
        MyInterface multiply = (a, b) -> { return a * b; };

        System.out.println(add.calculate(5, 3)); // 輸出 8
        System.out.println(subtract.calculate(5, 3)); // 輸出 2
        System.out.println(multiply.calculate(5, 3)); // 輸出 15


        // 使用 Runnable 介面 (也是函數式介面)
        Runnable runnable = () -> System.out.println("Hello from Lambda!");
        new Thread(runnable).start();
    }
}
```

**何時使用 Lambda 表達式：**

* **簡化程式碼:**  Lambda 表達式可以使程式碼更簡潔易讀，尤其是在處理簡單的函數式介面時。
* **函數式程式設計:**  Lambda 表達式是函數式程式設計的基礎，可以方便地進行函數的傳遞和組合。
* **串流 API:**  Java 8 的 Stream API 與 Lambda 表達式完美結合，可以簡潔高效地處理集合數據。
* **事件處理:**  Lambda 表達式可以簡化事件處理器的編寫。


**何時不應該使用 Lambda 表達式：**

* **複雜邏輯:**  如果函數的邏輯非常複雜，包含大量的語句和控制流程，使用 Lambda 表達式可能會降低程式碼的可讀性。此時，使用傳統的類別或方法定義可能更清晰。
* **需要除建構函式以外的建構函式:** Lambda 表達式本身不能定義建構函式。
* **狀態維護:**  Lambda 表達式不適合維護狀態，如果需要在多次調用之間保存狀態，應該使用傳統的類別或方法。
* **除錯困難:**  Lambda 表達式的除錯可能會比傳統方法更困難，因為它們沒有名稱，堆疊追蹤信息可能不夠清晰。  雖然現代 IDE 已大幅改善此問題，但仍需注意。


**與匿名內部類別的比較：**

* Lambda 表達式更簡潔，語法更精簡。
* Lambda 表達式沒有名稱，而匿名內部類別可以有名稱（雖然通常不使用）。
* Lambda 表達式不能定義建構函式，而匿名內部類別可以使用實例初始化塊。
* Lambda 表達式可以更有效地與 Java 8 的 Stream API 結合使用。

**總結：**

Lambda 表達式是 Java 的一個強大特性，可以簡化程式碼並提高開發效率。  理解其優缺點並合理使用，可以使您的 Java 程式碼更優雅和高效。  在邏輯複雜或需要維護狀態的情況下，仍應考慮使用傳統的類別或方法。


---




[(811) Lambda Expressions in Java - Full Simple Tutorial - YouTube](https://www.youtube.com/watch?v=tj5sLSFjVj4&list=WL&index=17)



這個函數吃 Printable interface
然後你應該會有一個 class impl this interface, 這個函數可以吃這個 obj, 然後呼叫這個 obj impl 的print


這樣比較麻煩，因為你需要, 建立 一個 class impl this method
![[IMG-Java Lambda-20250805201156827.png]]


或是我們用lambda
不需要建立函數
直接用 impl
![[IMG-Java Lambda-20250805201157516.png]]

one line, remove { }
![[IMG-Java Lambda-20250805201158114.png]]

也可以抽出來，如果很複雜
這個lambda 的type 就是 Printable
![[IMG-Java Lambda-20250805201159203.png]]


say 我們加上 arg
![[IMG-Java Lambda-20250805201200089.png]]

也是可以
![[IMG-Java Lambda-20250805201200971.png]]
![[IMG-Java Lambda-20250805201201657.png]]



關鍵!!

lambda 的本質就是可以使用這種 @FunctionalInterface
這種只能放一個 abstract method (你可以放static 等，但是就是抽象函數只能放一個，然後要給 lambda用的)

你也可以不加 @FunctionalInterface, 這會自動加上去

如果你放兩個 抽象函數，使用 lambda 那邊就會噴掉
![[IMG-Java Lambda-20250805201202234.png|1002]]