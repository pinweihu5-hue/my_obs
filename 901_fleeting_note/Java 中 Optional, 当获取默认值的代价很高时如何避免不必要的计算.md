这段文字解释了 Java 中 `Optional` 类的高级用法，特别是当获取默认值的代价很高时如何避免不必要的计算。

**`Optional` 的基本用法回顾:**

`Optional` 用于表示可能存在或不存在的值，可以避免 `NullPointerException`。  

常用的方法有 `orElse(T other)`，它返回 `Optional` 中的值（如果存在），否则返回提供的默认值 `other`。



**`orElseGet(Supplier<? extends T> other)` 的作用：延迟计算默认值**

`orElseGet` 方法接受一个 `Supplier<T>` 作为参数。`Supplier` 是一个函数式接口，它不接受任何参数并返回一个值。  `orElseGet` 的关键在于它只会在 `Optional` 为空时才调用 `Supplier` 来获取默认值。  如果 `Optional` 中存在值，`Supplier` 就不会被执行，从而避免了潜在的昂贵计算。

**为什么 `orElseGet` 应该被称为 `orElseCompute`:**

PS: Effective Java 作者认为 `orElseGet` 应该被称为 `orElseCompute`，因为它与 `Map` 接口中的 `computeIfAbsent`、`computeIfPresent` 和 `compute` 方法类似，这些方法都涉及到根据条件计算值。


**`Optional` 的其他高级方法:**

除了 `orElse` 和 `orElseGet`，`Optional` 还提供了其他一些更高级的方法，用于处理更复杂的场景：

* **`filter(Predicate<? super T> predicate)`:**  如果 `Optional` 中的值存在并且满足给定的谓词，则返回包含该值的 `Optional`，否则返回空的 `Optional`。
* **`map(Function<? super T, ? extends U> mapper)`:** 如果 `Optional` 中的值存在，则将该值应用于给定的函数，并返回包含结果的 `Optional`，否则返回空的 `Optional`。
* **`flatMap(Function<? super T, Optional<U>> mapper)`:**  与 `map` 类似，但是映射函数返回的是一个 `Optional` 对象。  这可以避免嵌套的 `Optional`。
* **`ifPresent(Consumer<? super T> consumer)`:** 如果 `Optional` 中的值存在，则将该值作为参数传递给给定的消费者，否则不执行任何操作。
* **`or(Supplier<? extends Optional<? extends T>> supplier)` (Java 9+)**: 如果 `Optional` 包含值，则返回该 `Optional`；否则，调用提供的 `supplier` 并返回其结果。
* **`ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction)` (Java 9+)**: 如果 `Optional` 包含值，则将值传递给 `action`；否则，执行 `emptyAction`。


**总结:**

当获取默认值的代价很高时，应该使用 `orElseGet` 方法来避免不必要的计算。  `Optional` 类还提供了其他一些高级方法，可以根据具体的需求选择使用。  如果基本方法无法满足需求，应该查阅这些高级方法的文档，看看它们是否能够完成任务。


以下是一个使用 Optional 和 flatMap 的具体例子，以及不使用 flatMap 的对比，以便更好地理解其作用。

**场景：** 假设你有一个 `Person` 类和一个 `Car` 类，一个人可能有一辆车也可能没有。你需要获取一个人的车的颜色。

**代码 (使用 flatMap):**

```java
import java.util.Optional;

class Person {
    Optional<Car> car;

    public Person(Optional<Car> car) {
        this.car = car;
    }

    public Optional<Car> getCar() {
        return car;
    }
}

class Car {
    String color;

    public Car(String color) {
        this.color = color;
    }

    public String getColor() {
        return color;
    }
}

public class OptionalFlatMapExample {
    public static void main(String[] args) {
        Person personWithCar = new Person(Optional.of(new Car("red")));
        Person personWithoutCar = new Person(Optional.empty());

        // `personWithCar.getCar()` 返回一个包含 `Car` 对象的 `Optional`
        Optional<String> carColor1 = personWithCar.getCar().flatMap(car -> Optional.ofNullable(car.getColor()));
        Optional<String> carColor2 = personWithoutCar.getCar().flatMap(car -> Optional.ofNullable(car.getColor()));

        System.out.println("Person 1 car color: " + carColor1.orElse("No car")); // 输出: red
        System.out.println("Person 2 car color: " + carColor2.orElse("No car")); // 输出: No car
    }
}
```

**解释：**

1. `personWithCar.getCar()` 返回一个包含 `Car` 对象的 `Optional`。
2. `flatMap(car -> Optional.ofNullable(car.getColor()))`  将 `Optional<Car>` 转换为 `Optional<String>`。 
	- `flatMap` 接收一个函数作为参数，这个函数将 `Car` 对象转换为 `Optional<String>`。  
	- 如果 `Car` 对象存在，则返回包含颜色的 `Optional`；如果 `Car` 对象为 `null` 或 `getColor()` 返回 `null`，则返回空的 `Optional`。 
	- 关键在于 `flatMap` 将嵌套的 `Optional`（`Optional<Optional<String>>`）扁平化成了 `Optional<String>`。
1. `orElse("No car")`  如果 `Optional` 为空，则返回 "No car"。


**代码 (不使用 flatMap，使用 map):**

```java
Optional<String> carColor1 = personWithCar.getCar().map(car -> car.getColor()).orElse("No car"); // 可以正常工作
Optional<String> carColor2 = personWithoutCar.getCar().map(car -> car.getColor()).orElse("No car"); // 抛出 NullPointerException
```


**解释：**
如果使用 `map`，当 `personWithoutCar.getCar()` 返回空 `Optional` 时，`map(car -> car.getColor())` 会抛出 `NullPointerException`，因为 `car` 为 `null`。  因此，必须在 `map` 之后再套一层 `Optional` 处理，再使用 `flatMap` 来解掉一層的 `Optional`。

