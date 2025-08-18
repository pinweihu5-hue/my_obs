`peek`、`skip` 和 `unordered` 都是 Java Stream 的中間操作，它們分別用於不同的目的：

**1. `peek(Consumer<T> action)`:**

* **用途:**  `peek` 操作主要用於**除錯**和**監控** Stream 管道中的元素。它會對 Stream 中的每個元素執行指定的動作 (由 `Consumer<T>` 定義)，但**不修改元素本身**。  `peek` 操作會返回一個新的 Stream，其中包含與原始 Stream 相同的元素。

* **範例:**

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

names.stream()
    .peek(name -> System.out.println("Processing: " + name)) // 印出正在處理的元素
    .map(String::toUpperCase)
    .forEach(System.out::println);
```

* **注意事項:**  `peek` 操作的動作不應該修改 Stream 中的元素，也不應該有副作用 (例如修改外部狀態)。  它主要用於觀察 Stream 的行為。


**2. `skip(long n)`:**

* **用途:**  `skip` 操作會**跳過** Stream 中的前 `n` 個元素，並返回一個新的 Stream，其中包含剩餘的元素。  如果 `n` 大於或等於 Stream 的大小，則返回一個空的 Stream。

* **範例:**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

numbers.stream()
    .skip(2) // 跳過前兩個元素
    .forEach(System.out::println); // 輸出 3, 4, 5
```


**3. `unordered()`:**

* **用途:**  `unordered()` 操作會提示 Stream **放棄遇到操作的順序**。 對於某些操作（例如 `distinct`），如果不需要維持遇到順序，放棄遇到順序可以提高效能。  這個操作主要用於**優化** Stream 的執行，特別是在並行操作的情況下。

* **範例:**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 2, 1);

numbers.stream()
    .unordered() // 提示 Stream 放棄遇到順序
    .distinct() // 去除重複元素
    .forEach(System.out::println); // 輸出順序不確定，但只會包含 1, 2, 3
```

* **注意事項:**  `unordered()` 操作本身並不保證 Stream 會以非排序的方式執行。  它只是向 Stream 提供一個提示，Stream 可以根據具體情況決定是否利用這個提示進行優化。  對於順序敏感的操作 (例如 `limit`、`skip`)，`unordered()` 操作不會有任何影響。



總而言之，`peek` 用於除錯和監控，`skip` 用於跳過元素，`unordered` 用於提示 Stream 放棄遇到順序以提高效能。  理解它們的用途可以幫助你更有效地使用 Java Stream API。
