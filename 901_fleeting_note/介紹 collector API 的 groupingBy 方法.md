`groupingBy()` 是 Java Stream API 中 Collector 接口的一個靜態方法

它用於根據**指定的分類函數**(譬如下面第一個例子的 Person: :getAge)將流的元素分組到一個 `Map` 中。
這個 `Map` 的鍵(key)是分類函數返回的分組依據，值是屬於同一組的元素的集合。


以下是 `groupingBy()` 方法的幾種用法及其說明：

**1. 基本用法：根據單一屬性分組**

這是最常見的用法，將流元素按照某個屬性分組。

```java
List<Person> people = Arrays.asList(
        new Person("Alice", 25, "New York"),
        new Person("Bob", 30, "London"),
        new Person("Charlie", 25, "New York"),
        new Person("David", 35, "London")
);

Map<Integer, List<Person>> peopleByAge = people.stream().groupBy(Person::getAge);

// peopleByAge: {25=[Alice, Charlie], 30=[Bob], 35=[David]}
```

這裡，`Person::getAge` 作為分類函數，將 `people` 列表中的 `Person` 對象按照年齡分組。結果 `peopleByAge` 是一個 `Map`，鍵是年齡，值是具有相同年齡的 `Person` 對象的 `List`。

**2. 使用下游收集器：定制分組後的結果**

`groupingBy()` 可以接受一個下游收集器，用於定制每個組中元素的收集方式。例如，可以將元素收集到 `Set`、`Queue` 或計算每個組的元素數量等。
```java
Map<Integer, Set<Person>> peopleByAgeSet = people.stream()
        .groupBy(Person::getAge, Collectors.toSet());

// peopleByAgeSet: {25=[Alice, Charlie], 30=[Bob], 35=[David]} (Set 去除了重複)

Map<Integer, Long> ageCounts = people.stream().groupBy(Person::getAge, Collectors.counting());

// ageCounts: {25=2, 30=1, 35=1}
```

這裡，`Collectors.toSet()` 將每個年齡組的元素收集到一個 `Set` 中，`Collectors.counting()` 計算每個年齡組的元素數量。

**3. 多級分組：根據多個屬性分組**

可以使用嵌套的 `groupingBy()` 實現多級分組。
```java
Map<Integer, Map<String, List<Person>>> peopleByAgeAndCity = people.stream()
        .groupBy(Person::getAge, Collectors.groupingBy(Person::getCity));

// peopleByAgeAndCity: {25={New York=[Alice, Charlie]}, 30={London=[Bob]}, 35={London=[David]}}
```

這裡，先按年齡分組，然後在每個年齡組內再按城市分組。

**4. 使用映射函數：更改分組鍵的類型**

可以使用映射函數修改分組鍵的類型。
```java
Map<String, List<Person>> peopleByAgeGroup = people.stream()
        .groupBy(p -> p.getAge() < 30 ? "Young" : "Old");

// peopleByAgeGroup: {Young=[Alice, Bob, Charlie], Old=[David]}
```

這裡，使用 lambda 表達式將年齡映射到 "Young" 或 "Old" 字符串，然後根據這些字符串進行分組。


**總結:**

`groupingBy()` 是一个非常强大的收集器，可以灵活地根据不同的需求对流元素进行分组。 通过结合下游收集器和映射函数，可以实现各种复杂的分组操作。  理解 `groupingBy()` 的不同用法对于有效地处理集合数据至关重要。
