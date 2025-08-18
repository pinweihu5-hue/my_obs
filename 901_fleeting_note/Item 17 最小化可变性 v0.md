Minimize mutability


一個 不可變類，是指至個類的實例是無法被改動的。
這個實例的資訊，在這個實例的生命之其中，都不會變。
很多類都是這種類，like String, the boxed primitive classes, and BigInteger and BigDecimal.

這種類的好處是：
好設計
好實作
少錯誤
更安全(e.g. threade safe)


要讓一個類不可變，依照下面規則
1. 不要提供 setter (known as mutators) 改變類的狀態
2. 讓類無法被 extended -> 防止 careless or malicious subclasses from compromising the immutable behavior of th
	1. 可以透過final 或是其他方法（看下面）
3. 所有欄位都要是 final. 這也讓你獲得 threade safe 而不需要 synchronization
4. 所有欄位都要是 private. 避免如果有 mutable obj 被 access and modify.
	- 技術來說，你公開 immutable obj 或是 原生類也可以，但是如果你讓他私有，你更好，因為你有了未來去調整內部實作的彈性。
5. 確保對 mutable 元件的 access 是 excluive 的
	1. 如果你的 class 有欄位指向 mutable obj, 確保客戶無法 access 到那個 obj
	2. Make defensive copies (Item 50) in ctors, getter 和使用 readObject methods (Item 88).



例子
這邊的加減乘除是 ret 一個 new 的, 而不是去 modify 本來的 實例變數
![[IMG-Item 17 最小化可变性 v0-20250805201134623.png|853]]

好處是，這樣就有了不變性。

這個 class無法讓你修改內部狀態，因此很簡單使用。
因此你建了，那個直就一直跟著那個實例一直下去，都不會變。

如果你有一個可以 mutable的物件，你基本上就需要去思考那個 state 會如何改變，這是完全多了另外一個維度的複雜度出來。



不變性的物件也是線程安全的，不需要synchronization
多線程下的不變性物件的值因為不變性，因此不會非預期的改動，這是最簡單達到線程安全的作法。


不變類會鼓勵使用者 reuse 已經存在的實例
One easy way to do this is to provide public static final constants for commonly used values. 


For example, the Complex class might provide these constants:
![[IMG-Item 17 最小化可变性 v0-20250805201135016.png]]


你可以更進一步：
你可以提供靜態工廠(Item 1), cache 常常被呼叫的 instance  (good for mem and GC cost)
例子：All the boxed primitive classes and BigInteger do this. 

你用靜態工廠的話，也讓你可以之後去新增 cache 功能，而不會影響到客戶端

另外，不變性的物件，you never have to make defensive copies of them (Item 50). 


因為 the copies would be forever equivalent to the originals. 
Therefore, you need not and should not provide a clone method or copy constructor (Item 13) on an immutable class. This was not well understood in the early days of the Java platform, so the String class does have a copy constructor, but it should rarely, if ever, be used (Item 6).



Not only can you share immutable objects, but they can share their internals.  
see [[Java BigInteger 類為例，通過使用符號-大小表示法]]



Immutable objects 也是很好的 building blocks for other objects, whether mutable or immutable. 

It’s much easier to maintain the invariants of a complex object if you know that its component objects will not change underneath it. A special case of this principle is that immutable objects make great map keys and set elements: you don’t have to worry about their values changing once they’re in the map or set, which would destroy the map or set’s invariants.



Immutable objects provide failure atomicity for free (Item 76). Their state never changes, so there is no possibility of a temporary inconsistency.


immutable 類有缺點吧?


有，只要 value 不同，就需要一個新的 obj
如果這樣 obj很大，那就滿 cost 的


Say you have a million-bit BigInteger and you want to change its low-order bit:
![[IMG-Item 17 最小化可变性 v0-20250805201135501.png]]

你只想要改一個bit for 這個 一百萬的 bit 的 物件
你就需要建立一個新的
時間和空間成本就取決於你的 size, 這邊是一百萬!

java.util.BitSet, 是 mutable, 提供一個方法讓你可以進行上面的操作， constant time!
![[IMG-Item 17 最小化可变性 v0-20250805201136323.png]]

因此如果你的不變類有很多步的操作的話，都需要建立很多 big obj, 那成本是很大的。


