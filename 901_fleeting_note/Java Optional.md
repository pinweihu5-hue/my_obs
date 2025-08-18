
目的是用在 return type, 讓使用的人 expliccit 知道要handle, 不要到處都用


本來要這樣 avoid null ptr exception
![[IMG-Java Optional-20250805201157625.png]]
![[IMG-Java Optional-20250805201158117.png]]




改用 optional 返回
![[IMG-Java Optional-20250805201159207.png]]



可以這樣接，但跟原本一樣 verbose (還是有明確表示的好處辣), 幫助不大，不建議。
![[IMG-Java Optional-20250805201200092.png]]

更好的作法, 直接用，然後可以加上 default
![[IMG-Java Optional-20250805201200979.png]]



orElseGet 跟 verbose 的 optionaCat.get() 一樣
![[IMG-Java Optional-20250805201201661.png]]

也可以 throw
![[IMG-Java Optional-20250805201202237.png]]



optional 是一個 container, 可以用map 去拿裡面的屬性, 裡面放 mapper (這是 java lambda 寫法)
因此這樣拿會更方便
![[IMG-Java Optional-20250805201202850.png]]



請幫我盡量完整的介紹Java optional, 並給我例子，何時要使用，何時不應該使用
---


## Java Optional 完整介紹

Java Optional 是 Java 8 引入的一個容器物件，用於表示值可能存在或不存在（null）。
它的主要目的是**更優雅地處理 null 值，避免 NullPointerException**，並提高程式碼的可讀性。

**Optional 的核心概念：**
Optional 將值包裝在一個容器中，你可以使用其提供的方法來安全地處理值是否存在的情況，而無需直接檢查 null。



**建立 Optional 物件：**
* `Optional.of(T value)`: 建立一個包含非 null 值的 Optional。如果 `value` 為 null，則拋出 `NullPointerException`。
* `Optional.ofNullable(T value)`: 建立一個 Optional，可以包含 null 值。如果 `value` 為 null，則建立一個空的 Optional。
* `Optional.empty()`: 建立一個空的 Optional。

**Optional 的常用方法：**

* `isPresent()`:  檢查 Optional 是否包含值。
* `get()`:  如果 Optional 包含值，則返回該值；否則，拋出 `NoSuchElementException`。
* `orElse(T other)`:  如果 Optional 包含值，則返回該值；否則，返回指定的 `other` 值。
* `orElseGet(Supplier<? extends T> other)`:  如果 Optional 包含值，則返回該值；否則，使用指定的 Supplier 產生一個值並返回。
* `orElseThrow(Supplier<? extends X> exceptionSupplier)`: 如果 Optional 包含值，則返回該值；否則，拋出由指定的 Supplier 產生的異常。
* `ifPresent(Consumer<? super T> consumer)`: 如果 Optional 包含值，則將該值傳遞給指定的 Consumer 執行；否則，不執行任何操作。
* `map(Function<? super T, ? extends U> mapper)`: 如果 Optional 包含值，則將該值傳遞給指定的 Function 進行轉換，並返回一個包含轉換後值的 Optional；否則，返回一個空的 Optional。
* `flatMap(Function<? super T, Optional<U>> mapper)`:  與 `map` 類似，但指定的 Function 必須返回一個 Optional。
* `filter(Predicate<? super T> predicate)`:  如果 Optional 包含值，並且該值滿足指定的 Predicate，則返回包含該值的 Optional；否則，返回一個空的 Optional。


**使用範例：**

```java
import java.util.Optional;

public class OptionalExample {

    public static void main(String[] args) {
        String name = null;
        Optional<String> optionalName = Optional.ofNullable(name);

        // 使用 orElse 提供預設值
        String result = optionalName.orElse("Unknown");
        System.out.println(result); // 輸出 "Unknown"

        // 使用 orElseGet 動態產生預設值 via lambda
        result = optionalName.orElseGet(() -> "Generated Name");
        System.out.println(result); // 輸出 "Generated Name"

        // 使用 ifPresent 執行操作
        optionalName.ifPresent(System.out::println); // 不輸出任何東西

        // 使用 map 轉換值
        Optional<Integer> optionalLength = optionalName.map(String::length);
        optionalLength.ifPresent(System.out::println); // 不輸出任何東西


        String name2 = "John Doe";
        Optional<String> optionalName2 = Optional.ofNullable(name2);
        optionalName2.ifPresent(System.out::println); // 輸出 "John Doe"

        Optional<Integer> optionalLength2 = optionalName2.map(String::length);
        optionalLength2.ifPresent(System.out::println); // 輸出 8


    }
}

```


**何時使用 Optional：**
* **返回可能為 null 的值：**  例如，從資料庫或 API 中查詢數據時。
* **表示值不存在：**  例如，一個使用者可能沒有中間名。
* **鏈式操作：**  Optional 提供了方便的鏈式操作方法，例如 `map`、`flatMap`、`filter` 等。
* **提高程式碼可讀性：**  使用 Optional 可以更清晰地表達值的可能不存在的情況。


**何時不應該使用 Optional：**
* **作為欄位類型：**  不建議將 Optional 用作類別的欄位類型。
* **作為方法參數：**  不建議將 Optional 用作方法的參數類型，除非該參數明確表示值可能不存在。
* **在序列化或反序列化中：** Optional 並非設計用於序列化或反序列化。
* **在集合 collection 中：**  不應該在集合中儲存 Optional 物件。
* **過度使用：**  不要在所有可能為 null 的地方都使用 Optional，只在需要明確表示值可能不存在的情況下使用。


**總結：**
Java Optional 提供了一種更優雅、更安全的方式來處理 null 值，可以有效避免 NullPointerException，並提高程式碼的可讀性和可維護性。  理解何時以及如何正確使用 Optional 對於編寫高質量的 Java 程式碼至關重要。


