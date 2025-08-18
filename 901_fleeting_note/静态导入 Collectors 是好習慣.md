
**靜態導入:**

靜態導入允許你直接使用靜態成員的名稱，而無需指定類名。  例如，`Collectors.toList()` 可以簡寫為 `toList()`。

**為什麼要靜態導入 `Collectors` 的所有成員?**

`Collectors` 類提供了許多用於終端操作的靜態方法，例如 `toList()`、`toSet()`、`toMap()`、`groupingBy()`、`joining()` 等。  這些方法在 Stream API 中非常常用。

如果不使用靜態導入，每次使用這些方法時都需要寫完整的類名，例如 `Collectors.toList()`。  這會使程式碼顯得冗長，降低可讀性，尤其是在 Stream 管道中，可能會有多個 `Collectors` 方法鏈式調用。

使用靜態導入後，可以省略類名，直接使用方法名，例如 `toList()`。  這樣可以使程式碼更簡潔，更容易理解。

**示例：**

**不使用靜態導入：**

```java
List<String> names = people.stream()
    .map(Person::getName)
    .collect(Collectors.toList());
```

**使用靜態導入：**

```java
import static java.util.stream.Collectors.*;

List<String> names = people.stream()
    .map(Person::getName)
    .collect(toList()); // 更簡潔
```

**結論：**

靜態導入 `Collectors` 的所有成員是一種良好的編碼習慣，可以提高程式碼的可讀性，尤其是在大量使用 Stream API 和 `Collectors` 的情況下。  因此，這句話建議將其作為一種習慣用法。


