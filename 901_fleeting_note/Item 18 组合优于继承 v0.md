Favor composition over inheritance

Inheritance is a powerful way to achieve code reuse, but it is not always the best tool for the job. Used inappropriately, it leads to fragile software.


It is safe to use inheritance within a package, where the subclass and the superclass implementations are under the control of the same programmers. It is also safe to use inheritance when extending classes specifically designed and documented for extension (Item 19). 

**Inheriting from ordinary concrete classes across package boundaries, however, is dangerous.** 

As a reminder, this book uses the word “inheritance” to mean implementation inheritance (when one class extends another). The problems discussed in this item do not apply to interface inheritance (when a class implements an interface or when one interface extends another).



**Unlike method invocation, inheritance violates encapsulation [Snyder86]. In other words, a subclass depends on the implementation details of its superclass for its proper function.** 

後果?
The superclass’s implementation may change from release to release, and if it does, the subclass may break, even though its code has not been touched XDDD 

As a consequence, a subclass must evolve in tandem with its superclass, unless the superclass’s authors have designed and documented it specifically for the purpose of being extended.


To make this concrete, let’s suppose we have a program that uses a HashSet. To tune the performance of our program, we need to query the HashSet as to how many elements have been added since it was created (not to be confused with its current size, which goes down when an element is removed). To provide this functionality, we write a HashSet variant that keeps count of the number of attempted element insertions and exports an accessor for this count. The HashSet class contains two methods capable of adding elements, add and addAll, so we override both of these methods:

```java

// Broken - Inappropriate use of inheritance!
public class InstrumentedHashSet<E> extends HashSet<E> {

    // The number of attempted element insertions
    private int addCount = 0;


    public InstrumentedHashSet() {}

    public InstrumentedHashSet(int initCap, float loadFactor) {
        super(initCap, loadFactor);
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }
}


```

This class looks reasonable, but it doesn’t work. Suppose we create an instance and add three elements using the addAll method. Incidentally, note that we create a list using the static factory method List.of, which was added in Java 9; if you’re using an earlier release, use Arrays.asList instead:
![[IMG-Item 18 组合优于继承 v0-20250805201134967.png]]

這時候去看，應該是 3, 但卻是6, why?
We would expect the getAddCount method to return three at this point, but it returns six. What went wrong? 


Internally, HashSet’s addAll method is implemented on top of its add method, although HashSet, quite reasonably, does not document this implementation detail. The addAll method in Instrumented-HashSet added three to addCount and then invoked HashSet’s addAll implementation using super.addAll. This in turn invoked the add method, as overridden in InstrumentedHashSet, once for each element. Each of these three invocations added one more to addCount, for a total increase of six: each element added with the addAll method is double-counted.



We could “fix” the subclass by eliminating its override of the addAll method. While the resulting class would work, it would depend for its proper function on the fact that HashSet’s addAll method is implemented on top of its add method. This “self-use” is an implementation detail, not guaranteed to hold in all implementations of the Java platform and subject to change from release to release. Therefore, the resulting InstrumentedHashSet class would be fragile.

我們用 super.xxx 去呼叫 super 就是 depend on super class 的實作
會無法預期的發生上面的行為



It would be slightly better to override the addAll method to iterate over the specified collection, calling the add method once for each element. This would guarantee the correct result whether or not HashSet’s addAll method were implemented atop its add method because HashSet’s addAll implementation would no longer be invoked. This technique, however, does not solve all our problems. It amounts to reimplementing superclass methods that may or may not result in self-use, which is difficult, time-consuming, error-prone, and may reduce performance. Additionally, it isn’t always possible because some methods cannot be implemented without access to private fields inaccessible to the subclass.

還是有方法可以解，但是 super class 的調整都會有風險
此外有時候 super class 的私有 mehotd 你也無法 access, 有可能 you can't fund walkaround


A related cause of fragility in subclasses is that their superclass can acquire new methods in subsequent releases. Suppose a program depends for its security on the fact that all elements inserted into some collection satisfy some predicate. This can be guaranteed by subclassing the collection and overriding each method capable of adding an element to ensure that the predicate is satisfied before adding the element. This works fine until a new method capable of inserting an element is added to the superclass in a subsequent release. Once this happens, it becomes possible to add an “illegal” element merely by invoking the new method, which is not overridden in the subclass. This is not a purely theoretical problem. Several security holes of this nature had to be fixed when Hashtable and Vector were retrofitted to participate in the Collections Framework.
上段解說：
有一個需求，就是我們想要對 insert element 確認是否符合某個predicate
一個簡單作法就是直接 subclass 這個 class, 然後對某個 method override, 加上predicate確認, 然後再 super.xxx 之類的
有一天， super class release new version and 新增method, subclass 沒有動，但是因為給這個新增的 method 增加檢查, 就出現 security 風險了, 這是真實案例，發生在 Collections Framework







