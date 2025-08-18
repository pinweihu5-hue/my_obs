Consider type safe heterogeneousc ontainers

also see [[如何在 Java 中創建類型安全的異構容器]]

Common uses of generics include collections, such as `Set<E>` and `Map<K,V>`, and single-element containers, such as `ThreadLocal<T>` and `AtomicReference<T>`. **In all of these uses, it is the container that is parameterized**. 

上面這些常用例子，是 container 被參數化


有更彈性的作法 -> 我們可以直接把 key 給 參數化


假設一個 Favorites 類，可以放各種 type

The Class 物件 的 type 就是參數的 key

這個 class literal 類字面值 的 type 不是 Class, 是 `Class<T>`

舉例來說，String.class 的 type 是 `Class<String>`, and Integer.class 的 type 是 `Class<Integer>`. 

因此當我們用這種 `Class<T>` 拿來作為函數間的 type 溝通工具時，這又叫做 type token [Bracha04].

It looks just like a simple map, except that the key is parameterized instead of the map. The client presents a Class object when setting and getting favorites. 

Here is the API:
![[IMG-Item33 优先考虑类型安全的异构容器 v0-20250805201149139.png]]

Here is a sample program that exercises the Favorites class, storing, retrieving, and printing a favorite String, Integer, and Class instance
![[IMG-Item33 优先考虑类型安全的异构容器 v0-20250805201149883.png]]
![[IMG-Item33 优先考虑类型安全的异构容器 v0-20250805201150795.png]]


PS: 這邊有一點跟 C 不同， Java 用 `%n`, C 是用 `\n`. In Java, The %n generates the applicable platform-specific line separator, which is \n on many but not all platforms.




這是typesafe, 你給他 string 就是return string
給他 Integer 就是 integer
而且是可以放不同 type


這邊是實作
![[IMG-Item33 优先考虑类型安全的异构容器 v0-20250805201151688.png]]



Each Favorites instance is backed by a private `Map<Class<?>, Object>` called favorites. 

這邊要留意: 不是 map 的 type 是 wildcard type, 是 Key 的 type
因此，每一個 Map 的key 可以放不同的 type, one can be `Class<String>`, the next `Class<Integer>`, and so on. That’s where the heterogeneity comes from.



另外 value 的 type 就是 Object, Java type system無法界定 key 和 value 的關係，所以就是 Object而已。



getFavorite 裡面，因為拿到的是 Object, 但是我們要回傳 T 因此這邊有使用 Class’s cast method
The cast method is the dynamic analogue of Java’s cast operator. It simply checks that its argument is an instance of the type represented by the Class object. If so, it returns the argument; otherwise it throws a ClassCastException. 



Favorites類要留意的地方

客戶端可以破壞 typeSaf, 透過使用 Class object in its raw form, 然後這樣會有警告。
（就類似 你可以放一個 String 到 `HashSet<Integer>` by using the raw type HashSet (Item 26)）

但是我們可以改一下，讓 putFavorite 檢查實例是否真的是對應的 type, 如下：
![[IMG-Item33 优先考虑类型安全的异构容器 v0-20250805201152625.png]]


下段是說 java.util.Collections 也有使用同樣的技巧去檢查客戶端是否有 add 錯誤的 type
There are collection wrappers in java.util.Collections that play the same trick. They are called checkedSet, checkedList, checkedMap, and so forth. Their static factories take a Class object (or two) in addition to a collection (or map). The static factories are generic methods, ensuring that the compile-time types of the Class object and the collection match. The wrappers add reification to the collections they wrap. For example, the wrapper throws a ClassCastException at runtime if someone tries to put a Coin into your `Collection<Stamp>`. These wrappers are useful for tracking down client code that adds an incorrectly typed element to a collection, in an application that mixes generic and raw types.


另外一個注意點，Favorites類無法用在 不可重現類 non-reifiable type (Item 28) (like 泛型)

所以你可以放  String or String[]
但不可以放 `List<String>` -> 會無法 compile

因為你拿不到 `List<String>`的 class object,  `List<String>.class` <-- 沒這個語法

