

如何在 Java 中創建類型安全的異構容器。

異構容器是指可以儲存不同類型物件的容器，而類型安全則是指編譯器可以幫助我們在編譯時發現類型錯誤，避免在運行時出現 `ClassCastException`

**問題：**
在 Java 中，如果我們使用泛型來創建一個容器，例如 `Map<String, Object>`，雖然可以儲存不同類型的值，但在取值時需要進行強制類型轉換，這可能會導致運行時錯誤。


**解決方案：**
可以使用類型令牌 (type token) 來實現類型安全的異構容器。
類型令牌是一個包含泛型類型資訊的物件。我們可以使用匿名類別來創建類型令牌。



**例子：**
**解釋：** 看 comment
```java
package org.example.effectiveJava;

import java.util.HashMap;
import java.util.Map;
import java.util.Objects;

public class Favorites {

    // Favorites 類別使用 Map<Class<?> Object> 來儲存不同類型的物件。
    // Class<?> 作為 key，表示物件的類型；Object 作為 value，表示物件本身。
    private final Map<Class<?>, Object> favorites = new HashMap<>();


    // putFavorite() 方法使用 type.cast(instance) 來確保插入的物件類型與 key 的類型一致。
    // 這增加了類型安全，如果類型不匹配，會在編譯時或運行時拋出 ClassCastException
    public <T> void putFavorite(Class<T> type, T instance) {
        favorites.put(Objects.requireNonNull(type), type.cast(instance)); // 防止插入錯誤類型
    }

    // getFavorite() 方法使用 type.cast() 將取出的物件轉換為指定的類型。
    // 由於 putFavorite() 方法已經保證了類型安全，因此這裡的類型轉換是安全的。
    public <T> T getFavorite(Class<T> type) {
        return type.cast(favorites.get(type));
    }

    public static void main(String[] args) {

        Favorites f = new Favorites();
        f.putFavorite(String.class, "Java");
        f.putFavorite(Integer.class, 0xcafebabe);
        f.putFavorite(Class.class, Favorites.class);

        String favoriteString = f.getFavorite(String.class);
        int favoriteInteger = f.getFavorite(Integer.class);
        Class<?> favoriteClass = f.getFavorite(Class.class);

        System.out.println(favoriteString); // Java
        System.out.println(favoriteInteger); // 3405691582
        System.out.println(favoriteClass); // class Favorites
    }
}

```


**何時使用：**
* 需要創建一個可以儲存不同類型物件的容器。
* 需要在編譯時確保類型安全。


**何時不應該使用：**
* 容器只需要儲存單一類型的物件。這種情況下，使用普通的泛型容器即可，例如 `List<String>`。
* 效能非常關鍵。使用類型令牌會有一些效能開銷，因為需要進行類型檢查和轉換。


**優點：**
* 類型安全：在編譯時或運行時可以發現類型錯誤。
* 靈活性：可以儲存不同類型的物件。


**缺點：**
* 相比於普通的泛型容器，程式碼略微複雜。
* 有一些效能開銷。


**總結：**
類型安全的異構容器提供了一種在 Java 中安全地儲存和檢索不同類型物件的方法。
使用類型令牌可以避免運行時類型錯誤，並提高程式碼的可讀性和可維護性。 
然而，在使用之前，需要權衡其優缺點，並根據實際情況選擇是否使用。


