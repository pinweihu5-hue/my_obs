Java 提供了幾個類別來產生隨機數，主要分為以下兩種：

# **1. `java.util.Random` 類別：**

   - **基本用法：** `Random` 類別是最常用的隨機數產生器。它使用一個種子 (seed) 來產生偽隨機數序列。如果使用相同的種子，每次執行程式都會產生相同的隨機數序列。
   - **建構子：**
      - `Random()`: 使用當前時間作為種子，產生不同的隨機數序列。
      - `Random(long seed)`: 使用指定的 `long` 值作為種子，產生可重複的隨機數序列。
   - **常用方法：**
      - `nextInt()`: 產生一個隨機的 `int` 值 (範圍為所有可能的 `int` 值)。
      - `nextInt(int bound)`: 產生一個 0 (包含) 到 `bound` (不包含) 之間的隨機 `int` 值。
      - `nextLong()`: 產生一個隨機的 `long` 值。
      - `nextFloat()`: 產生一個 0.0 (包含) 到 1.0 (不包含) 之間的隨機 `float` 值。
      - `nextDouble()`: 產生一個 0.0 (包含) 到 1.0 (不包含) 之間的隨機 `double` 值。
      - `nextBoolean()`: 產生一個隨機的 `boolean` 值 (true 或 false)。
      - `nextGaussian()`: 產生一個平均值為 0.0，標準差為 1.0 的高斯 (常態) 分布的隨機 `double` 值。
   - **範例：**

     ```java
     import java.util.Random;

     public class RandomExample {
         public static void main(String[] args) {
             Random random = new Random();

             System.out.println("隨機整數: " + random.nextInt());
             System.out.println("0 到 9 的隨機整數: " + random.nextInt(10));
             System.out.println("隨機浮點數: " + random.nextDouble());
             System.out.println("隨機布林值: " + random.nextBoolean());

             // 使用特定種子
             Random seededRandom = new Random(12345);
             System.out.println("使用種子的隨機整數: " + seededRandom.nextInt());
             System.out.println("使用種子的隨機整數: " + seededRandom.nextInt()); // 每次執行都會產生相同的值
         }
     }
     ```

# **2. `java.security.SecureRandom` 類別：**

   - **安全性：** `SecureRandom` 類別提供更強的隨機數產生器，適用於需要高安全性的應用，例如密碼學。它使用作業系統提供的隨機源，產生更難以預測的隨機數。
   - **建構子：**
      - `SecureRandom()`: 使用預設的隨機源。
   - **方法：**
      - 它的方法與 `Random` 類別類似，例如 `nextInt()`, `nextLong()`, `nextDouble()`, `nextBytes()` 等。
   - **範例：**

     ```java
     import java.security.SecureRandom;
     import java.util.Arrays;

     public class SecureRandomExample {
         public static void main(String[] args) {
             SecureRandom secureRandom = new SecureRandom();

             System.out.println("安全的隨機整數: " + secureRandom.nextInt());
             System.out.println("安全的隨機浮點數: " + secureRandom.nextDouble());

             byte[] randomBytes = new byte[16];
             secureRandom.nextBytes(randomBytes);
             System.out.println("安全的隨機位元組: " + Arrays.toString(randomBytes));
         }
     }
     ```

**選擇哪個類別？**

- **`Random`:**
   - 適用於大多數應用，例如遊戲、模擬、測試等。
   - 速度較快。
   - 如果不需要高安全性，則足夠使用。
- **`SecureRandom`:**
   - 適用於需要高安全性的應用，例如密碼學、產生安全金鑰等。
   - 速度較慢。
   - 產生更難以預測的隨機數。

**注意事項：**

- **種子：** 如果需要產生可重複的隨機數序列，請使用相同的種子初始化 `Random` 物件。
- **範圍：** 使用 `nextInt(int bound)` 方法時，請注意 `bound` 是不包含在內的。
- **多執行緒：** 在多執行緒環境中，如果多個執行緒同時使用同一個 `Random` 物件，可能會導致效能問題。建議每個執行緒使用自己的 `Random` 物件，或者使用 `ThreadLocalRandom` 類別 (Java 7 引入)。
- **統計特性：** 偽隨機數產生器產生的數字並非真正隨機，它們是根據演算法產生的。對於需要高度隨機性的應用，可能需要使用更複雜的隨機數產生器或外部隨機源。