上面都是 override 造成的問題，那如果我們都不 override , 只新增 class, 就安全了?

沒有喔

如果新的 superclass 新增 method 剛好跟你撞到一樣的簽名
- 如果 ret type 不同 -> 根本無法 compile
- 如果 ret type 剛好一樣  -> 你就回到 override, 跟上面一樣
- 然後你的 method 跟 superclass 的新增method 基本上有不同的意涵，是有可能出現其他問題。




要避免上面的問題，不要用 extends
我們用一個私有欄位，refer to 這個要用的 class

這就是 composition 組合

叫做組合是因為這個要用的class 變成的當下 class的一部分


然後我們用 forwarding:
Each instance method in the new class invokes the corresponding method on the contained instance of the existing class and returns the results. This is known as forwarding, and the methods in the new class are known as forwarding methods. The resulting class will be rock solid, with no dependencies on the implementation details of the existing class. Even adding new methods to the existing class will have no impact on the new class. 


To make this concrete, here’s a replacement for InstrumentedHashSet that uses the composition-and-forwarding approach. 
```java


// Wrapper class - uses composition in place of inheritance
public class InstrumentedSet<E> extends ForwardingSet<E> {

    private int addCount = 0;

    public InstrumentedSet(Set<E> s) {
        super(s);
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }
}


// A reusable forwarding class, which contains all of the forwarding methods and nothing else
// The design of the InstrumentedSet class is enabled by the existence of the Set interface, 
// which captures the functionality of the HashSet class. 

public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;

    public ForwardingSet(Set<E> s) {
        this.s = s;
    }

    public void clear() {
        s.clear();
    }

    public boolean contains(Object o) {
        return s.contains(o);
    }

	public boolean isEmpty()
	
	public int size()
	
	public Iterator<E> iterator()
	
	public boolean add(E e)
	
	public boolean remove(Object o)

    public boolean containsAll(Collection<?> c) {
        return s.containsAll(c);
    }

    public boolean addAll(Collection<? extends E> c) {
        return s.addAll(c);
    }

    public boolean removeAll(Collection<?> c) {
        return s.removeAll(c);
    }

    public boolean retainAll(Collection<?> c) {
        return s.retainAll(c);
    }

    public Object[] toArray() {
        return s.toArray();
    }

    public <T> T[] toArray(T[] a) {
        return s.toArray(a);
    }

    @Override
    public boolean equals(Object o) {
        return s.equals(o);
    }

    @Override
    public int hashCode() {
        return s.hashCode();
    }

    @Override
    public String toString() {
        return s.toString();
    }
}

```


不但解耦，也更有彈性。
本來 inheritance-based approach, which works only for a single concrete class and 你需要寫不同的 ctor 去支援不同的 ctor in the superclass


新的作法，你可以直接使用不同且已經存在的 ctor, 如下
![[IMG-Item 18 组合优于继承 v0-20250805201135479.png]]

The InstrumentedSet class can even be used to temporarily instrument a set instance that has already been used without instrumentation:
![[IMG-Item 18 组合优于继承 v0-20250805201136122.png]]


The InstrumentedSet class is known as a wrapper class because each InstrumentedSet instance contains (“wraps”) another Set instance. This is also known as the Decorator pattern [Gamma95] because the InstrumentedSet class “decorates” a set by adding instrumentation.  
see [[Java Decorator Pattern]]


Sometimes the combination of composition and forwarding is loosely referred to as delegation. Technically it’s not delegation unless the wrapper object passes itself to the wrapped object [Lieber- man86; Gamma95].


The disadvantages of wrapper classes are few. 


One caveat is that wrapper classes are not suited for use in callback frameworks, wherein objects pass self-references to other objects for subsequent invocations (“callbacks”). Because a wrapped object doesn’t know of its wrapper, it passes a reference to itself (this) and callbacks elude the wrapper. This is known as the SELF problem [Lieberman86]. 
下面解釋：

