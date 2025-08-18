## Java 匿名內部類別 (Anonymous Inner Classes) 完整介紹

匿名內部類別是 Java 中一種沒有名稱的內部類別。它們通常用於在需要建立類別實例但又不想顯式定義新類別的情況下。它們本質上是**一次性**的類別，直接在需要使用的地方定義和實例化。

**定義和用法：**

匿名內部類別通常用於實現介面或擴展抽象類別。它們的語法如下：

```java
// 實現介面
interface MyInterface {
    void doSomething();
}

MyInterface myObject = new MyInterface() {
    @Override
    public void doSomething() {
        System.out.println("Doing something in anonymous class");
    }
};

// 擴展抽象類別
abstract class MyAbstractClass {
    abstract void doSomething();
}

MyAbstractClass myObject2 = new MyAbstractClass() {
    @Override
    void doSomething() {
        System.out.println("Doing something in anonymous class extending abstract class");
    }
};

myObject.doSomething();
myObject2.doSomething();
```

**何時使用匿名內部類別：**

* **簡化程式碼:**  當只需要一個類別的單一實例，且該類別的邏輯相對簡單時，使用匿名內部類別可以避免定義一個新的具名類別，從而減少程式碼量並提高可讀性。
* **事件處理:**  在 GUI 程式設計中，匿名內部類別經常用於處理事件，例如按鈕點擊或滑鼠移動。
* **覆寫方法:**  可以利用匿名內部類別快速覆寫父類別或介面的方法，而無需建立一個新的具名類別。
* **局部作用域:** 匿名內部類別只能在其定義的作用域內訪問，這有助於提高程式碼的封裝性和安全性。


**例子：**

```java
import javax.swing.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class AnonymousInnerClassExample {

    public static void main(String[] args) {
        JFrame frame = new JFrame("Anonymous Inner Class Example");
        JButton button = new JButton("Click me");

        // 使用匿名內部類別處理按鈕點擊事件
        button.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                JOptionPane.showMessageDialog(frame, "Button clicked!");
            }
        });

        frame.add(button);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.pack();
        frame.setVisible(true);
    }
}
```

**何時不應該使用匿名內部類別：**

* **複雜邏輯:**  如果類別的邏輯很複雜，或者需要在多個地方重複使用，那麼最好定義一個具名的類別，以便更好地組織和維護程式碼。
* **需要命名:**  如果需要在其他地方引用該類別，例如將其作為參數傳遞給其他方法，則必須使用具名類別。
* **需要除建構函式以外的建構函式:** 匿名內部類別只能使用實例初始化塊 (instance initializer block) 來初始化，不能定義建構函式。 如果需要多個建構函式或複雜的初始化邏輯，則應使用具名類別。
* **需要實作多個介面 (Java 8 以前):** 在 Java 8 之前，匿名內部類別只能擴展一個類別或實現一個介面。 如果需要實現多個介面，則必須使用具名類別。 (Java 8 之後可以使用 lambda 表達式來解決這個問題)


**總結:**

匿名內部類別是 Java 中一個強大的特性，可以簡化程式碼並提高可讀性。 但是，在使用匿名內部類別時，需要仔細考慮其適用場景，避免濫用導致程式碼難以理解和維護。  如果類別的邏輯複雜或需要重複使用，最好定義一個具名的類別。


希望這個解釋對您有所幫助！



[(811) Java Anonymous Inner Classes Explained in 6 Minutes - YouTube](https://www.youtube.com/watch?v=DwtPWZn6T1A&list=WL&index=10)


當你需要 one-shot class 使用

一種是透過 implcit extend
![[IMG-Java Anonymous Inner Classes-20250805201152342.png]]


一種是 thru interface inheritance
下面 Runnable 是 interface
你用這個語法， java 會知道你會實作這個 interface 裡面的 method
這邊就是 run
![[IMG-Java Anonymous Inner Classes-20250805201153198.png]]