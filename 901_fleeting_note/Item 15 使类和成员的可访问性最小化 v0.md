Minimize the accessibility of classes and members

就是所謂的 infomationa hiding 或封裝 encapsulation, 此為軟體設計的基石

可以 decouple system
好維護


你讓他private,
後續你可以改動，調整，移除內部實作，都不會影響外部使用
你變成 public, 你就會需要外部調整，如果你是 public 給大家用的，那基本上你就要維護他，和考慮相容性。


原則: 
- 盡量讓 class or member as inaccessible as possible
- 根據需求，讓越多可以 hide 越好


top level class/ interface 只能是 public or package-private, 但如果可以用 package-private 就用 package-private


如果 package-private class/interface 只被一個 class 用，考慮把它變成那個 class 的 private static nested class

對於 member 來說，有以下四種 access level

- private
	- 只有宣告的 class 自己 可以用
- package-private
	- 宣告 class 所在的 pkg 裡面的 class 都可以
	- this is default 設定如果你沒有設定的話
	- PS: interface member default 是 public, 不一樣喔
- protected
	- subclass 可以用(除了一些限制), 同一個 package 的 class 也可以用
- public
	- 大家都可以用這個 class 的 member




如果你的 class 是 pub, 盡量讓 memebr 是 private
除非同pkg 的 class 需要 access, 那就用 pkg-private

如果你一直遇到無法 make it private, 需要用 pkg-private, 可以看看是否是設計上需要調整

PS: private and pkg-pri 是內部實作不影響 API, 但如果 class 有實作 Serializable (Items 86 and 87), 還是有可能會 leak 實作






A protected member 也是 class’s exported API 的一部分喔，因此也需要 support forever.
意指:  represents a public commitment to an implementation detail (Item 19)
你應該會很少需要使用 protected member 才對。



會讓你無法降低 methods 的 accessibility因素?

如果你是 overrieds super class method, 你無法用比 super class 更嚴格的 access level -> 會噴錯。
(This is necessary to ensure that an instance of the subclass is usable anywhere that an instance of the superclass is usable (the Liskov substitution principle, see Item 15))



interface不太一樣: 如果一個 class 實現了一個接口（interface），那麼該class中所有實現接口的方法必須被聲明為 `public`。這是因為接口中的方法默認是 `public` 的，並且任何實現該接口的class都必須遵循這一規則，以確保接口的可見性和一致性



有時候為了 test, 你會想要讓 memebr 有更好的access level, 不過最多就是package-private, 這也應該夠了。


公開類的實例欄位應該很少需要是 pub  (Item 16)

如果你的實例欄位不是 final, 或是指向 metable obj ，又是 pub, 你基本上放棄了你可以控制這個欄位的能力
 - 你無法限制這個欄位
 - 當這個欄位被modifed, 你也無法 take acction
 - 也不會是 thread safe

如果你的欄位是 final 但指向 mutable obj, 你如果 make it public -> 你一樣是放棄了去切換到一個新的內部結構的彈性。


靜態欄位一樣遵循上面的規則，不過有一點差別，你可以暴露 constants 透過 public static final fields, 給定這個 constants  form an integral part of the abstraction provided by the class.

慣例是這個欄位會是 大寫, like APPLE_PIE

另外這些欄位應該要是 primitive values or references to immutable objects (Item 17)

欄位如果是指向 mutable obj, 那跟 沒final 的欄位一樣的問題。

欄位如果是指向 mutable obj，雖然指向的位置無法調整，但是指向的內容是可以調整的。


留意 nonzero-length array is always mutable
因此如果有 class 有一個 public static final array field 或是有任何 getter 可以拿到這個欄位，那就是錯的。
因為這意思就是有人可以從外部更改這個 arr 的內容，這基本上就是資安漏洞。
小心一些 IDE 自動生產的getter 會自動造成這個問題。
![[IMG-Item 15 使类和成员的可访问性最小化 v0-20250805201134247.png]]






通常有兩種解法。
你可以改為private, 然後提供一個public 去拿一個 immutable list
![[IMG-Item 15 使类和成员的可访问性最小化 v0-20250805201134505.png]]


或是，一樣改為private, 提供一個 public method 返回一個 copy 的 private arr
![[IMG-Item 15 使类和成员的可访问性最小化 v0-20250805201134989.png]]


哪一個比較好？取決於客戶使用情況，哪一個客戶使用上比較方便，客戶是否有效率考慮等等。


Java9 引入模組系統
你可以把很多類包在模組裡面，然後用 module-info.java 來規定哪些類在個特定的模組然後 export 出去


unexported 類的公開和保護成員不會被外部取得
模組裡面，accessibility的規則一樣

模組系統的好處是，你可以 share class among pkgs 在同一個模組裡面，而不用讓全部的 class 都是 public


不過模組系統目前是建議性的居多，如果你把 module’s JAR file 放在 on your application’s class path instead of its module path, the packages in the module revert to their non-modular behavor

基本上 JDK 來強制模組系統，你需要依照他的規則放在 module path
要做到這樣，你需要
- you must group your packages into modules, make all of their dependencies explicit in module declaration
- rearrange your source tree
-  and take special actions to accommodate any access to non-modularized packages from within your modules

基本上要看你的系統有沒有這些 enforement



To summarize:
- you should reduce accessibility of program elements as much as possible (within reason). 
- After carefully designing a minimal public API, you should prevent any stray(迷失的) classes, interfaces, or members from becoming part of the API. 
- With the exception of public static final fields, which serve as constants
- public classes should have no public fields. 
- Ensure that objects referenced by public static final fields are immutable.