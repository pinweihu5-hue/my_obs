在 API 設計中，如何避免因為函式式介面造成的多載方法歧義性問題，並引用了 `ExecutorService.submit` 方法作為反例。

**核心問題：相同參數位置的不同函式式介面**

當一個方法有多個多載版本，且這些版本在**相同的參數位置**使用了**不同的函式式介面**時，客戶端程式碼在呼叫這個方法時，可能會遇到編譯器無法判斷該使用哪個多載版本的情況，從而導致歧義。

**反例：`ExecutorService.submit`**

`ExecutorService.submit` 方法有兩個多載版本：

* `Future<T> submit(Callable<T> task)`
* `Future<?> submit(Runnable task)`

這兩個版本都接受一個函式式介面作為參數，但一個是 `Callable<T>`，另一個是 `Runnable`。  當客戶端程式碼提交一個 Lambda 表達式或方法參考時，編譯器可能無法確定該使用哪個版本。

例如：

```java
ExecutorService executor = Executors.newSingleThreadExecutor();

// Lambda 表達式可以同時匹配 Callable<T> 和 Runnable
executor.submit(() -> System.out.println("Hello")); // 這裡會產生歧義
```

在這個例子中，Lambda 表達式 `() -> System.out.println("Hello")` 既可以被視為 `Callable<Void>`，也可以被視為 `Runnable`，因此編譯器不知道該使用哪個 `submit` 方法。  客戶端程式碼需要強制類型轉換來消除歧義：

```java
executor.submit((Runnable) () -> System.out.println("Hello")); // 強制轉換為 Runnable
```

**解決方案：避免在相同參數位置使用不同的函式式介面**

為了避免這種歧義性，最簡單的方法就是在設計 API 時，**不要在相同參數位置使用不同的函式式介面**。  如果需要支援不同的函式式介面，可以考慮使用不同的方法名稱，或者在不同的參數位置使用這些介面。

**與 Item-52 的關聯**

這段話提到的 "這是 Item-52 「明智地使用多載」建議的一個特例" 指的是 Effective Java 中的 Item-52，它建議謹慎使用多載，避免造成混淆和錯誤。  在函式式介面的情況下，避免在相同參數位置使用不同的函式式介面，正是 Item-52 建議的一個具體應用。


總而言之，在設計 API 時，要小心處理函式式介面和多載方法的組合，避免在相同參數位置使用不同的函式式介面，以防止客戶端程式碼出現歧義和編譯錯誤。


