介绍 `String`类的`contentEquals`方法的演变
以及`CharSequence`接口的引入如何导致`contentEquals`方法的重载。

**1. `contentEquals(StringBuffer)` (Java 4)**

在 Java 4 中，`String`类提供了`contentEquals(StringBuffer sb)`方法。这个方法用于比较`String`对象和`StringBuffer`对象的内容是否相等。  它会逐个字符地比较两个对象的内容，忽略它们的类型差异。

**示例:**

```java
String str = "hello";
StringBuffer sb = new StringBuffer("hello");
boolean isEqual = str.contentEquals(sb); // isEqual 为 true
```

**2. `CharSequence` 接口 (Java 5)**

在 Java 5 中，引入了`CharSequence`接口。这是一个表示字符序列的接口，`String`、`StringBuffer`、`StringBuilder`、`CharBuffer`等类都实现了这个接口。`CharSequence`接口提供了一种统一的方式来访问不同类型的字符序列。

**3. `contentEquals(CharSequence)` (Java 5)**

为了支持`CharSequence`接口，Java 5 中为`String`类添加了一个重载的`contentEquals(CharSequence cs)`方法。这个方法的功能与`contentEquals(StringBuffer)`类似，但是它接受的参数类型是`CharSequence`，因此可以用于比较`String`对象与任何实现了`CharSequence`接口的对象的内容。

**示例:**

```java
String str = "hello";
StringBuilder sb = new StringBuilder("hello");
boolean isEqual = str.contentEquals(sb); // isEqual 为 true

CharBuffer cb = CharBuffer.wrap("hello".toCharArray());
isEqual = str.contentEquals(cb); // isEqual 为 true
```

**重载和向后兼容性:**

`String`类同时拥有`contentEquals(StringBuffer)`和`contentEquals(CharSequence)`这两个方法，构成了方法重载。保留`contentEquals(StringBuffer)`是为了保持向后兼容性，使得使用 Java 4 或更早版本编写的代码仍然可以正常工作。  新增的`contentEquals(CharSequence)`则提供了更通用的功能，可以处理更多类型的字符序列。


**总结:**

`contentEquals`方法允许比较`String`对象与其他字符序列对象的内容是否相等。 Java 5 中引入的`CharSequence`接口和对应的`contentEquals(CharSequence)`方法扩展了`contentEquals`的适用范围，同时保留了对`StringBuffer`的兼容性。  这体现了 Java 在演进过程中保持向后兼容性的努力。
