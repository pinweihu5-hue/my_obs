

`Collectors.joining()` 是 `Collectors` 類的一個靜態方法

**專門用於將 Stream 中的元素連接成一個單個字符串**。



它有以下三種重載形式：

1. **`joining()`**:  這是最簡單的形式，它將 Stream 中的所有元素直接連接在一起，沒有任何分隔符。

   ```java
   List<String> strings = Arrays.asList("a", "b", "c");
   String joined = strings.stream().collect(Collectors.joining()); // joined = "abc"
   ```

2. **`joining(CharSequence delimiter)`**:  這個版本允許你指定一個分隔符，用於分隔連接後的元素。

   ```java
   List<String> strings = Arrays.asList("a", "b", "c");
   String joined = strings.stream().collect(Collectors.joining(",")); // joined = "a,b,c"
   ```

3. **`joining(CharSequence delimiter, CharSequence prefix, CharSequence suffix)`**: 這個版本最為靈活，允許你指定分隔符、前綴和後綴。

   ```java
   List<String> strings = Arrays.asList("a", "b", "c");
   String joined = strings.stream().collect(Collectors.joining(",", "[", "]")); // joined = "[a,b,c]"
   ```


**一些使用 `Collectors.joining()` 的例子:**

* **連接數字:**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3);
String joinedNumbers = numbers.stream()
        .map(String::valueOf) // 先將 Integer 轉換為 String
        .collect(Collectors.joining(", ")); // joinedNumbers = "1, 2, 3"
```

* **連接對象的屬性:**

```java
List<Person> people = Arrays.asList(
        new Person("Alice", 25),
        new Person("Bob", 30)
);

String joinedNames = people.stream()
        .map(Person::getName)
        .collect(Collectors.joining(", ")); // joinedNames = "Alice, Bob"
```

* **構建 SQL 查询语句:**

```java
List<String> names = Arrays.asList("Alice", "Bob");
String inClause = names.stream()
        .map(name -> "'" + name + "'") // 添加引號
        .collect(Collectors.joining(",")); // inClause = "'Alice','Bob'"

String sql = "SELECT * FROM users WHERE name IN (" + inClause + ")";
```


**總結:**

`Collectors.joining()` 是 Java Stream API 中用於連接 Stream 元素成字符串的主要方法。它提供了靈活的選項，允許你指定分隔符、前綴和後綴，使其適用於各種字符串拼接場景。  記住，如果你的 Stream 元素不是字符串類型，你需要先使用 `map()` 方法將它們轉換為字符串。
