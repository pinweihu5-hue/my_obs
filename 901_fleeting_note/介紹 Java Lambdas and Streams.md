
這兩者是 Java 8 引入的強大特性，它們極大地改變了 Java 的程式設計風格，讓程式碼更簡潔、更具表達力，並且更容易進行並行處理。

**Lambda 表達式 (Lambdas)**

**什麼是 Lambda 表達式？**

Lambda 表達式是一種匿名函式，它可以被當作物件傳遞。Lambda 表達式允許你將行為 (程式碼區塊) 作為參數傳遞給方法，或者將行為儲存在變數中。

**Lambda 表達式的語法：**

```java
(parameters) -> expression
```

或

```java
(parameters) -> { statements; }
```

*   **`(parameters)`:** 參數列表，可以為空，也可以包含一個或多個參數。參數的型別可以省略，編譯器會根據上下文推斷。
*   **`->`:** Lambda 運算子，用於分隔參數列表和 Lambda 表達式的主體。
*   **`expression` 或 `{ statements; }`:** Lambda 表達式的主體，可以是一個單一的表達式，也可以是一個程式碼區塊。

**Lambda 表達式的用途：**

1.  **簡化匿名內部類：**
    *   在 Java 8 之前，如果需要將行為作為參數傳遞，通常需要使用匿名內部類。Lambda 表達式可以簡化這個過程，讓程式碼更簡潔。

    ```java
    // 使用匿名內部類
    Runnable r1 = new Runnable() {
        @Override
        public void run() {
            System.out.println("Hello from anonymous inner class");
        }
    };

    // 使用 Lambda 表達式
    Runnable r2 = () -> System.out.println("Hello from lambda");
    ```

2.  **函數式介面 (Functional Interfaces)：**
    *   Lambda 表達式通常與函數式介面一起使用。函數式介面是指只包含一個抽象方法的介面。
    *   Java 提供了許多內建的函數式介面，例如 `Runnable`, `Comparator`, `Predicate`, `Function`, `Consumer`, `Supplier` 等。

    ```java
    // 使用 Comparator 介面
    List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
    Collections.sort(names, (s1, s2) -> s1.compareTo(s2)); // 使用 Lambda 表達式
    ```

3.  **作為方法參數傳遞行為：**
    *   Lambda 表達式可以作為方法參數傳遞，讓方法可以接收不同的行為。
    ```java
    
    public static void process(List<Integer> numbers, Predicate<Integer> condition) {
        for (int number : numbers) {
            if (condition.test(number)) {
                System.out.println(number);
            }
        }
    }

    // 使用 Lambda 表達式作為參數
    List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
    process(numbers, n -> n % 2 == 0); // 列印偶數
    process(numbers, n -> n > 3); // 列印大於 3 的數
    ```

**串流 (Streams)**

**什麼是串流？**

串流 (Stream) 是 Java 8 引入的一種新的抽象概念，它代表一個資料序列，可以對這個序列進行各種操作，例如過濾、映射、排序、聚合等。串流本身不儲存資料，它只是對資料進行處理的一個管道。

**串流的特性：**
*   **非修改性：** 串流操作不會修改原始資料來源。
*   **惰性求值：** 串流操作只有在需要結果時才會執行。
*   **管道式操作：** 串流操作可以鏈式調用，形成一個操作管道。
*   **支援並行處理：** 串流可以很容易地進行並行處理，提高處理效率。

**串流的操作類型：**
1.  **中間操作 (Intermediate Operations)：**
    *   返回一個新的串流，可以鏈式調用。
    *   例如：`filter()`, `map()`, `sorted()`, `distinct()`, `limit()`, `skip()` 等。

2.  **終端操作 (Terminal Operations)：**
    *   產生一個結果或副作用，終止串流操作。
    *   例如：`forEach()`, `collect()`, `reduce()`, `count()`, `anyMatch()`, `allMatch()`, `noneMatch()`, `findFirst()`, `findAny()` 等。

**串流的使用範例：**

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class StreamExample {
    public static void main(String[] args) {
    
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David", "Eve");

        // 過濾出以 "C" 開頭的名字，並轉換為大寫
        List<String> filteredNames = names.stream()
                .filter(name -> name.startsWith("C"))
                .map(String::toUpperCase)
                .collect(Collectors.toList());
        System.out.println("Filtered names: " + filteredNames); // Output: [CHARLIE]

        // 計算所有名字的長度總和
        int totalLength = names.stream()
                .mapToInt(String::length)
                .sum();
        System.out.println("Total length of names: " + totalLength); // Output: 21

        // 找出名字長度最長的名字
        String longestName = names.stream()
                .reduce((s1, s2) -> s1.length() > s2.length() ? s1 : s2)
                .orElse("");
        System.out.println("Longest name: " + longestName); // Output: Charlie

        // 並行處理
        long count = names.parallelStream()
                .filter(name -> name.length() > 3)
                .count();
        System.out.println("Count of names with length > 3 (parallel): " + count); // Output: 3
    }
}
```

**程式碼解釋：**

*   **`names.stream()`:** 建立一個串流。
*   **`filter(name -> name.startsWith("C"))`:** 過濾出以 "C" 開頭的名字。
*   **`map(String::toUpperCase)`:** 將名字轉換為大寫。
*   **`collect(Collectors.toList())`:** 將串流中的元素收集到一個 List 中。
*   **`mapToInt(String::length)`:** 將字串轉換為長度 (int)。
*   **`sum()`:** 計算所有長度總和。
*   **`reduce((s1, s2) -> s1.length() > s2.length() ? s1 : s2)`:** 找出最長的名字。
*   **`orElse("")`:** 如果串流為空，則返回空字串。
*   **`names.parallelStream()`:** 建立一個並行串流。
*   **`count()`:** 計算符合條件的元素個數。

**Lambda 和 Stream 的優點：**

*   **簡潔的程式碼：** Lambda 表達式和串流可以讓程式碼更簡潔、更具表達力。
*   **函數式程式設計：** 它們鼓勵函數式程式設計風格，讓程式碼更易於理解和維護。
*   **並行處理能力：** 串流可以輕鬆地進行並行處理，提高處理效率。
*   **更強大的資料處理能力：** 串流提供了豐富的操作，可以方便地對資料進行各種處理。

**總結：**

Lambda 表達式和串流是 Java 8 中引入的強大特性，它們讓 Java 程式碼更簡潔、更具表達力，並且更容易進行並行處理。掌握 Lambda 表達式和串流對於現代 Java 開發至關重要。

