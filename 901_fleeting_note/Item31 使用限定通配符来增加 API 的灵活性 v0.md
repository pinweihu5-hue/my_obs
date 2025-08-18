Usebounded wild cards to increase API flexibility


### invariant 的侷限

item28 提到 泛型/parameterized types are invariant

因此，say we save Type1 and Type2, `List<Type1>` 和 `List<Type2>` 不是彼此的 subType or superType
意思是就，`List<String>` 不是 `List<Object>` 的 subType

你可以 put any obj into `List<Object>`, 但是你只能在  `List<String>` 放 string
因此，`List<String>` 基本上無法做 `List<Object>` 的很多事情，因此不是 subType(by the Liskov substitution principal, Item 10)


但是如果我們想要提供更多的彈性呢?
Consider the Stack class from Item 29. To refresh your memory, here is its public API:
![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201148107.png]]

假設我們有一個 pushAll method
我們要推 a sequence of elements 進去
第一個寫法如下：
![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201148803.png]]


This method compiles cleanly, but it isn’t entirely satisfactory. If the element type of the Iterable `src` exactly matches that of the stack, it works fine. But suppose you have a `Stack<Number> `and you invoke push(intVal), where intVal is of type Integer. This works because Integer is a subtype of Number. So logically, it seems that this should work, too:

我們來用用看
init stack with `Stack<Number>
但是我們要推的是 `Iterable<integer>`
因為 Integer is a subtype of Number, 所以應該可以吧?
![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201149428.png]]


會噴掉，因為 parameterized types are invariant:
![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201150352.png]]


有解！泛型有提供 bounded wildcard type 來處理這種情況

pushAll的 type 可以改為 `Iterable<? extends E>`, 可以讀做  “Iterable of some subtype of E,” 

PS: The use of the keyword extends is slightly misleading: recall from Item 29 that subtype is defined so that every type is a subtype of itself, even though it does not extend itself.) 

![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201151273.png]]
解掉了!  client 和這邊都沒 compiler error, 也沒 warning -> typesafe 保證


假設我們要寫一個 popAll method
他可以把 pop all 到一個 給定的 collection

first try:
![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201152143.png]]

一樣 compile cleanly 但是如果我們如同下面這樣用
say 我們有 `Stack<Number>`  然後要把東西popAll 給 `Collection<Object>` type
![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201153099.png]]

一樣會噴掉，拿到 error: `Collection<Object>` is not a subtype of `Collection<Number>`

同樣，我們可以用 wildcard types 
`Collection<? super E>` 讀做   “collection of some super type of E”  (因為這邊是要吃Object, Object is super type of Number)

![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201154020.png]]

如果你要彈性，你可以用 wildcard types

但是你要清楚是 super or extend, 只能選一邊，無法都吃

### PECS

記憶法 PECS: 
Prdocuer - Extends
Consumer - Super


In other words, if a parameterized type represents a T producer, use `<? extends T>`; if it represents a T consumer, use `<? super T>`

In our Stack example, 
pushAll’s src parameter produces E instances for use by the Stack, so the appropriate type for src is `Iterable<? extends E>`; popAll’s dst parameter consumes E instances from the Stack, so the appropriate type for dst is `Collection<? super E>`. 

E 是生產給 Stack 用的，push, 會是 Stack T 的 subtype  -> 用 extend
E 是消費來自於 Stack 用的，pop, 會是 Stack T 的 supertype  -> 用 super


### 更多例子

看其他例子: The Chooser constructor in Item 28 has this declaration:

  `public Chooser(Collection<T> choices)`

這個建構子是 "產生" value of type T (to store them for later use)
This constructor uses the collection choices only to produce values of type T (and stores them for later use), so its declaration should use a wildcard type that extends T. 

```java
// Wildcard type for parameter that serves as an T producer 
public Chooser(Collection<? extends T> choices)

