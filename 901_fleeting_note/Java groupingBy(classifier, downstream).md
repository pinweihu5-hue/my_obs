
解釋
```java

Map<String, Long> freq;

try (Stream<String> words = new Scanner(file).tokens()) {
    freq = words.collect(groupingBy(String::toLowerCase, counting()));
}

```



這段程式碼的功能是統計一個檔案中每個單詞出現的頻率，並將結果儲存到一個 `Map<String, Long>` 中。 
這裡的單詞是指以空格分隔的字串。  程式碼使用了 Java 8 的 Stream API 和 Collectors 工具來實現這個功能。

讓我們逐步解釋：

1. **`Map<String, Long> freq;`**: 宣告一個 `Map` 變數 `freq`，用於儲存單詞及其出現頻率。 鍵 (Key) 是單詞的小寫形式 (String)，值 (Value) 是出現次數 (Long)。

2. **`try (Stream<String> words = new Scanner(file).tokens()) { ... }`**:  這是一個 try-with-resources 語句，它確保 `Scanner` 物件在使用完畢後會自動關閉，釋放資源。 `Scanner(file)` 建立一個 `Scanner` 物件，用於讀取指定的檔案 (`file`)。 `tokens()` 方法會返回一個 `Stream<String>`，其中包含檔案中以空格分隔的所有單詞。

3. **`freq = words.collect(groupingBy(String::toLowerCase, counting()));`**: 這是程式碼的核心部分，它使用 `collect` 方法和 `groupingBy` 和 `counting` 這兩個 Collectors 來統計單詞頻率。

    * **`collect(Collector<? super T, A, R> collector)`**:  `collect` 方法是一個終端操作，它會使用指定的 Collector 將 Stream 中的元素收集到一個結果中。

    * **`groupingBy(Function<? super T,? extends K> classifier)`**:  `groupingBy` Collector 會根據指定的分類函數 (`classifier`) 將 Stream 中的元素分組。 在這個例子中，分類函數是 `String::toLowerCase`，它會將每個單詞轉換為小寫形式。

    * **`counting()`**:  `counting` Collector 會計算每個組中元素的數量。

    * **`groupingBy(classifier, downstream)`**:  這個版本的 `groupingBy` 接受一個下游 Collector (`downstream`)，用於對每個組中的元素進行進一步的處理。 在這個例子中，下游 Collector 是 `counting()`，它會計算每個組 (也就是每個單詞的小寫形式) 中的元素數量。


**程式碼的執行流程：**
1. 程式碼會讀取檔案中的所有單詞，並將它們轉換為一個 Stream。
2. `collect` 方法會使用 `groupingBy` 和 `counting` 將 Stream 中的單詞分組並計算數量。
3. `groupingBy` 會將每個單詞轉換為小寫形式，並作為 Map 的鍵。
4. `counting` 會計算每個小寫單詞出現的次數，並作為 Map 的值。
5. 最後，`collect` 方法會返回一個 `Map<String, Long>`，其中包含每個單詞的小寫形式及其出現次數。


**範例：**

假設 `file` 中的內容是：

```
The quick brown fox jumps over the lazy dog.
```

那麼 `freq` 的內容將會是：

```
{the=2, quick=1, brown=1, fox=1, jumps=1, over=1, lazy=1, dog=1}
```


這段程式碼簡潔高效地實現了單詞頻率統計的功能，展現了 Java Stream API 和 Collectors 的强大功能。


