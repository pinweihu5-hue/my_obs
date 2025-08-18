[(808) Records In Java - Full Tutorial - The Best New Java Feature You're Not Using - YouTube](https://www.youtube.com/watch?v=gJ9DYC-jswo)

Java 的 Record（記錄）是一種特殊的類別，旨在簡潔地儲存不可變數據。它從 Java 14 開始引入，作為預覽功能，並在 Java 16 正式成為標準功能。  Record 可以視為一種高度簡化的類別，專門用於儲存數據。

**Record 的主要特性：**

* **不可變性 (Immutability):** Record 的組成元件 (components) 在建立後就無法修改。這確保了數據的完整性和線程安全。
	* 如同加上 final 在 class 的 class and field
* **簡潔的語法:** 使用 `record` 關鍵字即可定義一個 Record，編譯器會自動生成許多標準方法，例如建構子、getter、`equals()`、`hashCode()`、`toString()` 等。
* **名義上的 tuple:**  Record 可以被視為名義上的 tuple（具名元組），它不僅儲存數據，還帶有名稱，提高了程式碼的可讀性。


**範例：**

```java
record Person(String name, int age) {}  // 定義一個名為 Person 的 Record

public class Main {
    public static void main(String[] args) {
        Person person = new Person("Alice", 30);  // 建立 Person 物件
        System.out.println(person.name());  // 輸出 "Alice"
        System.out.println(person.age());   // 輸出 30

        // person.name() = "Bob";  // 編譯錯誤，因為 Record 的組成元件是不可變的
    }
}
```

**Record 與傳統類別的比較：**

| 特性 | Record | 傳統類別 |
|---|---|---|
| 不可變性 | 預設不可變 | 需要自行實現 |
| 語法 | 簡潔 | 繁瑣 |
| 方法 | 自動生成 | 需要自行編寫 |
| 繼承 | 不可繼承其他類別 | 可以繼承其他類別 |


**Record 的優點：**
* **減少樣板程式碼 (Boilerplate Code):**  不需要手動編寫建構子、getter、`equals()`、`hashCode()`、`toString()` 等方法。
* **提高程式碼可讀性:**  簡潔的語法讓程式碼更易讀易懂。
* **增強數據完整性:**  不可變性確保了數據的安全性。


**Record 的使用場景：**
* **儲存數據物件 (Data Objects):**  例如 DTO (Data Transfer Object)、Entity 等。
* **作為函數式程式設計的數據載體:**  Record 的不可變性使其非常適合用於函數式程式設計。
* **簡化 API 設計:**  使用 Record 可以簡化 API 的介面和數據結構。



**總結：**

Java Record 是一種簡潔、高效、安全的數據載體，它可以減少樣板程式碼，提高程式碼可讀性，並增強數據完整性。  在需要儲存不可變數據的場景下，Record 是比傳統類別更佳的選擇。



---
也是可以 override Record, 但有限制

like 可以用 static field, 不可以用 instance field

![[IMG-Java Record-20250805201157678.png]]


override 預設的 ctor
類似你想要加上更多檢查
![[IMG-Java Record-20250805201158468.png]]

或是用更簡潔的寫法, compact ctor
![[IMG-Java Record-20250805201159477.png]]