```

有差嗎? 會喔
如果你想要 pass `List<Integer>`  然後你的 ctor 是 `Chooser<Number>`, 會噴掉，除非你用 bounded wildcard type


另一個例子：
Now let’s look at the union method from Item 30.
`public static <E> Set<E> union(Set<E> s1, Set<E> s2)`

s1, s2 都是產生出來，要進去處理
`public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2)`


另外，return type is still `Set<E>`. 

不要在 ret type 用 bouned wird card type, 這樣等於強迫 client 使用  bouned wird card type


調整後，一樣 compile cleanly
```java

Set<Integer> integers = Set.of(1, 3, 5);
Set<Double>  doubles  = Set.of(2.0, 4.0, 6.0);
Set<Number>  numbers  = union(integers, doubles);
```



如果你寫的對，客戶端基本上不會有感覺，因為你只是讓他們更方便使用。


Java8以前，上面的 union ret type 的 `Set<Number>`，會噴下面的錯誤
![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201154834.png]]


你要用 explicit type 參數來讓 compile, 如下
![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201155716.png]]



另一個例子， the max method in Item 30.
 `public static <T extends Comparable<T>> T max(List<T> list)`


Here is a revised declaration that uses wildcard types:
`public static <T extends Comparable<? super T>> T max( List<? extends T> list)`

To get the revised declaration from the original, we applied the PECS heuristic twice. 

先看parameter list, 因為會產生 T instances, 因此 `List<T>` to `List<? extends T>`

Comparables are always consumers, so you should generally use `Comparable<? super T>` in preference to` Comparable<T>`. The same is true of comparators; therefore, you should generally use `Comparator<? super T>` in preference to `Comparator<T>`.

這個 type 是變得很複雜，但是的確也增加了彈性
下面這個 list 就是一個原本吃不去，但是調整後卻可以的
`List<ScheduledFuture<?>> scheduledFutures = ... ;`

why 原本 的宣告無法? 

因為 ScheduledFuture 沒有實作 `Comparable<ScheduledFuture>`
ScheduledFuture 是 sub interface of `Delayed`, which extends `Comparable<Delayed>`
或是說，a ScheduledFuture 實例 is not comparable to 其他的 ScheduledFuture 實例, 他是 comparable 其他的 Delayed instance

因此，你可以用 wildcard 如果 type 沒有直接實作 Comparable (or Comparator), 但是這個 extends 來自的 type 有實作

==


### make client ez to use

下面這個靜態函數 swap 互換兩個 idx in a list

第一個用 unbounded type parameter
第二個用 unbounded wildcard
![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201156614.png]]
如果是 public API, 第二個比較好用，你 pass any list, 就可以用, client side 不需要 worry about type parameter

所以， As a rule of thumb:
- If it’s an unbounded type parameter(like `List<T>`), replace it with an unbounded wildcard (like `List<?>`); 
- if it’s a bounded type parameter(like `List<T extends Number>`), replace it with a bounded wildcard (like `List<? super Integer>`, `List<? extends Integer>`).


There’s one problem with the second declaration for swap. 
The straightforward implementation won’t compile:
![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201157386.png]]

不過這樣會噴錯喔
![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201158107.png]]

因為你這樣 `List<?>` 定義 List, 你只能放 null 到 `List<?>`
不需要raw type or uncheck cast, 我們需要一個 helper method 如下


The swapHelper method knows that list is a `List<E>`. 
因此，他知道 value 出來的 E  就是進去 的 E
![[IMG-Item31 使用限定通配符来增加 API 的灵活性 v0-20250805201159200.png]]
我們可以把這個複雜的 type 放 private
讓好用的 `List<?>` be public


### summary
- using wildcard types in your APIs, while tricky, makes the APIs far more flexible. 
- If you write a library that will be widely used, the proper use of wildcard types should be considered mandatory. 
- Remember the basic rule: producer-extends, consumer-super (PECS). 
- Also remember that all comparables and comparators are consumers.