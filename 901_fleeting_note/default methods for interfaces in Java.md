## Java 介面中的預設方法 (Default Methods) 完整介紹

Java 8 引入了介面預設方法，允許在介面中提供方法的預設實現。這項特性在不破壞現有程式碼的情況下，為介面添加新的方法提供了很大的靈活性。

**語法：**

```java
interface MyInterface {
    void abstractMethod(); // 抽象方法

    default void defaultMethod() {
        System.out.println("This is a default method.");
    }
}
```

使用 `default` 關鍵字來宣告預設方法。

**例子：**

```java
interface Formula {
    double calculate(int a);

    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}

public class DefaultMethodsExample {
    public static void main(String[] args) {
        Formula formula = new Formula() {
            @Override
            public double calculate(int a) {
                return sqrt(a * 100);
            }
        };

        System.out.println(formula.calculate(100)); // 100.0
        System.out.println(formula.sqrt(16)); // 4.0
    }
}
```

**何時使用預設方法：**

* **演進介面：**  在不破壞現有程式碼的情況下，向介面添加新的方法。  如果直接在介面中添加抽象方法，所有實現該介面的類別都必須實現這個新方法，否則會編譯錯誤。  使用預設方法，則可以提供一個預設實現，現有類別無需修改即可編譯通過。
* **提供便捷方法：**  為介面的使用者提供一些常用的便捷方法。
* **多重繼承的行為：**  雖然 Java 不支援多重繼承類別，但預設方法允許一個類別繼承多個介面，並從多個介面繼承預設方法。  這在一定程度上模擬了多重繼承的功能。


**何時不應該使用預設方法：**

* **破壞 Liskov 替換原則：**  如果預設方法的行為與某些實現類別的預期行為不一致，可能會破壞 Liskov 替換原則。  應仔細考慮預設方法的設計，確保它不會與現有實現衝突。
* **介面設計過於複雜：**  如果介面中包含大量的預設方法，可能會使介面變得臃腫和難以理解。  應盡量保持介面的簡潔性，只包含必要的抽象方法和預設方法。
* **存在更好的替代方案：**  例如，如果可以使用靜態方法或輔助類別來實現相同的功能，則應優先考慮這些方案。


**多重繼承的預設方法衝突：**

如果一個類別繼承自多個介面，並且這些介面包含具有相同簽章的預設方法，則會發生衝突。  需要在類別中覆寫這個方法，明確指定使用哪個介面的預設方法，或者提供自己的實現。


**總結：**

預設方法是 Java 8 的一個重要特性，它提高了介面的靈活性和可演進性。  使用預設方法可以方便地向介面添加新功能，而無需修改現有程式碼。  然而，在使用預設方法時，需要仔細考慮其設計和潛在的衝突，以確保程式碼的正確性和可維護性。  並非所有情況都適合使用預設方法，有時使用靜態方法或輔助類別可能是更好的選擇。
