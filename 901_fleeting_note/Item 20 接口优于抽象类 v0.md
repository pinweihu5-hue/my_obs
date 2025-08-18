Prefer interfaces to abstract classes


Java has two mechanisms to define a type that permits multiple implementations: interfaces and abstract classes. Since the introduction of default methods for interfaces in Java 8 [JLS 9.4.3], both mechanisms allow you to provide implementations for some instance methods. A major difference is that to implement the type defined by an abstract class, a class must be a subclass of the abstract class. Because Java permits only single inheritance, this restriction on abstract classes severely constrains their use as type definitions. Any class that defines all the required methods and obeys the general contract is permitted to implement an interface, regardless of where the class resides in the class hierarchy.

要讓 type 可以有很多實作， java 有兩種方法 
1. 接口 
2. 抽象類


另外 java8 後 interface 還可提供預設 or 默認方法
[[default methods for interfaces in Java]]

接口的好處是你可以一個 type, 很多實現方法
抽象類你需要透過繼承，但是java只可以單個繼承，就是每個 subclass 只能有一個 super class




Existing classes can easily be retrofitted to implement a new interface. All you have to do is to add the required methods, if they don’t yet exist, and to add an implements clause to the class declaration. For example, many existing classes were retrofitted to implement the Comparable, Iterable, and Autocloseable interfaces when they were added to the platform. Existing classes cannot, in general, be retrofitted to extend a new abstract class. If you want to have two classes extend the same abstract class, you have to place it high up in the type hierarchy where it is an ancestor of both classes. Unfortunately, this can cause great collateral damage to the type hierarchy, forcing all descendants of the new abstract class to subclass it, whether or not it is appropriate.

已經存在的類可以很好修改去增加新的接口，你只需要去新增對應要實作的方法就好。很多類有有實作 Comparable, Iterable, and Autocloseable 等接口。但是要去 繼承新抽象類不太好改，因為只支援單個繼承，你可能要調整你的類的繼承結構，類似在拉一個level出來，多一個抽象類之類的，影響很大。



Interfaces are ideal for defining mixins. Loosely speaking, a mixin is a type that a class can implement in addition to its “primary type,” to declare that it pro- vides some optional behavior. For example, Comparable is a mixin interface that allows a class to declare that its instances are ordered with respect to other mutu- ally comparable objects. Such an interface is called a mixin because it allows the optional functionality to be “mixed in” to the type’s primary functionality. Abstract classes can’t be used to define mixins for the same reason that they can’t be retrofitted onto existing classes: a class cannot have more than one parent, and there is no reasonable place in the class hierarchy to insert a mixin.

接口用來作 mixins 也很適合。
mixin是一種作法， 就是類可以實作另一個 type, 這個 type 有別於本身的type, 讓類可以有更多可選的行為。
像是 Comparable 就是可以 mixin 接口，讓類本身還可以有序的彼此比較。

抽象類無法這樣做。



Type hierarchies are great for organizing some things, but other things don’t fall neatly into a rigid hierarchy. For example, suppose we have an interface representing a singer and another representing a songwriter:

接口也很好用在你的型別並沒有階層的框架
型別有階層(hierarchies) 有時候是需要的，但有些是後不需要，類似 singer and songwriter 如下
```java

public interface Singer {
       AudioClip sing(Song s);
}

public interface Songwriter {
       Song compose(int chartPosition);
}

```

In real life, some singers are also songwriters. Because we used interfaces rather than abstract classes to define these types, it is perfectly permissible for a single class to implement both Singer and Songwriter. In fact, we can define a third interface that extends both Singer and Songwriter and adds new methods that are appropriate to the combination:

你會遇到一些 singers 也會是 songwriter, 因為你用接口，你的類可以簡單的實作這兩個接口
或是你可以用一個整合的接口如下：
```java

 public interface SingerSongwriter extends Singer, Songwriter {
       AudioClip strum();
       void actSensitive();
}

```


You don’t always need this level of flexibility, but when you do, interfaces are a lifesaver. The alternative is a bloated class hierarchy containing a separate class for every supported combination of attributes. If there are n attributes in the type system, there are 2n possible combinations that you might have to support. This is what’s known as a combinatorial explosion. Bloated class hierarchies can lead to bloated classes with many methods that differ only in the type of their arguments because there are no types in the class hierarchy to capture common behaviors.

