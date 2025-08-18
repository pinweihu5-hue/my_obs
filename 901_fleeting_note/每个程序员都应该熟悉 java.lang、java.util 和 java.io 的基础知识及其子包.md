你說得對，`java.lang`、`java.util` 和 `java.io` 是 Java 的核心包，每個程序員都應該熟悉。以下列出這些包以及一些重要的子包和它們包含的關鍵類：

**1. `java.lang` (核心類別)**

這個包提供 Java 語言最基礎的類別，自動導入到每個 Java 程式中。不需要顯式導入。

* **基本資料型態的包裝類別:** `Byte`, `Short`, `Integer`, `Long`, `Float`, `Double`, `Boolean`, `Character`
* **物件類別:** `Object` (所有類別的父類別)
* **字串類別:** `String`, `StringBuffer`, `StringBuilder`
* **系統類別:** `System` (提供系統相關功能，例如標準輸入輸出)
* **執行緒類別:** `Thread`, `Runnable`
* **數學類別:** `Math` (提供數學函數)
* **類別類別:** `Class` (表示類別本身)
* **例外處理類別:** `Throwable`, `Exception`, `RuntimeException`, `Error`  以及許多特定的例外類別
* **程序類別:** `Process`, `ProcessBuilder`
* **基本類型數組:**  雖然不是類別，但 `java.lang` 包含對基本類型數組的支持。


**2. `java.util` (集合框架、日期時間、其他工具)**

這個包包含 Java 的集合框架、日期和時間處理、國際化和其他實用工具類別。

* **集合框架:** `Collection`, `List`, `ArrayList`, `LinkedList`, `Set`, `HashSet`, `TreeSet`, `Map`, `HashMap`, `TreeMap`, `Queue`, `Deque`, `Iterator`, `Comparator`, `Collections` 等。
* **日期和時間:**  `Date` (舊版，不推薦使用), `Calendar` (舊版，不推薦使用),  `java.time` 子包 (Java 8 引入的新日期時間 API，包含 `LocalDate`, `LocalTime`, `LocalDateTime`, `ZonedDateTime`, `Duration`, `Period` 等)
* **其他工具:** `Objects`, `Random`, `Scanner`, `Formatter`, `Timer`, `TimerTask`, `UUID`, `Properties`, `ResourceBundle` 等。


**3. `java.io` (輸入輸出)**

這個包提供用於讀取和寫入數據的類別。

* **輸入流:** `InputStream`, `FileInputStream`, `ByteArrayInputStream`, `DataInputStream`, `ObjectInputStream`, `BufferedInputStream` 等。
* **輸出流:** `OutputStream`, `FileOutputStream`, `ByteArrayOutputStream`, `DataOutputStream`, `ObjectOutputStream`, `BufferedOutputStream`, `PrintStream` 等。
* **讀取器:** `Reader`, `FileReader`, `BufferedReader`, `InputStreamReader` 等。
* **寫入器:** `Writer`, `FileWriter`, `BufferedWriter`, `OutputStreamWriter`, `PrintWriter` 等。
* **檔案和目錄:** `File`, `FileDescriptor`
* **序列化:** `Serializable` 接口


**子包:**

除了以上列出的主要類別外，`java.util` 和 `java.io` 還包含一些重要的子包：

* **`java.util.concurrent`:** 提供并发编程相关的类，例如 `ExecutorService`, `Future`, `ConcurrentHashMap`, `CountDownLatch` 等。
* **`java.util.function`:**  包含 Java 8 引入的函数式接口，例如 `Predicate`, `Consumer`, `Function`, `Supplier` 等。
* **`java.util.stream`:**  支持 Java 8 的 Stream API，用於以聲明式方式處理數據集合。
* **`java.util.regex`:**  提供正則表達式相關的類，例如 `Pattern`, `Matcher`。
* **`java.io.nio`:**  提供非阻塞 I/O 操作，通常用於高性能應用。


這只是一個概述，每個包和子包都包含許多其他類別和接口。  建議你查閱 Java API 文檔以獲取更完整的信息。  理解這些核心包是成為一名熟練 Java 程序員的基礎。
