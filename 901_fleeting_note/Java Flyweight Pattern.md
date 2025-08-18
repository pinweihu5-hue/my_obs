
Flyweight Pattern（享元模式）是一種結構型設計模式，旨在透過共享物件來減少記憶體使用量，特別是在需要建立大量相似物件的情況下。

它的核心思想是將物件的**內在狀態 (Intrinsic State)** 和 **外在狀態 (Extrinsic State)** 分離，並將內在狀態共享，而外在狀態則由客戶端程式碼維護。

**內在狀態 vs. 外在狀態:**
* **內在狀態 (Intrinsic State):**  物件中不變的、可以共享的部分，例如字型的樣式、顏色等。
* **外在狀態 (Extrinsic State):**  物件中可變的、依賴於上下文的部分，例如字型的大小、位置等。


**Flyweight Pattern 的組成部分：**

* **Flyweight (享元):**  宣告一個介面，讓客戶端程式碼可以透過它來操作享元物件。
* **ConcreteFlyweight (具體享元):**  實現 Flyweight 介面，並儲存內在狀態。
* **FlyweightFactory (享元工廠):**  負責建立和管理享元物件，確保享元物件被共享。
* **Client (客戶端):**  維護外在狀態，並透過 Flyweight 介面操作享元物件。


**範例：**

```java
import java.util.HashMap;
import java.util.Map;

// Flyweight 介面
interface Font {
    void display(int size, int x, int y);
}

// ConcreteFlyweight
class Character implements Font {
    private final char character;

    public Character(char character) {
        this.character = character;
    }

    @Override
    public void display(int size, int x, int y) {
        System.out.println("顯示字元 '" + character + "'，大小：" + size + "，位置：(" + x + ", " + y + ")");
    }
}

// FlyweightFactory
class FontFactory {
    private static final Map<Character, Font> characterCache = new HashMap<>();

    public static Font getCharacter(char character) {
        Font font = characterCache.get(character);
        if (font == null) {
            font = new Character(character);
            characterCache.put(character, font);
        }
        return font;
    }
}

public class FlyweightExample {
    public static void main(String[] args) {
        Font a = FontFactory.getCharacter('a');
        Font b = FontFactory.getCharacter('b');
        Font a2 = FontFactory.getCharacter('a'); // 共享 'a' 的物件

        a.display(12, 10, 20);
        b.display(14, 30, 40);
        a2.display(16, 50, 60); // 使用相同的 'a' 物件
    }
}

```


**何時使用 Flyweight Pattern：**
* **應用程式需要建立大量相似物件，導致記憶體使用量過高。**
* **物件的大部分狀態可以被共享。**
* **將物件狀態外部化可以提高應用程式的靈活性。**


**何時不應該使用 Flyweight Pattern：**
* **物件的狀態大部分是外在狀態，無法共享。**
* **共享物件的成本高於建立新物件的成本。**
* **應用程式對效能要求不高，記憶體使用量不是主要問題。**


**Flyweight Pattern 的優點：**
* **減少記憶體使用量：** 透過共享物件，可以顯著減少記憶體消耗。
* **提高效能：**  減少物件建立的次數，可以提高應用程式的效能。


**Flyweight Pattern 的缺點：**
* **增加程式碼的複雜度：**  需要額外的程式碼來管理享元物件。
* **可能導致效能問題：**  如果共享狀態的成本過高，反而可能降低效能。



**總結：**

Flyweight Pattern 是一種有效的記憶體優化技巧，適用於需要建立大量相似物件的場景。  透過共享物件的內在狀態，可以減少記憶體使用量，提高應用程式的效能。  但是，在使用 Flyweight Pattern 時，需要仔細考慮物件的狀態是否可以共享，以及共享狀態的成本。
