
函数式接口是 Java 8 引入的一个重要概念，它是只有一个抽象方法的接口。  它可以有默认方法和静态方法，但必须只有一个抽象方法。  函数式接口是 Lambda 表达式和方法引用的基础。

**核心概念：**

* **单一抽象方法:** 函数式接口的核心特征是只有一个抽象方法。  这个方法定义了接口的功能。
* **`@FunctionalInterface` 注解 (可选):**  可以使用 `@FunctionalInterface` 注解来标记一个接口是函数式接口。  这有助于编译器进行检查，确保接口只有一个抽象方法。  即使没有这个注解，只要接口只有一个抽象方法，它仍然是函数式接口。
* **Lambda 表达式和方法引用:** 函数式接口的主要用途是作为 Lambda 表达式和方法引用的目标类型。  Lambda 表达式和方法引用提供了一种简洁的方式来实现函数式接口的抽象方法。

**例子：**

```java
@FunctionalInterface
interface MyFunction {
    int apply(int a, int b); // 只有一個抽象方法

    default int square(int x) { // 默认方法
        return x * x;
    }

    static int add(int a, int b) { // 静态方法
        return a + b;
    }
}

public class Main {
    public static void main(String[] args) {
        // 使用 Lambda 表达式实现函数式接口
        MyFunction add = (a, b) -> a + b;
        int result = add.apply(5, 3); // 调用 Lambda 表达式
        System.out.println(result); // 输出 8

        // 使用方法引用实现函数式接口
        MyFunction multiply = Math::multiplyExact;
        int result2 = multiply.apply(5, 3); // 调用方法引用
        System.out.println(result2); // 输出 15

        // 调用默认方法
        int square = add.square(4);
        System.out.println(square); // 输出 16

        // 调用静态方法
        int sum = MyFunction.add(2, 3);
        System.out.println(sum); // 输出 5
    }
}
```

**常见的函数式接口：**

Java 提供了许多预定义的函数式接口，位于 `java.util.function` 包中，例如：

* `Predicate<T>`:  接受一个参数并返回一个 boolean 值。
* `Consumer<T>`:  接受一个参数，不返回值。
* `Function<T, R>`:  接受一个参数并返回一个结果。
* `Supplier<T>`:  不接受参数，返回一个结果。
* `BinaryOperator<T>`:  接受两个参数并返回一个结果，参数和结果的类型相同。

**何时使用函数式接口：**

* **使用 Lambda 表达式和方法引用:**  函数式接口是 Lambda 表达式和方法引用的目标类型。  它们使得代码更简洁易读。
* **回调函数:**  函数式接口可以作为回调函数传递给其他方法，例如事件处理程序。
* **Stream API:**  Java Stream API 广泛使用函数式接口，例如 `map`, `filter`, `reduce` 等操作。
* **定义 API:**  可以使用函数式接口来定义 API，使 API 更灵活和易于使用。


**何时不应使用函数式接口：**

* **需要多个抽象方法:**  如果接口需要多个抽象方法，就不能使用函数式接口。  需要使用普通的接口或抽象类。
* **需要定义状态:**  如果接口需要维护状态，函数式接口可能不太合适。  函数式接口通常用于表示无状态的操作。  虽然可以通过将状态封装在 Lambda 表达式的捕获变量中来实现，但这可能会使代码更复杂。
* **复杂的逻辑:**  对于非常复杂的逻辑，使用函数式接口和 Lambda 表达式可能会使代码难以理解和调试。  在这种情况下，使用传统的类和方法可能更合适。


**总结：**

函数式接口是 Java 8 的一个重要特性，它使得代码更简洁易读，并促进了函数式编程风格在 Java 中的应用。  理解函数式接口的概念对于有效地使用 Lambda 表达式、方法引用和 Stream API 至关重要。  但是，也需要注意函数式接口的局限性，并根据具体情况选择合适的编程方式。
