
這是用來表示 在 runtime 下， class or interface 是哪一個的 的 expression
它們提供了一種取得 `Class` 物件的方式，`Class` 物件代表了類別本身的 meata data


**語法：** `XXXX.class`，其中 `XXXX` 是類別或介面的名稱。

**例子：**

```java

Class<String> stringClass = String.class;
Class<Integer> integerClass = Integer.class;


// 介面也可以有類別字面值
Class<List> listClass = List.class; 


```

**用途：**

* **反射 (Reflection):** 類別字面值是反射的基礎，可以用來在運行時檢查類別的結構、成員變數和方法，以及創建新的實例。
* **類型檢查:** 可以使用 `instanceof` 運算符和 `Class.isInstance()` 方法來檢查物件的類型。
* **泛型類型推斷:**  在使用泛型方法時，編譯器可以使用類別字面值來推斷類型參數。
* **動態載入類別:**  可以使用 `Class.forName()` 方法根據類別名稱動態載入類別。
* **獲取類別相關資訊:** 可以通過 `Class` 物件獲取類別的名稱、父類別、介面、註解等資訊。


**何時使用類別字面值：**
* **需要在運行時操作類別:** 例如，使用反射來創建類別的實例、調用方法或訪問成員變數。
* **需要進行類型檢查:** 例如，檢查物件是否屬於特定類型。
* **需要動態載入類別:** 例如，根據配置文件中的類別名稱載入不同的類別。
* **需要獲取類別相關資訊:** 例如，獲取類別的註解或實現的介面。


**何時不應該使用類別字面值 (或應該謹慎使用)：**
* **過度使用反射:** 反射會降低效能，並且使程式碼更難以理解和除錯。如果可以在編譯時確定類型，則應盡量避免使用反射。
* **安全性問題:**  反射可以繞過訪問控制修飾符，例如 `private` 和 `protected`，這可能會導致安全問題。在安全性敏感的應用程式中，應謹慎使用反射。


**例子 - 使用反射創建 class instance：**
```java
public class ClassLiteralExample {

    public static void main(String[] args) throws Exception {
    
        Class<ArrayList> arrayListClass = ArrayList.class;

        // 使用 class literals get ctor and new ArrayList 的實例
        ArrayList<String> list = arrayListClass.getDeclaredConstructor().newInstance();

        list.add("apple");
        list.add("banana");

        System.out.println(list); // 輸出 [apple, banana]
    }
}
```

**例子 - 使用類別字面值進行類型檢查：**
```java
public class ClassLiteralExample {

    public static void main(String[] args) {
    
        Object obj = "Hello";

        if (obj instanceof String) {
            System.out.println("obj is a String");
        }

        if (String.class.isInstance(obj)) {
            System.out.println("obj is a String (using Class.isInstance())");
        }
    }
}
```


**總結:**

類別字面值是 Java 中一個強大的特性，提供了在 run time 可以 操作 class/type 的機制。  
它們在反射、類型檢查、動態載入類別等方面都扮演著重要的角色。  
然而，過度使用反射可能會降低效能，並增加程式碼的複雜性。  在使用類別字面值和反射時，應謹慎考慮其優缺點，並確保程式碼的安全性。
