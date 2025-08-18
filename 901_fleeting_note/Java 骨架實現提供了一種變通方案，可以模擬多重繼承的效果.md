

**核心思想：**

Java 不支援多重繼承，也就是一個類別不能繼承多個類別。但是，有時候我們希望一個類別可以同時擁有多個類別的功能。骨架實現提供了一種變通方案，可以模擬多重繼承的效果。

**方法：**

1. **介面:** 定義所需的功能。
2. **骨架實現 (抽象類別):**  實現介面，並提供部分方法的預設實現。
3. **私有內部類別:**  在實現介面的類別中，創建一個私有的內部類別，繼承自骨架實現。
4. **轉發方法調用:**  實現介面的類別將介面方法的調用轉發給私有內部類別的實例。

**例子 (簡化版):**

```java
interface Flyer {
    void fly();
}

abstract class AbstractFlyer implements Flyer {
    // 這邊可能有很多通用的方法已經實作好了
}

interface Singer {
    void sing();
}

class Bird implements Flyer, Singer {
    private final Flyer flyerImpl = new PrivateFlyer(); // 私有內部類別實例


    // 私有內部類, 繼承自骨架實現
    private class PrivateFlyer extends AbstractFlyer {
        // 可以根據 Bird 的特性覆寫 fly()
        @Override
        public void fly() {
            System.out.println("Bird is flying");
        }
    }

    @Override
    public void fly() {
        flyerImpl.fly(); // 轉發方法調用
    }

    @Override
    public void sing() {
        System.out.println("Bird is singing");
    }
}
```

**與裝飾器模式的關係：**

這種技術與裝飾器模式很相似。兩者都使用了組合而不是繼承來擴展功能。不同之處在於，裝飾器模式主要用於動態地添加功能，而骨架實現則更側重於提供介面的預設實現，並模擬多重繼承。

**優點：**
* **避免多重繼承的複雜性和問題：**  例如菱形繼承問題。
* **提供介面的預設實現：**  減少程式碼重複。
* **靈活的擴展性：**  子類別可以根據需要覆寫預設方法。


**總結：**

通過私有內部類別繼承骨架實現，並將介面方法調用轉發給內部類別實例，可以有效地模擬多重繼承，並獲得多重繼承的大部分優點，同時避免其缺點。  這是一種在 Java 中實現複雜介面或模擬多重繼承的有效策略。