基本上，`List<String>` and `List<Integer>` 共用同一個 Class object, which is `List.class`


==


這邊的 type token `Map<Class<?>, Object>` 是unbounded, 因此 getFavorite and putFavorite 可以吃 any Class object
你也可以讓他有更多限制，透過  bounded type token

例子： The annotations API (Item 39) makes extensive use of bounded type tokens. 

For example, here is the method to read an annotation at runtime. 
This method comes from the AnnotatedElement interface, which is implemented by the reflective types that represent classes, methods, fields, and other program elements:
![[IMG-Item33 优先考虑类型安全的异构容器 v0-20250805201153456.png]]

getAnnotation 的 argument, `annotationType`, is a bounded type token representing an annotation type. The method returns the element’s annotation of that type, if it has one, or null, if it doesn’t. 
本質上來說，一個被注釋的 element 就是一個 型別安全的異質容器, keys 需要是 annotation types



==

假設你有一個物件，type 是 `Class<?>`, 你要pass 這個物件到一個函數，這個函數只吃 bounded type token, like `getAnnotation`
You could cast the object to `Class<? extends Annotation>`,  但是你會拿到一個 cast unchecked warning

怎麼辦? 

使用 asSubclass (在 Java 中，`asSubclass` 是 `Class` 類的一個方法，主要用於類型轉換。這個方法允許你將一個 `Class` 對象轉換為其子類的類型，並且在轉換過程中進行類型檢查，以確保轉換是安全的)





下面就是你如何使用 asSubclass 來 read 一個 annotation, which type is unknown at compile time
![[IMG-Item33 优先考虑类型安全的异构容器 v0-20250805201154357.png]]

In summary
- the normal use of generics, exemplified by the collections APIs, restricts you to a fixed number of type parameters per container. You can get around this restriction by placing the type parameter on the key rather than the container. 
- You can use Class objects as keys for such typesafe heterogeneous containers. 
- A Class object used in this fashion is called a type token. 
- You can also use a custom key type. For example, you could have a DatabaseRow type representing a database row (the container), and a generic type `Column<T>` as its key.


下面是一個使用 `Column<T>` 作為鍵的 `DatabaseRow` 類的簡單示例

```java

import java.util.HashMap;
import java.util.Map;

// 定義 Column 類
class Column<T> {
    private final String name;

    public Column(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

// 定義 DatabaseRow 類
class DatabaseRow {
    private final Map<Column<?>, Object> columns = new HashMap<>();

    // 設置列的值
    public <T> void setValue(Column<T> column, T value) {
        columns.put(column, value);
    }

    // 獲取列的值
    public <T> T getValue(Column<T> column) {
        return (T) columns.get(column);
    }
}

// 使用示例
public class Main {
    public static void main(String[] args) {
        // 創建列
        Column<String> nameColumn = new Column<>("Name");
        Column<Integer> ageColumn = new Column<>("Age");

        // 創建數據庫行
        DatabaseRow row = new DatabaseRow();
        row.setValue(nameColumn, "Alice");
        row.setValue(ageColumn, 30);

        // 獲取列的值
        String name = row.getValue(nameColumn);
        Integer age = row.getValue(ageColumn);

        // 輸出結果
        System.out.println("Name: " + name); // 輸出: Name: Alice
        System.out.println("Age: " + age);   // 輸出: Age: 30
    }
}

```

`Column<T>` 類：這是一個泛型類，用於表示數據庫中的列。它包含一個列名的屬性。

DatabaseRow 類：這個類使用一個 Map 來存儲列及其對應的值。setValue 方法用於設置列的值，而 getValue 方法用於獲取列的值。

使用示例：在 Main 類中，我們創建了兩個列（nameColumn 和 ageColumn），然後創建了一個 DatabaseRow 對象，並設置了這些列的值。最後，我們獲取並輸出了這些值。

這個示例展示了如何使用 `Column<T>` 作為鍵來實現類型安全的異構容器，並且能夠靈活地處理不同類型的數據。