Don’t use raw types 


what is raw type?
For example, the raw type corresponding to `List<E>` is `List`. 

Raw types behave as if all of the generic type information were erased from the type declaration.
They exist primarily for compatibility with pre-generics code (pre Java 5)

```java

// Raw collection type - don't do this!
// My stamp collection. Contains only Stamp instances.
private final Collection stamps = ... ;


// Raw iterator type - don't do this!
for (Iterator i = stamps.iterator(); i.hasNext(); )
Stamp stamp = (Stamp) i.next(); // Throws ClassCastException stamp.cancel();

```


As mentioned throughout this book, it pays to discover errors as soon as possible after they are made, ideally at compile time. 
In this case, you don’t discover the error until runtime, long after it has happened, and in code that may be distant from the code containing the error.



 it is leagal 合法(語法) to use raw types (generic types without their type parameters), 但你不應該用(除了下面提到的兩個情況)
 
會合法是為了 **migration compatibility**, drove the decisions to support raw types and to implement generics using erasure (Item 28).




那 raw type List and the parameterized type `List<Object>?` 的差異?
前者讓你跳脫 泛型檢查，後者沒有，還是會檢查(explicitly told the compiler that it is capable of holding objects of any type)


```java
// Fails at runtime - unsafeAdd method uses a raw type (List)!

public static void main(String[] args) {  
	List<String> strings = new ArrayList<>(); 
	unsafeAdd(strings, Integer.valueOf(42));  
	String s = strings.get(0); // Has compiler-generated cast

}

private static void unsafeAdd(List list, Object o) {  // <--這裡用raw type, 因此 compiler 只會噴 warning
	list.add(o);

}
```

This program compiles, but because it uses the raw type List, you get a warning:
```shell

Test.java:10: warning: [unchecked] unchecked call to add(E) as a member of the raw type List
             list.add(o);
                     ^


```


This is a compiler-generated cast, so it’s normally guaran teed to succeed, but in this case we ignored a compiler warning and paid the price.
如果你也忽略這個 warning -> run time 會噴 ex 喔



如果你不用 raw type, 你用  `List<Object>` in the unsafeAdd declaration
compiler 會噴錯，馬上提醒你

```java

Test.java:5: error: incompatible types: List<String> cannot be
         converted to List<Object>
             unsafeAdd(strings, Integer.valueOf(42));
                 ^

```




這邊不知道 type, 用 raw Type? `Set` 可以嗎?
```java

// Use of raw type for unknown element type - don't do this! 
static int numElementsInCommon(Set s1, Set s2) {
             int result = 0;
             for (Object o1 : s1)
                 if (s2.contains(o1))
                     result++;
             return result;
         }

```
不可以

如果你不知道type. 用 wild card `Set<?>`, 而不是 raw type `Set`

```java

// Uses unbounded wildcard type - typesafe and flexible 
static int numElementsInCommon(Set<?> s1, Set<?> s2) { ... }

```



unbounded wildcard `type Set<?>` and the raw `type Set` 差異在哪裡? 前者有檢查的


**you can’t put any element (other than null) into a `Collection<?>`.** 
Attempting to do so will generate a compile-time error message like this:
```java

WildCard.java:13: error: incompatible types: String cannot be
   converted to CAP#1
       c.add("verboten");
             ^
     where CAP#1 is a fresh type-variable:
       CAP#1 extends Object from capture of ?



```


Not only can’t you put any element (other than null) into a` Collection<?>`, but you can’t assume anything about the type of the objects that you get out. If these restrictions are unacceptable, you can use generic methods (Item 30) or bounded wildcard types (Item 31).


不要用raw type 除了, 以下兩個情況

1.
class literals 得使用 raw types, 因為本來就無法使用 parameterized types
因為 class literals 是用在 run time 下拿 class meta data 用的 
see [[Java Class Literals]]

In other words,` List.class`, `String[].class`, and` int.class` are all legal
but `List<String>.class` and `List<?>.class` are not legal in class literal


2.
可用在 `instanceof` operator. 
因為這也是 run time 用的
and generic type information  在 runtime 會被拿掉，因此 illegal to use the instanceof operator on parameterized types

你是可以用unbounded wildcard types  在 instanceof, 但是沒有幫助，因此沒必要讓 code 變複雜。


This is the preferred way to use the instanceof operator with generic types:
```java

// Legitimate use of raw type - 
instanceof operator if (o instanceof Set) { // Raw type
	Set<?> s = (Set<?>) o; // Wildcard type
	... 
}

```

另外，當你判定後 o is a Set 後，你要 cast 為 wildcard type `Set<?>`, 不是 the raw `type Set`. 
This is a checked cast, so it will not cause a compiler warning.



### summary

- 90% 的情況，不要 using raw types (`Set`, `List`), 會 run time 噴異常
- 要用 參數話的泛型，like `Set<String>`, even `Set<Object>`, 這樣compiler 才會幫你檢查 type
- raw type 是為了相容於以前的 Java 5 存在的
-  `Set<?>` is a wildcard type 可以讓你放 unknown type or 你不 case 的情況，這邊還是會幫你進行"一定程度的"檢查。
- raw types (`Set`, `List`) 只會用在 class literals 和 instanceof 運算子

### 泛型名詞解釋
| Term | Example | Item |
|---|---|---|
| Parameterized type | `List<String>` | Item 26 |
| Actual type parameter | `String` | Item 26 |
| Generic type | `List<E>` | Items 26, 29 |
| Formal type parameter | `E` | Item 26 |
| Unbounded wildcard type | `List<?>` | Item 26 |
| Raw type | `List` | Item 26 |
| Bounded type parameter | `<E extends Number>` | Item 29 |
| Recursive type bound | `<T extends Comparable<T>>` | Item 30 |
| Bounded wildcard type | `List<? extends Number>` | Item 31 |
| Generic method | `static <E> List<E> asList(E[] a)` | Item 30 |
| Type token | `String.class` | Item 33 |
