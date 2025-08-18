
使用 `toMap` Collector 建立 Map 時，如何處理鍵衝突的問題，特別是使用 merge 函數的情況。

**鍵衝突:**

當你使用 `toMap` 將 Stream 的元素收集到 Map 中時，如果多個元素對應相同的鍵，就會發生鍵衝突。  例如，如果你想根據人名建立一個 Map，其中鍵是人名，值是年齡，但有兩個人同名，就會發生鍵衝突。

**`toMap` 的 merge 函數:**

`toMap` 提供了一個更複雜的形式，允許你提供一個 merge 函數來處理鍵衝突。  merge 函數是一個 `BinaryOperator<V>`，其中 `V` 是 Map 的值類型。  當發生鍵衝突時，merge 函數會被用來將新的值和現有的值合併成一個值。

**如何使用 merge 函數:**

`toMap` 的這個版本接受三個參數：

1. **keyMapper:**  一個 `Function`，用於從 Stream 的元素中提取鍵。
2. **valueMapper:**  一個 `Function`，用於從 Stream 的元素中提取值。
3. **mergeFunction:**  一個 `BinaryOperator`，用於在發生鍵衝突時合併值。

**示例：計算相同鍵的值的乘積**

```java
List<Item> items = Arrays.asList(
    new Item("A", 2),
    new Item("B", 3),
    new Item("A", 5),
    new Item("C", 7),
    new Item("B", 2)
);

Map<String, Integer> productMap = items.stream().collect(
    Collectors.toMap(
        Item::getName,  // keyMapper: 提取名稱作為鍵
        Item::getValue, // valueMapper: 提取值
        (v1, v2) -> v1 * v2 // mergeFunction: 將值相乘
    )
);

System.out.println(productMap); // 輸出 {A=10, B=6, C=7}
```

在這個例子中：

* `Item::getName` 用於提取商品名稱作為鍵。
* `Item::getValue` 用於提取商品的值。
* `(v1, v2) -> v1 * v2` 是 merge 函數，它會將相同鍵的值相乘。

因此，對於鍵 "A"，值 2 和 5 會被相乘，得到 10。 對於鍵 "B"，值 3 和 2 會被相乘，得到 6。


**`groupingBy` 和 merge 函數:**

`groupingBy` 方法也提供了類似於 `toMap` 的功能，可以處理鍵衝突。  它通常與下游 Collector 一起使用，例如 `Collectors.reducing()`，來對每個組的值進行聚合操作，這也包含了處理鍵衝突的策略。


總而言之，`toMap` 和 `groupingBy` 提供了靈活的機制來處理鍵衝突，允許你根據具體需求定義如何合併值。  merge 函數提供了一種強大的方式來處理這種情況，例如計算總和、乘積、最大值、最小值等。


