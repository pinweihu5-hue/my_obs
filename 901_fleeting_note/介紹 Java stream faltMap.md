`flatMap()` 是 Java Stream API 中一個非常重要的操作，它允許你將一個 Stream 中的每個元素**扁平化**成多個元素，然後將這些元素組合成一個新的 Stream。  它本質上是一個**一對多**的轉換操作。


**flatMap 的使用方法:**

`flatMap()` 的方法簽名如下：

```java
<R> Stream<R> flatMap(
	Function<? super T, ? extends Stream<? extends R>> mapper
);

```

其中 `mapper` 函數接受一個類型為 `T` 的元素，並返回一個類型為 `R` 的元素的 Stream。

**示例:**

1. **將字符串列表扁平化成字符 Stream:**
   ```java
   List<String> words = Arrays.asList("hello", "world");

   List<String> characters = words.stream()
           .flatMap(
	           word -> word.chars().boxed().map(i -> Character.toString((int) i))
	        )
           .collect(Collectors.toList());

   // characters: [h, e, l, l, o, w, o, r, l, d]
   ```

   這裡，`flatMap()` 將每個字符串轉換成一個 IntStream (通過 `chars()` 方法)，然後使用 `boxed()` 將其轉換為 `Stream<Integer>`，再使用 `map()` 將每個 Integer 轉換為 String 類型的字符。 最後，`flatMap()` 將所有字符 Stream 扁平化成一個單個 Stream。

2. **處理嵌套集合:**
   ```java
   List<List<Integer>> nestedList = Arrays.asList(
           Arrays.asList(1, 2),
           Arrays.asList(3, 4)
   );

   List<Integer> flattenedList = nestedList.stream()
           .flatMap(innerList -> innerList.stream())
           .collect(Collectors.toList());

   // flattenedList: [1, 2, 3, 4]
   ```

   這裡，`flatMap()` 將每個內部列表轉換成一個 Stream，然後將所有 Stream 扁平化成一個包含所有數字的單個 Stream。

3. **處理 Optional:**
   ```java
   List<Optional<Integer>> optionals = Arrays.asList(
           Optional.of(1),
           Optional.empty(),
           Optional.of(2)
   );

   List<Integer> presentValues = optionals.stream()
           .flatMap(Optional::stream) // Java 9 或更高版本
           .collect(Collectors.toList());

   // presentValues: [1, 2]
   ```

   這裡，`flatMap(Optional::stream)`  將每個 `Optional` 轉換成一個 Stream，如果 `Optional` 包含值，則 Stream 包含該值；如果 `Optional` 為空，則 Stream 也為空。  `flatMap()` 將所有 Stream 扁平化，有效地过滤掉了空的 `Optional`。


**flatMap vs map:**

`map()` 是一對一的轉換
`flatMap()` 是一對多的轉換，然後扁平化結果。  


