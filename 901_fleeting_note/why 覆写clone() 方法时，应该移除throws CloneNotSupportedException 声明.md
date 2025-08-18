
`Object.clone()`方法声明了`throws CloneNotSupportedException`。这意味着任何直接调用`Object.clone()`的方法都必须处理这个受检异常（checked exception），要么捕获它，要么继续抛出。

然而，当你覆写`clone()`方法时，特别是当你已经实现了`Cloneable`接口并打算让你的类支持克隆时，继续抛出`CloneNotSupportedException`就显得多余且降低了可用性。

Effective Java（Item-71）建议避免不必要的受检异常。如果一个方法能够合理地处理异常，并且客户端代码不需要采取特定的恢复措施，那么最好在方法内部处理异常，而不是抛出受检异常，让客户端代码强制处理。

在`clone()`方法的场景下，如果你的类实现了`Cloneable`，就意味着你已经做好了处理克隆失败的准备（例如，通过深拷贝等方式）。因此，在`clone()`方法内部处理`CloneNotSupportedException`，并返回一个默认值或抛出一个运行时异常（unchecked exception）是更合理的做法。

**示例:**

```java
class MyClass implements Cloneable {
    @Override
    public MyClass clone() { // 注意这里没有 throws CloneNotSupportedException
        try {
            return (MyClass) super.clone();
        } catch (CloneNotSupportedException e) {
            // 这里不应该发生，因为我们已经实现了 Cloneable
            throw new AssertionError(e); // 抛出一个运行时异常
        }
    }
}
```

通过移除`throws CloneNotSupportedException`声明，客户端代码就可以更简洁地调用`clone()`方法，而无需额外的`try-catch`块。这提高了代码的可读性和易用性。


总而言之，如果你的类支持克隆，覆写`clone()`方法时应该移除`throws CloneNotSupportedException`声明，并在方法内部处理潜在的`CloneNotSupportedException`，提供更友好的用户体验。
