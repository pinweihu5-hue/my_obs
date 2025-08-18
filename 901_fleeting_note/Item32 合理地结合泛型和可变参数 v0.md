Combine generics and varargs judiciously

雖然可變參數和泛型都是 Java5引進，但他們本身的互動並不是很好

可變參數是放在 arr, 因此如果一個函數宣告可變參數的 type 是泛型，那compilier 會有下面的警告
(原因上面已經提到，arr 是 refiiable type, 泛型是 non-refiiable type 因此 not go well together)
![[IMG-Item32 合理地结合泛型和可变参数 v0-20250805201148661.png]]

Heap pollution 是指一個變數的參數化type(泛型) refer 到的物件不是那個變數的 type
這會讓 compiler cast 失敗，最後泛型的機制就失效了



For example, consider this method, which is a thinly disguised variant of the code fragment on page 127:
然後這個函數會噴 ClassCastException
![[IMG-Item32 合理地结合泛型和可变参数 v0-20250805201149281.png]]


你會想要問，為何明明搭配有問題，還有問題，卻還是可以讓我們這樣寫?
因為語言設計者覺得有時候還是很有用！

 In fact, the Java libraries export several such methods, including `Arrays.asList(T... a)`, `Collections.addAll(Collection<? super T> c`, `T... elements)`, and `EnumSet.of(E first, E... rest)`, 不過這些是 typeSafe 的

Java7以前的版本，你需要在每一個這個這種地方都加上 @SuppressWarnings("unchecked")，很煩

Java7後，method的實作者可以使用 SafeVarargs annotation, 這樣使用者就不需要 SuppressWarnings了。本質上，就是作者透過這個註釋來跟使用者說這個 method 是 typesafe


那你要怎麼確認一個 函數是否可以加上 @SafeVarargs? 如何知道 it is safe?

如果一個 method doesn’t store anything into the array (which would overwrite the parameters)
並且不會allow指向的 arr 可以 escape (which would enable untrusted code to access the array), then it’s safe. 



下面是一個不安全的例子
這些 arg 可以 reference to 其他地方
![[IMG-Item32 合理地结合泛型和可变参数 v0-20250805201150126.png]]

這個函數看起來很簡單，就是簡單的把傳入的可變參數 return 而已
但是 compiler 無法確認這些 arg 的 type, 另外這些函數直接 return 這些 arr, 因此 heap pollution 可以跨越很多 call stack


我們來看一個例子 pickTwo
裡面使用上面的 toArray, 返回 ...arg
![[IMG-Item32 合理地结合泛型和可变参数 v0-20250805201151046.png]]
這個函數看起來也是無害, 除了他直接ret varargs parameyter

當你 compile, compiler 會推論 2個 T 實例, 放到 array of type Object[], 這是 compiler 最多可以做的，因為資訊就只有T, 他也只能用 Object 來處理,最後pickTwo return 的是 an array of type Object[]

Now consider this main method, which exercises pickTwo:
這邊也沒有 警告，但會噴了, ClassCastException
噴的原因是，because Object[] is not a subtype of String[]
![[IMG-Item32 合理地结合泛型和可变参数 v0-20250805201151911.png]]

這個例子是讓你知道， it's unsafe 去使用另一個函數去 access 一個泛型的可變參數, 除非..
1. it is safe to pass the array to another varargs method that is correctly annotated with @SafeVarargs
2. it is safe to pass the array to a non-varargs method that merely computes some function of the contents of the array



下面是一個安全的使用泛型可變參數的例子
呼叫他的人不會有 warning, 因為有 @SafeVarargs

![[IMG-Item32 合理地结合泛型和可变参数 v0-20250805201152775.png]]

SafeVarargs的用法很簡單：
- 只要你寫的 method 是泛型可變參數，就要用，這樣你的函數的使用者就不會被 warning 困擾
- 如果你的mehthod 有 compiler warning-> fix it and make it safe



As a reminder, a generic varargs methods is safe if:
1. it doesn’t store anything in the varargs parameter array, and
2. 這個函數不會讓 array (or a clone) 可以被任何不受信任的code可見
fix it 如果違背上述兩點



另外 SafeVarargs annotation 只可以用在無法 override 的函數上，因為你無法確認被 override的函數一樣安全。

Java8, 你只可以用在 static methods and final instance methods
Java9. 你也可以用在 private instance method


或是你直接不要用可變參數，改用 list就好
![[IMG-Item32 合理地结合泛型和可变参数 v0-20250805201153699.png]]
然後你還可以拿這個函數去跟  static factory method `List.of` 一起用，這樣你還是可以使用可變參數
因為 List.of 的宣告有 @SafeVarargs
`audience = flatten(List.of(friends, romans, countrymen));`

這樣做的好處就是你不用自己搞 SafeVarargs 的 method了，你還得自己證明安全
壞處就是你的code變多，然後也可能慢一點點


上面我們的 pickTwo也可以這樣用, 使用 `List.of`
![[IMG-Item32 合理地结合泛型和可变参数 v0-20250805201154602.png]]

and the main method becomes this:
這是 typeSafe
![[IMG-Item32 合理地结合泛型和可变参数 v0-20250805201155395.png]]


In summary
- varargs and generics do not interact well because the varargs facility is a leaky abstraction built atop arrays, and arrays have different type rules from generics. 
- Though generic varargs parameters are not typesafe, they are legal. If you choose to write a method with a generic (or parameterized) varargs parameter, first ensure that the method is typesafe, and then annotate it with @SafeVarargs so it is not unpleasant to use.