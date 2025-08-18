在 Java Stream API 中，操作分為兩種：中間操作 (Intermediate Operations) 和終端操作 (Terminal Operations)。理解它們的區別對於有效地使用 Stream API 至關重要。

**1. 中間操作 (Intermediate Operations):**

* **特性:** 中間操作會**返回一個新的 Stream**。  它們是惰性求值的 (lazily evaluated)，也就是說，在終端操作被呼叫之前，中間操作並不會真正執行。
* **目的:**  中間操作用於對 Stream 中的元素進行轉換和過濾，例如 `filter`、`map`、`sorted`、`distinct`、`limit`、`skip` 等。
* **鏈式調用:**  由於中間操作返回一個新的 Stream，因此可以將多個中間操作鏈式調用，形成一個流水線。

**範例:**

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");

Stream<String> filteredNames = names.stream()
        .filter(name -> name.startsWith("A")) // 中間操作：過濾
        .map(String::toUpperCase); // 中間操作：轉換為大寫

// 到目前為止，filter 和 map 操作都還沒有執行
```

**2. 終端操作 (Terminal Operations):**

* **特性:** 終端操作會**消費 Stream 並返回一個結果**。  它們會觸發 Stream 中所有中間操作的執行。  執行完終端操作後，Stream 就被消費掉了，不能再被使用。
* **目的:** 終端操作用於從 Stream 中獲取結果，例如 `forEach`、`collect`、`reduce`、`count`、`min`、`max`、`findFirst`、`anyMatch` 等。

**範例 (接續上面的例子):**

```java
long count = filteredNames.count(); // 終端操作：計算數量，此時才會執行 filter 和 map 操作
System.out.println(count); // 輸出 1
```

**中間操作和終端操作的區別總結：**

| 特性        | 中間操作                      | 終端操作                      |
|-------------|-------------------------------|-------------------------------|
| 返回值      | 新的 Stream                   | 非 Stream 結果 (例如 `int`、`List`、`Optional` 等) |
| 求值方式    | 惰性求值                      | 及早求值                      |
| 是否消費 Stream | 不消費 Stream，可以繼續鏈式調用 | 消費 Stream，Stream 不能再被使用 |


**示例說明:**

想像一下一個流水線，中間操作就像流水線上的各个加工站，而終端操作就像流水線的終點，用於收集最終產品。  水在終點被收集之前，不會流經整個流水線。  同樣地，在終端操作被呼叫之前，中間操作也不會執行。  一旦水被收集，流水線就停止工作了。  同樣地，執行完終端操作後，Stream 也就不能再被使用了。


理解中間操作和終端操作的區別對於正確使用 Java Stream API 至關重要，可以幫助你編寫更高效、更易讀的程式碼。


