`computeIfAbsent` 是 Java 中 `Map` 接口的一個方法，特別是在 `HashMap` 和其他實現了 `Map` 接口的類中可用。這個方法的主要目的是在指定的鍵不存在於地圖中時，計算一個值並將其放入地圖中。這樣可以避免在插入新值時進行額外的查找操作。

### 方法簽名

`computeIfAbsent` 方法的簽名如下：

```java

V computeIfAbsent(K key, Function<? super K, ? extends V> mappingFunction);

```

- **key**：要查找的鍵。
- **mappingFunction**：一個函數，當鍵不存在時用來計算值。

### 工作原理

1. **檢查鍵**：首先，`computeIfAbsent` 會檢查指定的鍵是否已經存在於地圖中。
2. **計算值**：如果鍵不存在，則會調用 `mappingFunction` 來計算一個值。
3. **插入值**：計算出的值會被插入到地圖中，並與鍵關聯。
4. **返回值**：最後，無論鍵是否存在，該方法都會返回與鍵關聯的值。

### 使用場景

`computeIfAbsent` 方法特別適合用於需要懶加載（lazy loading）或計算值的情況，這樣可以避免不必要的計算和存儲。

### 示例代碼

以下是一個使用 `computeIfAbsent` 的示例：

```java

import java.util.HashMap;
import java.util.Map;

public class ComputeIfAbsentExample {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();

        // 使用 computeIfAbsent 方法
        Integer value1 = map.computeIfAbsent("key1", key -> {
            System.out.println("計算值 for " + key);
            return 10; // 返回計算出的值
        });

        System.out.println("key1 的值: " + value1); // 輸出: key1 的值: 10

        // 再次調用 computeIfAbsent，這次不會計算值
        Integer value2 = map.computeIfAbsent("key1", key -> {
            System.out.println("這行不會被執行");
            return 20; // 返回計算出的值
        });

        System.out.println("key1 的值: " + value2); // 輸出: key1 的值: 10
    }
}
```

### 輸出結果

```

計算值 for key1
key1 的值: 10
key1 的值: 10
```

### 總結

- `computeIfAbsent` 方法提供了一種方便的方式來在地圖中懶加載值。
- 它可以幫助減少不必要的計算，並且在鍵不存在時自動計算並插入值。
- 這個方法在處理需要計算或生成值的情況下非常有用，特別是在多執行緒環境中，可以避免競爭條件。


=

當我們需要在一個 `Map` 中存儲計算結果時，使用 `computeIfAbsent` 可以有效地避免不必要的計算。以下是一個具體的例子，展示如何使用 `computeIfAbsent` 來計算和存儲 Fibonacci 數列的值，並避免重複計算。

### Fibonacci 數列示例

在這個例子中，我們將使用 `computeIfAbsent` 來計算 Fibonacci 數列的值。當我們請求某個 Fibonacci 數字時，如果該數字已經計算過並存儲在 `Map` 中，則直接返回該值；如果尚未計算，則計算並存儲它。

### 示例代碼
```java

import java.util.HashMap;
import java.util.Map;

public class FibonacciExample {
    private static Map<Integer, Long> fibonacciCache = new HashMap<>();

    public static void main(String[] args) {
        // 計算並打印 Fibonacci 數列的前 10 個數字
        for (int i = 0; i < 10; i++) {
            System.out.println("Fibonacci(" + i + ") = " + getFibonacci(i));
        }
    }

    public static long getFibonacci(int n) {
        // 使用 computeIfAbsent 來計算 Fibonacci 數字
        return fibonacciCache.computeIfAbsent(n, key -> {
            if (key == 0) return 0L;
            if (key == 1) return 1L;
            // 計算 Fibonacci 數字
            return getFibonacci(key - 1) + getFibonacci(key - 2);
        });
    }
}
```


### 輸出結果
```
Fibonacci(0) = 0
Fibonacci(1) = 1
Fibonacci(2) = 1
Fibonacci(3) = 2
Fibonacci(4) = 3
Fibonacci(5) = 5
Fibonacci(6) = 8
Fibonacci(7) = 13
Fibonacci(8) = 21
Fibonacci(9) = 34
```


### 解釋

1. **計算 Fibonacci 數字**：當我們請求 `Fibonacci(n)` 時，`getFibonacci` 方法會檢查 `fibonacciCache` 中是否已經存在該數字的計算結果。
2. **避免重複計算**：如果該數字已經存在，則直接返回存儲的值；如果不存在，則使用 `computeIfAbsent` 計算該數字並將其存儲在 `Map` 中。
3. **遞歸計算**：在計算 Fibonacci 數字時，使用遞歸方法來計算 `Fibonacci(n-1)` 和 `Fibonacci(n-2)`，這樣可以有效地利用已經計算過的結果，避免重複計算。

### 總結

這個例子展示了如何使用 `computeIfAbsent` 來避免不必要的計算，特別是在計算 Fibonacci 數列時。通過將已計算的結果存儲在 `Map` 中，我們能夠顯著提高性能，特別是在處理較大的數字時。