如果要用 抽象類來做，並且要支持每個情況下的組合，那n個屬性就會有 2^n 個 抽象類, 這叫做 組合爆炸
你會有一堆抽象類幾乎都一樣，就只是 argument type不同而已


Interfaces enable safe, powerful functionality enhancements via the wrapper class idiom (Item 18). If you use abstract classes to define types, you leave the programmer who wants to add functionality with no alternative but inheritance. The resulting classes are less powerful and more fragile than wrapper classes.

如果 wrapper class idiom (Item 18) 提到，接口也讓我們可以做到增強的功能，如果用繼承，程式比較不彈性且脆弱。


When there is an obvious implementation of an interface method in terms of other interface methods, consider providing implementation assistance to programmers in the form of a default method. For an example of this technique, see the removeIf method on page 104. If you provide default methods, be sure to document them for inheritance using the @implSpec Javadoc tag (Item 19).

如果接口裡面有顯而易見的實作，也可以提供預設方法。可以參考 removeIf method on page 104
另外要提供 doc via  @implSpec Javadoc tag
 

There are limits on how much implementation assistance you can provide with default methods. Although many interfaces specify the behavior of Object methods such as equals and hashCode, you are not permitted to provide default methods for them. Also, interfaces are not permitted to contain instance fields or nonpublic static members (with the exception of private static methods). Finally, you can’t add default methods to an interface that you don’t control.

預設方法有一些限制，像是你無法對 equals, hashCode 等等方法提供預設方法
另外接口也不允許包括實例欄位, 或是非公開的靜態欄位



You can, however, combine the advantages of interfaces and abstract classes by providing an abstract skeletal implementation class to go with an interface. The interface defines the type, perhaps providing some default methods, while the skeletal implementation class implements the remaining non-primitive interface methods atop the primitive interface methods. Extending a skeletal implementation takes most of the work out of implementing an interface. This is the Template Method pattern [Gamma95].

可以結合 接口和抽象類的好處
先用接口
然後抽象類實作接口
接口可以有一些預設方法
抽象類可以實作一些接口那邊預設方法沒提供但希望繼承這個抽象類可以直接通用的方法
這樣繼承這個抽象的子類用起來會很輕鬆
這也是  Template Method pattern 的概念
also see [[Java 中的骨架實現 (Skeletal Implementation)]]


By convention, skeletal implementation classes are called AbstractInterface, where Interface is the name of the interface they implement. For example, the Collections Framework provides a skeletal implementation to go along with each main collection interface: AbstractCollection, AbstractSet, AbstractList, and AbstractMap. Arguably it would have made sense to call them SkeletalCollection, SkeletalSet, SkeletalList, and SkeletalMap, but the Abstract convention is now firmly established. When properly designed, skeletal implementations (whether a separate abstract class, or consisting solely of default methods on an interface) can make it very easy for programmers to provide their own implementations of an interface.
這種骨架實現目前習慣叫做  AbstractXXX
XXX 是抽象類實作的接口
Collections Framework裡面有很多都是這種用法，像是 AbstractCollection, AbstractSet, AbstractList, and AbstractMap



 For example, here’s a static factory method containing a complete, fully functional List implementation atop AbstractList:
 下面就是一個靜態工廠方法，裡面使用了 AbstractList
 這個方法把 Array 轉為 List
 also see: [[Java boxing and autounboxing]]
![[IMG-Item 20 接口优于抽象类 v0-20250805201135710.png]]

When you consider all that a List implementation does for you, this example is an impressive demonstration of the power of skeletal implementations. 

上面的 code 說明 骨架實現很好用


Incidentally, this example is an Adapter [Gamma95] that allows an int array to be viewed as a list of Integer instances. Because of all the translation back and forth between int values and Integer instances (boxing and unboxing), its performance is not terribly good. Note that the implementation takes the form of an anonymous class (Item 24).

這邊也剛好是 Adapter pattern 的例子，把一個 arr 看成是 list of Integer實例
因為這邊每一個操作都有 box and unbox, perf 可能不算很好
另外這邊的實作使用  anonymous class (Item 24) 的方法去做


The beauty of skeletal implementation classes is that they provide all of the implementation assistance of abstract classes without imposing the severe constraints that abstract classes impose when they serve as type definitions. 

骨架實作類的好處就是提供了很多抽象類的實作，但是沒有很多限制

