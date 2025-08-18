Prefer lists to arrays


### arr is covariant ; 泛型是 invariant

array 是 covariant: if `Sub` is a subtype of `Super`, then the array type `Sub[]` is a subtype of the array type `Super[]`
Generics 是 invariant: for any two distinct types Type1 and Type2, `List<Type1>` is neither a subtype nor a supertype of `List<Type2>`



因此
```java

// Array, Object[]
// Fails only at runtime!
Object[] objectArray = new Long[1];
objectArray[0] = "I don't fit in"; // Throws ArrayStoreException


// List<Object>
// Fails at compile time
List<Object> ol = new ArrayList<Long>(); // Incompatible types ol.add("I don't fit in");

```

### arr is runtime check; 泛型是 compile time check

Array 的檢查是在 run time
泛型用到 erasure 技術，簡單說就是 compile time 檢查後會 拿掉 type 資訊，然後再進去 runtime -> 也因此可以讓泛型可以向前相容(Java5以前的 code)

因此，
兩者是不太相容的


下面這些 array 的泛型也是錯誤的語法 "generic array creation errors at compile time"
```java

new List<E>[], new List<String>[], new E[]. 

```


### arr is Reifiable type and 泛型 is not Reifiable type

Types such as `E`, `List<E>`, and `List<String>` are technically known as non-reifiable types  -> 就是 以 compile time 資訊為主的 type, like 泛型

Reifiable types is like Array, 以run time 資訊來判定的 type

譯註：Reifiable 的意思是：to make something more real or consider it as real, 可以理解為， run time 實際跑起來的

### 無法用建立泛型 arr 會遇到的麻煩

譬如，不容易用一個泛型colleciton retuen arr ( see Item 33 for a partial solution)

另外你如果用 varargs methods (Item 53 Use varargs judiciously) 和泛型一樣用，會有 warning,
因為 varargs parameters 是放在 arr 中. => The SafeVarargs annotation can be used to address this issue (Item 32 Combine generics and varargs judiciously).

### favor list over arr
如果你想要建立一個泛型arr 遇到 unchecked cast 警告當你 cast on arr -> the best solution is often to use the collection type `List<E>` in preference to the array type `E[]`

say we have below class, 還沒加上泛型
```java

public class Chooser {
       
       private final Object[] choiceArray;
       
       public Chooser(Collection choices) {
           choiceArray = choices.toArray();
       }
       
       public Object choose() {
           Random rnd = ThreadLocalRandom.current();
           return choiceArray[rnd.nextInt(choiceArray.length)];
       } 
}

```


加上泛型，無法 compile
```java

public class Chooser<T> {
	private final T[] choiceArray;
	
	public Chooser(Collection<T> choices) { 
		choiceArray = choices.toArray();
	}
	
    // choose method unchanged
   
}
```

error msg
![[IMG-Item28 列表优于数组 v0-20250805201146722.png]]

我們可以 cast the Object array to a T array: `choiceArray = (T[]) choices.toArray();`

還是會有 warning
![[IMG-Item28 列表优于数组 v0-20250805201147243.png]]


compiler warning 就是跟你說，他無法保證你的 type 在 runtime 的安全喔
因為 compiler 不知道 T 在 run-time 是什麼 (remember, element type information is erased from generics at runtime)

你可以自己驗證是否沒問題然後加上 comment and suppress warning
但是你最好還是看看是否可以 remove this warning (Item 27)

要移除，我們改用 List 就可以
```java


 // List-based Chooser - typesafe

public class Chooser<T> {  
	
	private final List<T> choiceList;

	public Chooser(Collection<T> choices) { 
		choiceList = new ArrayList<>(choices);
	}

	public T choose() {  
		Random rnd = ThreadLocalRandom.current();  
		return choiceList.get(rnd.nextInt(choiceList.size()));
	} 
}
```
This version is a tad more verbose, and perhaps a tad slower, but it’s worth it for the peace of mind that you won’t get a ClassCastException at runtime.


In summary
- arrays and generics have very different type rules. 
- Arrays are covariant and reified; generics are invariant and erased. 
- As a consequence, arrays provide runtime type safety but not compile-time type safety, and vice versa for generics. 
- As a rule, arrays and generics don’t mix well. 
- If you find yourself mixing them and getting compile-time errors or warnings, your first impulse should be to replace the arrays with lists.