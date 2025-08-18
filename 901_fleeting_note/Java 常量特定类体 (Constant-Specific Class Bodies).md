```java

// Enum type with constant-specific class bodies and data

public enum Operation {

    PLUS("+") {
        public double apply(double x, double y) { return x + y; }
    },
    
    MINUS("-") {
        public double apply(double x, double y) { return x - y; }
    },
    
    TIMES("*") {
        public double apply(double x, double y) { return x * y; }
    },
    
    DIVIDE("/") {
        public double apply(double x, double y) { return x / y; }
    };

    private final String symbol;

    Operation(String symbol) { this.symbol = symbol; }

    @Override
    public String toString() { return symbol; }

    public abstract double apply(double x, double y);
}

```

这段代码展示了如何利用 Java 枚举类型的强大功能，为每个枚举常量定义特定的行为和数据。  它定义了一个名为 `Operation` 的枚举类型，表示不同的数学运算。

**核心概念：常量特定类体 (Constant-Specific Class Bodies)**

每个枚举常量（PLUS, MINUS, TIMES, DIVIDE）都有自己的类体，就像一个匿名内部类一样。  这使得你可以为每个常量定义特定的方法实现。  在这个例子中，每个常量都覆写了 `apply()` 方法，以执行相应的数学运算。

**代码分解：**

1. **枚举常量定义:**  `PLUS("+")`, `MINUS("-")` 等定义了枚举常量，并在构造函数中传入对应的符号字符串。

2. **常量特定类体:**  每个枚举常量后面跟着一个代码块 `{}`，这就是常量特定类体。  在这个代码块中，可以定义字段、方法和构造函数，就像在一个普通的类中一样。  在这个例子中，每个常量都覆写了抽象方法 `apply(double x, double y)`，提供了具体的运算实现。

3. **私有字段 `symbol`:**  `private final String symbol;` 声明了一个私有 final 字段 `symbol`，用于存储每个运算符的符号字符串。

4. **构造函数 `Operation(String symbol)`:**  枚举类型的构造函数用于初始化 `symbol` 字段。  注意，枚举类型的构造函数必须是私有的（private）。

5. **覆写 `toString()` 方法:**  `@Override public String toString() { return symbol; }` 覆写了 `toString()` 方法，返回运算符的符号字符串。

6. **抽象方法 `apply(double x, double y)`:**  `public abstract double apply(double x, double y);` 声明了一个抽象方法 `apply()`，用于执行数学运算。  每个枚举常量都必须实现这个方法。

**使用方法：**

```java
public static void main(String[] args) {
    double x = 10.0;
    double y = 2.0;

    for (Operation op : Operation.values()) {
        System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
    }
}
```

**输出：**

```
10.000000 + 2.000000 = 12.000000
10.000000 - 2.000000 = 8.000000
10.000000 * 2.000000 = 20.000000
10.000000 / 2.000000 = 5.000000
```

**优势：**

* **简洁性:**  将数据和行为封装在一起，使代码更简洁易懂。
* **类型安全:**  避免了使用 `int` 常量或字符串表示运算类型可能带来的类型错误。
* **可扩展性:**  可以轻松地添加新的运算，而无需修改现有代码。


这个例子展示了 Java 枚举类型的强大功能，它不仅仅是一组命名的常量，还可以拥有更丰富的行为和数据。  这种特性使得枚举类型非常适合表示状态机、策略模式和其他需要将数据和行为结合在一起的场景。
