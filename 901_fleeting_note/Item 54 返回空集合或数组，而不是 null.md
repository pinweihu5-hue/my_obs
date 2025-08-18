Return empty collections or arrays, not nulls


It is not uncommon to see methods that look something like this:

如下的方法很常见：

```java
// Returns null to indicate an empty collection. Don't do this!
private final List<Cheese> cheesesInStock = ...;
/**
* @return a list containing all of the cheeses in the shop,
* or null if no cheeses are available for purchase.
*/
public List<Cheese> getCheeses() {
    return cheesesInStock.isEmpty() ? null: new ArrayList<>(cheesesInStock);
}
```

There is no reason to special-case the situation where no cheeses are available for purchase. Doing so requires extra code in the client to handle the possibly null return value, for example:

没有理由对没有奶酪可供购买的情况进行特殊处理。
**如果这样做，在客户端需要额外的代码处理可能为空(null)的返回值**，例如：

```java
List<Cheese> cheeses = shop.getCheeses();
if (cheeses != null && cheeses.contains(Cheese.STILTON))
    System.out.println("Jolly good, just the thing.");
```

This sort of circumlocution is required in nearly every use of a method that returns null in place of an empty collection or array. It is error-prone, because the programmer writing the client might forget to write the special-case code to handle a null return. Such an error may go unnoticed for years because such methods usually return one or more objects. Also, returning null in place of an empty container complicates the implementation of the method returning the container.

**在几乎每次使用返回 null 来代替空集合或数组的方法时，都需要使用这种权宜之计。它很容易出错，因为编写客户端的程序员可能忘记编写特殊情况的代码来处理 null 返回。**

这样的错误可能会被忽略多年，因为这样的方法通常返回一个或多个对象。此外，在空容器中返回 null 会使返回容器的方法的实现复杂化。

It is sometimes argued that a null return value is preferable to an empty collection or array because it avoids the expense of allocating the empty container. This argument fails on two counts. First, it is inadvisable to worry about performance at this level unless measurements have shown that the allocation in question is a real contributor to performance problems (Item 67). Second, it is possible to return empty collections and arrays without allocating them. Here is the typical code to return a possibly empty collection. Usually, this is all you need:

有时有人认为，null 返回值比空集合或数组更可取，因为它避免了分配空容器的开销。这个论点有两点是不成立的。
- 首先，在这个级别上担心性能是不明智的，除非分析表明这个方法正是造成性能问题的真正源头（[Item-67](/Chapter-9/Chapter-9-Item-67-Optimize-judiciously.md)）。
- 第二，返回空集合和数组而不分配它们是可能的。下面是返回可能为空的集合的典型代码。通常，这就是你所需要的：

```java
//The right way to return a possibly empty collection
public List<Cheese> getCheeses() {
    return new ArrayList<>(cheesesInStock);
}
```

In the unlikely event that you have evidence suggesting that allocating empty collections is harming performance, you can avoid the allocations by returning the same immutable empty collection repeatedly, as immutable objects may be shared freely (Item 17). Here is the code to do it, using the Collections.emptyList method. If you were returning a set, you’d use Collections.emptySet; if you were returning a map, you’d use Collections.emptyMap. But remember, this is an optimization, and it’s seldom called for. If you think you need it, measure performance before and after, to ensure that it’s actually helping:

不太可能會遇到分配空集合会损害性能的情況，你可以通过重复返回相同的不可变空集合来避免分配，因为不可变对象可以自由共享（[Item-17](/Chapter-4/Chapter-4-Item-17-Minimize-mutability.md)）。

下面是使用 `Collections.emptyList` 完成此任务的代码。如果你要返回一个 Set，你会使用 `Collections.emptySet`；如果要返回 Map，则使用 `Collections.emptyMap`。但是请记住，这是一个优化，很少真正需要它。如果你认为你需要它，测试一下前后的表现，确保它确实有帮助：

```java
// Optimization - avoids allocating empty collections
public List<Cheese> getCheeses() {
    return cheesesInStock.isEmpty() ? Collections.emptyList(): new ArrayList<>(cheesesInStock);
}
```

The situation for arrays is identical to that for collections. Never return null instead of a zero-length array. Normally, you should simply return an array of the correct length, which may be zero. Note that we’re passing a zero-length array into the toArray method to indicate the desired return type, which is Cheese[]:

数组的情况与集合的情况相同。永远不要返回 null，而应该返回零长度的数组。通常，你应该简单地返回一个正确长度的数组，它可能是零长度。

注意，我们将一个零长度的数组传递到 toArray 方法中，以指示所需的返回类型，即 Cheese[0]：
```java
//The right way to return a possibly empty array
public Cheese[] getCheeses() {
    return cheesesInStock.toArray(new Cheese[0]);
}
```

If you believe that allocating zero-length arrays is harming performance, you can return the same zero-length array repeatedly because all zero-length arrays are immutable:

如果你认为分配零长度数组会损害性能，你可以重复返回相同的零长度数组，因为所有的零长度数组都是不可变的：
```java
// Optimization - avoids allocating empty arrays
private static final Cheese[] EMPTY_CHEESE_ARRAY = new Cheese[0];

public Cheese[] getCheeses() {
    return cheesesInStock.toArray(EMPTY_CHEESE_ARRAY);
}
```

In the optimized version, we pass the same empty array into every toArray call, and this array will be returned from getCheeses whenever cheesesInStock is empty. Do not preallocate the array passed to toArray in hopes of improving performance. Studies have shown that it is counterproductive [Shipilëv16]:

在优化版本中，我们将相同的空数组传递到每个 toArray 调用中，当 cheesesInStock 为空时，这个数组将从 getCheeses 返回。


不要为了提高性能而预先分配传递给 toArray 的数组。研究表明，这样做会适得其反 [Shipilev16]:
```java
// Don’t do this - preallocating the array harms performance!
return cheesesInStock.toArray(new Cheese[cheesesInStock.size()]);
```

譯註：
问题在于预先分配了大小等于集合大小的数组 `new Cheese[cheesesInStock.size()]`。 toArray 方法的实现通常会检查传入的数组的大小是否足够容纳集合中的所有元素。

如果数组大小足够： toArray 方法会直接将集合元素复制到传入的数组中。 **但是，即使传入数组的大小正好等于集合大小，toArray 方法 仍然会创建一个新的数组，并将元素复制到新数组中，然后返回新数组。 这意味着预先分配的数组实际上被浪费了。**

如果数组大小不足： toArray 方法会创建一个新的、大小合适的数组，并将集合元素复制到新数组中，然后返回新数组。

在两种情况下，toArray 方法都会创建一个新的数组。 因此，预先分配数组的操作不仅没有帮助，反而增加了额外的开销，因为它创建了一个最终不会被使用的数组对象。



In summary, never return null in place of an empty array or collection. It makes your API more difficult to use and more prone to error, and it has no performance advantages.

总之，**永远不要用 null 来代替空数组或集合**。它使你的 API 更难以使用，更容易出错，并且没有性能优势。