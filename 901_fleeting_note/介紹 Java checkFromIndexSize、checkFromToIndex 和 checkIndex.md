`checkFromIndexSize`、`checkFromToIndex` 和 `checkIndex` 都是 Java 中用於檢查數組或列表索引有效性的工具方法。它們通常在 `java.util.Objects` 類中定義（Java 9 及以後版本），或者在其他工具类中用于类似的边界检查目的。 这些方法的主要目的是在访问数组或列表元素之前验证索引是否在有效范围内，从而防止 `IndexOutOfBoundsException` 的发生。

以下是每个方法的详细解释：

**1. `checkFromIndexSize(int fromIndex, int size, int length)`**

* **作用:** 检查 `fromIndex` 和 `size` 是否定义了一个有效的范围，该范围位于 0 到 `length` 之间。  它主要用于子数组或子列表操作，例如 `list.subList()` 或数组复制。

* **参数:**
    * `fromIndex`: 起始索引（包含）。
    * `size`:  子数组或子列表的大小。
    * `length`:  整个数组或列表的长度。

* **异常:** 如果 `fromIndex` 为负数，或者 `fromIndex + size` 大于 `length`，或者 `size` 为负数，则抛出 `IndexOutOfBoundsException`。

* **示例:**

```java
int fromIndex = 2;
int size = 3;
int length = 10;

Objects.checkFromIndexSize(fromIndex, size, length); // 不会抛出异常

Objects.checkFromIndexSize(-1, size, length); // 抛出 IndexOutOfBoundsException
Objects.checkFromIndexSize(fromIndex, -1, length); // 抛出 IndexOutOfBoundsException
Objects.checkFromIndexSize(8, 3, length); // 抛出 IndexOutOfBoundsException (8 + 3 > 10)
```

**2. `checkFromToIndex(int fromIndex, int toIndex, int length)`**

* **作用:** 检查 `fromIndex` 和 `toIndex` 是否定义了一个有效的范围，该范围位于 0 到 `length` 之间。  它主要用于遍历或处理数组或列表的某个部分。

* **参数:**
    * `fromIndex`: 起始索引（包含）。
    * `toIndex`: 结束索引（不包含）。
    * `length`: 整个数组或列表的长度。

* **异常:** 如果 `fromIndex` 为负数，或者 `toIndex` 大于 `length`，或者 `fromIndex` 大于 `toIndex`，则抛出 `IndexOutOfBoundsException`。

* **示例:**

```java
int fromIndex = 2;
int toIndex = 5;
int length = 10;

Objects.checkFromToIndex(fromIndex, toIndex, length); // 不会抛出异常

Objects.checkFromToIndex(-1, toIndex, length); // 抛出 IndexOutOfBoundsException
Objects.checkFromToIndex(fromIndex, 11, length); // 抛出 IndexOutOfBoundsException
Objects.checkFromToIndex(5, 2, length); // 抛出 IndexOutOfBoundsException (5 > 2)
```

**3. `checkIndex(int index, int length)`**

* **作用:** 检查单个索引 `index` 是否在 0 到 `length` 之间。  它主要用于访问数组或列表的单个元素。

* **参数:**
    * `index`: 要检查的索引。
    * `length`: 数组或列表的长度。

* **异常:** 如果 `index` 为负数或大于等于 `length`，则抛出 `IndexOutOfBoundsException`。

* **示例:**

```java
int index = 3;
int length = 10;

Objects.checkIndex(index, length); // 不会抛出异常

Objects.checkIndex(-1, length); // 抛出 IndexOutOfBoundsException
Objects.checkIndex(10, length); // 抛出 IndexOutOfBoundsException
```


**总结:**

这些方法提供了一种简洁且一致的方式来检查数组和列表索引的有效性，有助于提高代码的健壮性和可读性。  它们在 Java 标准库和许多其他库中被广泛使用，以确保索引访问的安全性。


