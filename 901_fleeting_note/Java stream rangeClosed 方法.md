`IntStream.rangeClosed()` 是 Java Stream API 中的一個靜態方法，用於生成一個包含指定範圍內所有整數的 `IntStream`，**包括起始值和結束值**。

**方法簽名:**

```java
public static IntStream rangeClosed(int startInclusive, int endInclusive)
```

* `startInclusive`: 範圍的起始值（包含在內）。
* `endInclusive`: 範圍的結束值（包含在內）。

**返回值:**

返回一個有序的 `IntStream`，其中包含從 `startInclusive` 到 `endInclusive` 的所有整數。

**示例:**

```java
IntStream.rangeClosed(1, 5).forEach(System.out::println);
// 輸出：
// 1
// 2
// 3
// 4
// 5


IntStream.rangeClosed(5, 1).forEach(System.out::println); // 空的 Stream，因為起始值大於結束值


List<Integer> list = IntStream.rangeClosed(2, 6)
    .boxed() // 將 IntStream 轉換為 Stream<Integer>
    .collect(Collectors.toList());
System.out.println(list); // 輸出: [2, 3, 4, 5, 6]


int sum = IntStream.rangeClosed(1, 10).sum();
System.out.println(sum); // 輸出: 55
```

**`rangeClosed()` vs `range()`:**

`IntStream` 中還有一個類似的方法叫做 `range()`，它的區別在於**不包含結束值**。

```java
IntStream.range(1, 5).forEach(System.out::println);
// 輸出：
// 1
// 2
// 3
// 4  (注意：不包含 5)
```

**使用場景:**

`rangeClosed()` 常用於以下場景：

* **迭代固定次數:**  例如，執行一個循環 10 次。
* **生成序列:**  例如，生成一個包含 1 到 100 的數字列表。
* **與其他 Stream 操作結合使用:**  例如，生成一個偶數序列，可以使用 `rangeClosed()` 生成數字序列，然後使用 `filter()` 篩選出偶數。


**總結:**

`IntStream.rangeClosed()` 是一個方便的方法，用於生成包含指定範圍內所有整數的 `IntStream`，包括起始值和結束值。  它與 `range()` 的區別在於是否包含結束值。  在需要包含結束值的場景下，`rangeClosed()` 是更合適的選擇。