"""
在回調框架中，物件會將自身的引用（self-reference）傳遞給其他物件，以便在後續的操作中進行調用（即「回調」）。這種情況下，物件需要能夠獲取到自己的引用，以便在需要時能夠正確地調用自身的方法。
包裝類的特性：

當一個物件被包裝在另一個類中（例如 InstrumentedSet 包裝了 Set），這個包裝類並不會知道被包裝的物件的存在。換句話說，包裝類的內部邏輯無法直接訪問被包裝物件的原始引用。
SELF 問題：

由於包裝類無法獲取到被包裝物件的原始引用，當被包裝的物件在回調中傳遞自身的引用（使用 this）時，實際上傳遞的是包裝類的引用，而不是原始物件的引用。這樣一來，回調將無法正確地訪問到包裝類的功能，這種情況被稱為「SELF 問題」。
"""



效能在實踐上其實沒差
Some people worry about the performance impact of forwarding method invocations or the memory footprint impact of wrapper objects. Neither turn out to have much impact in practice. 


你會覺得 code 很大，但你其實只要寫一次, 好像 Guava 也可以提供套件
It’s tedious to write forwarding methods, but you have to write the reusable forwarding class for each interface only once, and forwarding classes may be provided for you. For example, Guava provides forwarding classes for all of the collection interfaces [Guava].


下面是用的時機，真的必須要是 is-a 的關係
**Inheritance is appropriate only in circumstances where the subclass really is a subtype of the superclass. In other words, a class B should extend a class A only if an “is-a” relationship exists between the two classes**. If you are tempted to have a class B extend a class A, ask yourself the question: Is every B really an A? If you cannot truthfully answer yes to this question, B should not extend A. If the answer is no, it is often the case that B should contain a private instance of A and expose a different API: A is not an essential part of B, merely a detail of its implementation.


java套件也有違背這個規則
like stack 繼承 vector
Properties 繼承 hashTable

There are a number of obvious violations of this principle in the Java platform libraries. For example, a stack is not a vector, so Stack should not extend Vector. Similarly, a property list is not a hash table, so Properties should not extend Hashtable. In both cases, composition would have been preferable.



If you use inheritance where composition is appropriate, you needlessly expose implementation details. The resulting API ties you to the original implementation, forever limiting the performance of your class. More seriously, by exposing the internals you let clients access them directly. At the very least, it can lead to confusing semantics. For example, if p refers to a Properties instance, then p.getProperty(key) may yield different results from p.get(key): the former method takes defaults into account, while the latter method, which is inher- ited from Hashtable, does not. 

就算沒出問題，也讓人困混
say p 是 Properties的實例
p.getProperty(key) 和 p.get(key) 的結果，你覺得應該一樣吧，但可能會不同喔
因為前者是 subclass method, 會設定 default 
後者是直接使用 super class (hashTable) 的 method




Most seriously, the client may be able to corrupt invariants of the subclass by modifying the superclass directly. In the case of Properties, the designers intended that only strings be allowed as keys and values, but direct access to the underlying Hashtable allows this invariant to be violated. Once violated, it is no longer possible to use other parts of the Properties API (load and store). By the time this problem was discovered, it was too late to correct it because clients depended on the use of non-string keys and values.

嚴重的話，會出錯。
設計 Properties 的人本來是想要key and value 都用 string
但是如果你可以直接去用到  Hashtable, 這個 invariant 就破壞了


There is one last set of questions you should ask yourself before deciding to use inheritance in place of composition. Does the class that you contemplate extending have any flaws in its API? If so, are you comfortable propagating those flaws into your class’s API? Inheritance propagates any flaws in the superclass’s API, while composition lets you design a new API that hides these flaws.
然後如果你的 supercalss 本身有一些 設計flaws
你繼承就是一起把這些 flaws 繼承到 subclass



To summarize 
- inheritance is powerful, but it is problematic because it violates encapsulation. 
- It is appropriate only when a genuine subtype relationship exists between the subclass and the superclass. 
- 就算要用, inheritance may lead to fragility if the subclass is in a different package from the superclass and the superclass is not designed for inheritance. 
- To avoid this fragility, use composition and forwarding instead of inheritance, especially if an appropriate interface to implement a wrapper class exists. Not only are wrapper classes more robust than subclasses, they are also more powerful.