有兩個 approaches 去應對這種情況：

一種是你可以預測到哪一些常用的 multiple step, 然後把他們變成primitives
因此你就不需要去建立很多個物件

譬如, BigInteger has a package-private mutable “companion class” that it uses to speed up multistep operations such as modular exponentiation. 

使用 companion class 比較麻煩，但是不用擔心，因為 BigInteger 已經幫我做好了 XD


另一種是你無法預測哪些是常用的操作，那你就需要一個 public mutable companion 類來輔助。
像是 String，我們就可以用 StringBuilder (and its obsolete predecessor, StringBuffer)





==

我們來討論另一種設計

除了讓 class 變成 final, 
另一種更彈性的作法：把ctor 變成 private or pkg-private, 然後新增 public 靜態工廠函數來去帶 public ctor

Here’s how Complex would look if you took this approach:
![[IMG-Item 17 最小化可变性 v0-20250805201137034.png]]

這個作法比 final 更好，因為更有彈性，你讓他變成 pkg-private, 因此其他 class 還是可以用
因為是 pkg-private, 因此outside pkg, 也是無法去 extends, 一樣達到我們的目標

然後因為你用靜態工廠來提供實例，你也讓未來如果你要調整實作有了空間，類似新增 cache 等等


BigInteger and BigDecimal 實作的時候，沒有把 class 加上 final
而且現在也來不及改了(for 相容性)

因此如果你寫一個類，然後你有無法信任的 client 會依賴 BigInteger or BigDecimal 的不變性，你需要去額外檢查你的 BigInteger and BigDecimal 是否是真的是他們自己，還是 untrused 子類的實例, 如果是後者，你需要 defensively copy it under the assumption that it might be mutable (Item 50)
![[IMG-Item 17 最小化可变性 v0-20250805201137585.png]]


==


上面提到，不變類會需要所有的欄位都是 final, 這個條件是滿硬的，我們是可以鬆一點點來提高性能。
譬如，有些不變類會一個或多個 nonfinal 欄位，這些欄位可以用來 cache 結果(昂貴的計算成本的結果，或是常常呼叫且不常改變的)

For example, PhoneNumber’s hashCode method (Item 11, page 53) computes the hash code the first time it’s invoked and caches it in case it’s invoked again. This technique, an example of lazy initialization (Item 83), is also used by String.


最後討論一下 serializability 的問題。
如果你的不變性的類有實作 serializability, 並且有欄位指向可變物件，你一定要提供 an explicit readObject or readResolve method, 或是使用 ObjectOutputStream.writeUnshared and ObjectInputStream.readUnshared methods. 這使用來避免攻擊者可以從你的類來建立可變實例，請看 item88 for more. 



結論
- 因為不變性類有很多好處
- 不要預設每一個 getter 都要有 setter, 應該要預設每一個 getter 不應該要有 setter 除非你有好理由
- 不變性的類的壞處是在特定情況下的perf問題，上面有提到方法去解決。
- 你應該總是建立小的不變性的 value object , 像是 PhoneNumber and Complex
- see [[Value Object in Java]]
- PS:  (There are several classes in the Java platform libraries, such as java.util.Date and java.awt.Point, that should have been immutable but aren’t.) 
- 你也應該要讓大的 VO (value object) 可以有不變性， like  String and BigInteger
- 如果你有 perf 問題，你可以提供 public mutable companion class for your immutable class 
- 如果遇到把類變成不變性是不現實的話，盡量讓其可變程度越低越好。越少 mutable reference 越好。
- 盡量都是 final, 使用 nonfinal 要有原因
- 因此，盡量都用 private + nonfinal, 除非你有好原因。
- cotr 建立出來的物件，盡量具有不變性，不要再另外一個地方去 init 一個 class 的狀態，或是可以去 reinit 狀態。Such methods generally provide little if any performance benefit at the expense of increased complexity.
- The CountDownLatch class exemplifies these principles. It is mutable, but its state space is kept intentionally small. You create an instance, use it once, and it’s done: once the countdown latch’s count has reached zero, you may not reuse it.


PS: 本節的  Complex class 是為了教育意義的，不要真的去用，因為在handle  complex NaNs and infinities 是沒有正確去處理的。