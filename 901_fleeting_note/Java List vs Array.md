

1. Array（數組）
數組 是一種固定大小的數據結構，用於存儲相同類型的元素。數組的大小在創建時確定，並且一旦創建後就無法更改。

特點：
- 固定大小：數組的大小在創建時確定，無法動態調整。
- 快速訪問：可以通過索引快速訪問元素，時間複雜度為 O(1)。
- 類型安全：數組中的所有元素必須是相同類型。
- 內存連續性：數組在內存中是連續存儲的，這使得訪問速度較快。

```java

public class ArrayExample {
    public static void main(String[] args) {
        // 創建一個整數數組
        int[] numbers = new int[5]; // 大小為 5 的整數數組

        // 初始化數組
        for (int i = 0; i < numbers.length; i++) {
            numbers[i] = i * 2; // 將數組元素設置為 0, 2, 4, 6, 8
        }

        // 訪問數組元素
        for (int number : numbers) {
            System.out.println(number);
        }
    }
}

```


List（列表）
List 是 Java Collections Framework 中的一個 interface，表示一個有序的元素集合。
與數組不同，List 的大小是可變的，可以根據需要動態調整。

特點：
- 可變大小：List 可以根據需要動態增減元素。
- 有序性：List 中的元素是有序的，可以通過索引訪問。
- 多樣性：**List 接口有多個實現類，如 ArrayList、LinkedList 等，根據不同的需求選擇合適的實現**。
- 支持重複元素：List 可以包含重複的元素。

```java

import java.util.ArrayList;
import java.util.List;

public class ListExample {
    public static void main(String[] args) {
        // 創建一個 ArrayList
        List<String> fruits = new ArrayList<>();

        // 添加元素
        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Cherry");

        // 訪問元素
        for (String fruit : fruits) {
            System.out.println(fruit);
        }

        // 獲取特定索引的元素
        String firstFruit = fruits.get(0);
        System.out.println("First fruit: " + firstFruit);

        // 刪除元素
        fruits.remove("Banana");
        System.out.println("After removing Banana: " + fruits);
    }
}

```