# 3. **`java.util.concurrent.ThreadLocalRandom` 類別**

另外 Java 的 `java.util.concurrent` 套件中確實有一個類別可以用來產生隨機數，它就是 `ThreadLocalRandom`。


`ThreadLocalRandom` 是 Java 7 引入的，它專門為多執行緒環境設計，提供了一個更高效且線程安全的隨機數產生方式。

**為什麼需要 `ThreadLocalRandom`？**

在多執行緒環境中，如果多個執行緒同時使用同一個 `java.util.Random` 物件，會發生以下問題：

1. **競爭 (Contention)：** `Random` 類別的方法是同步的，這意味著當多個執行緒同時呼叫 `nextInt()` 等方法時，它們會互相競爭鎖，導致效能下降。
2. **效能瓶頸：** 鎖的競爭會成為效能瓶頸，尤其是在高併發的應用中。

`ThreadLocalRandom` 解決了這些問題，它為每個執行緒都維護一個獨立的隨機數產生器，避免了鎖的競爭，提高了效能。

**主要特性：**

- **線程安全：** 每個執行緒都有自己的 `ThreadLocalRandom` 實例，不會互相干擾。
- **高效：** 避免了鎖的競爭，提高了多執行緒環境下的效能。
- **使用方便：** 提供了與 `Random` 類別類似的方法，例如 `nextInt()`, `nextLong()`, `nextDouble()` 等。
- **隱式初始化：** 不需要顯式創建 `ThreadLocalRandom` 物件，可以直接使用靜態方法 `current()` 取得當前執行緒的實例。

**使用方法：**

```java
import java.util.concurrent.ThreadLocalRandom;

public class ThreadLocalRandomExample {
    public static void main(String[] args) {
        // 取得當前執行緒的 ThreadLocalRandom 實例
        ThreadLocalRandom random = ThreadLocalRandom.current();

        // 使用與 Random 類似的方法
        System.out.println("隨機整數: " + random.nextInt());
        System.out.println("0 到 9 的隨機整數: " + random.nextInt(10));
        System.out.println("隨機浮點數: " + random.nextDouble());

        // 在多執行緒環境中使用
        for (int i = 0; i < 5; i++) {
            new Thread(() -> {
                ThreadLocalRandom threadRandom = ThreadLocalRandom.current();
                System.out.println(Thread.currentThread().getName() + ": " + threadRandom.nextInt(100));
            }).start();
        }
    }
}
```

**與 `Random` 的比較：**

| 特性         | `java.util.Random` | `java.util.concurrent.ThreadLocalRandom` |
|--------------|--------------------|-----------------------------------------|
| 線程安全     | 不安全 (需要同步)  | 安全                                     |
| 效能         | 較低 (多執行緒)   | 較高 (多執行緒)                          |
| 適用場景     | 單執行緒或低併發   | 多執行緒或高併發                         |
| 初始化方式   | 需要顯式創建物件   | 使用 `current()` 方法取得當前執行緒實例 |

**總結：**

- `ThreadLocalRandom` 是 Java 中專為多執行緒環境設計的隨機數產生器。
- 它比 `Random` 更高效且線程安全，避免了鎖的競爭。
- 建議在多執行緒環境中使用 `ThreadLocalRandom` 來產生隨機數。
- 使用 `ThreadLocalRandom.current()` 可以取得當前執行緒的實例。

**何時使用 `ThreadLocalRandom`？**

- 當你的應用程式是多執行緒的，並且需要產生隨機數時。
- 當你需要更高的效能，避免鎖的競爭時。
- 當你不需要顯式管理隨機數產生器的種子時。

總之，`ThreadLocalRandom` 是 Java 中一個非常實用的類別，可以幫助你更高效地在多執行緒環境中產生隨機數。如果你正在開發多執行緒應用，強烈建議你使用它。

