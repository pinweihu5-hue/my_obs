

"方法引用" 是 lambda 表达式的简写形式，它直接引用现有的方法。

方法引用最基本的類型是"静态方法"的方法引用类型


**非静态方法"的方法引用类型:**

有四种：
- **构造器引用:**  引用类的构造器，例如 `String::new`。  这用于创建新对象。
- **数组构造器引用:**  引用数组的构造器，例如 `int[]::new`。  这用于创建新数组。
- **绑定实例方法引用:**  接收对象（调用方法的对象）在方法引用中已经指定。  
	- 例如：`String::length`，其中接收对象是隐式的 `String` 对象。  当你调用这个方法引用时，它会调用该 `String` 对象的 `length()` 方法。  它类似于静态方法引用，因为函数对象（方法引用）接受的参数与被引用方法的参数相同。
- **非绑定实例方法引用:**  接收对象在应用函数对象时才指定，作为方法参数之前的附加参数。 
	- **簡單說，就是對象透過參數提供**
	- 例如：`(str, i) -> str.substring(i)` 可以写成 `String::substring`。 
	- 在使用时，你需要提供 `String` 对象和 `int` 参数，例如 `someString::substring.apply(2)`。  
	- 在 Stream 管道中，非绑定引用常用作 `map` 和 `filter` 操作的参数。


**重点对比：绑定 vs. 非绑定实例方法引用**
* **绑定:** 接收对象已知，方法引用直接调用该对象的方法。  参数与被引用方法相同。
* **非绑定:** 接收对象在调用时才提供，作为额外的参数。


**例子:**
```java
import java.util.Arrays;
import java.util.List;
import java.util.function.BiFunction;
import java.util.function.Function;

public class MethodReferenceExample {
    public static void main(String[] args) {

		// arg is String[], 因此用 Arrays.asList 轉為 List<String>
        List<String> strings = Arrays.asList("hello", "world", "java");

        // 绑定实例方法引用 (String::length)
        strings.forEach(System.out::println); // 等价于 strings.forEach( s -> System.out.println(s) );
        int length = "test".length(); // 直接调用方法
        Function<String, Integer> stringLength = String::length; // 方法引用
        int length2 = stringLength.apply("test");


        // 非绑定实例方法引用 (String::substring)
        BiFunction<String, Integer, String> substring = String::substring;
        String sub = substring.apply("helloworld", 2); // 提供接收对象 "helloworld" 和参数 2
        String sub2 = "helloworld"::substring.apply(2); // 等价于上一个


        // 构造器引用 (String::new)
        Function<char[], String> stringConstructor = String::new;
        String newString = stringConstructor.apply(new char[]{'a', 'b', 'c'});


        // 数组构造器引用 (int[]::new)
        Function<Integer, int[]> intArrayConstructor = int[]::new;
        int[] newArray = intArrayConstructor.apply(5); // 创建长度为 5 的 int 数组
    }
}

```

**总结:**

绑定引用类似于静态引用，接收对象已知；
而非绑定引用需要在调用时提供接收对象作为额外的参数，在 Stream 的 `map` 和 `filter` 操作中很常见。