For most implementors of an interface with a skeletal implementation class, extending this class is the obvious choice, but it is strictly optional. 

使用者可以直接去繼承這個骨架實作類, 或是..


If a class cannot be made to extend the skeletal implementation, the class can always implement the interface directly. The class still benefits from any default methods present on the interface itself. Furthermore, the skeletal implementation can still aid the implementor’s task. 

如果無法，那可以直接去實作接口, 那還是可以拿到接口提供的預設方法的好處。


 
 The class implementing the interface can forward invocations of interface methods to a contained instance of a private inner class that extends the skeletal implementation. This technique, known as simulated multiple inheritance, is closely related to the wrapper class idiom discussed in Item 18. It provides many of the benefits of multiple inheritance, while avoiding the pitfalls.

上面說的說明請看這個例子 [[Java 骨架實現提供了一種變通方案，可以模擬多重繼承的效果]]


Writing a skeletal implementation is a relatively simple, if somewhat tedious, process. First, study the interface and decide which methods are the primitives in terms of which the others can be implemented. These primitives will be the abstract methods in your skeletal implementation. Next, provide default methods in the interface for all of the methods that can be implemented directly atop the primitives, but recall that you may not provide default methods for Object methods such as equals and hashCode. If the primitives and default methods cover the interface, you’re done, and have no need for a skeletal implementation class. Otherwise, write a class declared to implement the interface, with implementations of all of the remaining interface methods. The class may contain any nonpublic fields ands methods appropriate to the task.

如何寫一個骨架實作?
先寫你要的接口，決定哪些原生方法是你要提供共用的，哪些是你要讓使用者自己去實作的。
然後看這些抽象方法有哪些是你覺得可以提供預設方法的
(不過一些物件方法無法用預設方法，像是 equals and hashCode等)
如果全部你想要共用的方法你都用接口預設方法 cover 了，就結束了!你不需要建立一個 骨架實作類
如果還有遺漏的，你需要建立一個 骨架實作類, 實作這個接口，實作哪些你想要提供共用的實作。


As a simple example, consider the Map.Entry interface. 
The obvious primitives are getKey, getValue, and (optionally) setValue. 
The interface specifies the behavior of equals and hashCode, and there is an obvious implementation of toString in terms of the primitives. Since you are not allowed to provide default implementations for the Object methods, all implementations are placed in the skeletal implementation class:

下面是一個 Map.Entry 接口的例子
決定了三個原生方法  getKey, getValue, and (optionally) setValue
然後因為  equals and hashCode 和 toString 都無法在接口用預設方法做掉，因此在骨架實作類上去實作

![[IMG-Item 20 接口优于抽象类 v0-20250805201136454.png]]
![[IMG-Item 20 接口优于抽象类 v0-20250805201137120.png]]

Note that this skeletal implementation could not be implemented in the Map.Entry interface or as a subinterface because default methods are not permitted to override Object methods such as equals, hashCode, and toString.


Because skeletal implementations are designed for inheritance, you should follow all of the design and documentation guidelines in Item 19. For brevity’s sake, the documentation comments were omitted from the previous example, but good documentation is absolutely essential in a skeletal implementation, whether it consists of default methods on an interface or a separate abstract class.
不要忘了，你這樣也是要讓人家繼承，要遵循 item19, 要有好文件。



A minor variant on the skeletal implementation is the simple implementation, exemplified by AbstractMap.SimpleEntry. A simple implementation is like a skeletal implementation in that it implements an interface and is designed for inheritance, but it differs in that it isn’t abstract: it is the simplest possible working implementation. You can use it as it stands or subclass it as circumstances warrant.

与骨架实现有稍许不同的是简单实现，以 AbstractMap.SimpleEntry 为例。 一个简单的实现就像一个骨架 实现，它实现了一个接口，并且是为了继承而设计的，但是它的不同之处在于它不是抽象的:它是最简单的工作实 现。 你可以按照情况使用它，也可以根据情况进行子类化。


总而言之
- 一个接口通常是定义允许多个实现的类型的最佳方式。 
- 如果你导出一个重要的接口，应该强烈考虑 提供一个骨架的实现类。 
- 在可能的情况下，应该通过接口上的默认方法提供骨架实现，以便接口的所有实现者都可 以使用它。 也就是说，对接口的限制通常要求骨架实现类采用抽象类的形式。