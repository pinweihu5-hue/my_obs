好的，讓我來詳細介紹 Java 中的 `DateTimeFormatter` 類別。

**`DateTimeFormatter` 是什麼？**

`DateTimeFormatter` 是 Java 8 中引入的一個用於格式化和解析日期時間的類別。它位於 `java.time.format` 套件中，是 Java 新的日期時間 API (`java.time`) 的一部分。

`DateTimeFormatter` 提供了強大且靈活的方式來：

*   **格式化日期時間：** 將 `LocalDate`、`LocalTime`、`LocalDateTime`、`ZonedDateTime` 等日期時間物件轉換為特定格式的字串。
*   **解析日期時間：** 將特定格式的字串轉換為 `LocalDate`、`LocalTime`、`LocalDateTime`、`ZonedDateTime` 等日期時間物件。

**`DateTimeFormatter` 的特性：**

1.  **執行緒安全：** `DateTimeFormatter` 是執行緒安全的，可以在多執行緒環境下安全地使用。
2.  **不可變性：** `DateTimeFormatter` 是不可變的，一旦建立就不能修改。這表示你可以安全地將 `DateTimeFormatter` 物件傳遞給多個執行緒使用。
3.  **多種格式化模式：** `DateTimeFormatter` 支援多種格式化模式，可以滿足各種不同的日期時間格式化需求。
4.  **本地化支援：** `DateTimeFormatter` 支援本地化，可以根據不同的地區設定來格式化和解析日期時間。
5.  **自定義格式：** `DateTimeFormatter` 允許你使用模式字元來自定義日期時間的格式。

**`DateTimeFormatter` 的常用方法：**

1.  **`ofPattern(String pattern)`：**
    *   這個方法用於建立一個使用指定模式的 `DateTimeFormatter` 物件。
    *   `pattern` 參數是一個字串，用於指定日期時間的格式。
    *   例如：`DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")`。
2.  **`format(TemporalAccessor temporal)`：**
    *   這個方法用於將一個 `TemporalAccessor` 物件 (例如 `LocalDate`、`LocalDateTime`、`ZonedDateTime`) 格式化為字串。
    *   例如：`formatter.format(LocalDateTime.now())`。
3.  **`parse(CharSequence text, TemporalQuery<T> query)`：**
    *   這個方法用於將一個字串解析為一個 `TemporalAccessor` 物件。
    *   `text` 參數是要解析的字串。
    *   `query` 參數是一個 `TemporalQuery` 物件，用於指定要解析的日期時間類型。
    *   例如：`LocalDateTime.parse("2023-10-27 10:30:00", formatter)`。
4.  **`withLocale(Locale locale)`：**
    *   這個方法用於建立一個使用指定地區設定的 `DateTimeFormatter` 物件。
    *   例如：`formatter.withLocale(Locale.CHINA)`。
5.  **`ofLocalizedDate(FormatStyle dateStyle)`、`ofLocalizedTime(FormatStyle timeStyle)`、`ofLocalizedDateTime(FormatStyle dateTimeStyle)`：**
    *   這些方法用於建立一個使用預定義本地化格式的 `DateTimeFormatter` 物件。
    *   `FormatStyle` 是一個列舉，用於指定日期時間的格式風格 (例如 `SHORT`、`MEDIUM`、`LONG`、`FULL`)。
    *   例如：`DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM)`。

**`DateTimeFormatter` 的模式字元：**

`DateTimeFormatter` 使用模式字元來指定日期時間的格式。常用的模式字元包括：

*   **`y`：** 年份 (例如 `yyyy` 表示 2023)。
*   **`M`：** 月份 (例如 `MM` 表示 10，`MMM` 表示 Oct，`MMMM` 表示 October)。
*   **`d`：** 日期 (例如 `dd` 表示 27)。
*   **`H`：** 小時 (24 小時制，例如 `HH` 表示 10)。
*   **`h`：** 小時 (12 小時制，例如 `hh` 表示 10)。
*   **`m`：** 分鐘 (例如 `mm` 表示 30)。
*   **`s`：** 秒 (例如 `ss` 表示 00)。
*   **`S`：** 毫秒 (例如 `SSS` 表示 000)。
*   **`a`：** 上午/下午標記 (例如 `a` 表示 AM 或 PM)。
*   **`z`：** 時區 (例如 `z` 表示 PST)。
*   **`Z`：** 時區偏移量 (例如 `Z` 表示 -0800)。

**程式碼範例：**

```java
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.time.format.FormatStyle;
import java.util.Locale;

public class DateTimeFormatterExample {
    public static void main(String[] args) {
        // 使用 ofPattern() 建立 DateTimeFormatter
        DateTimeFormatter formatter1 = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        LocalDateTime now = LocalDateTime.now();
        String formattedDateTime = now.format(formatter1);
        System.out.println("Formatted DateTime: " + formattedDateTime); // Output: 2023-10-27 10:30:00 (實際時間會不同)

        // 使用 parse() 解析字串
        String dateTimeString = "2023-10-27 10:30:00";
        LocalDateTime parsedDateTime = LocalDateTime.parse(dateTimeString, formatter1);
        System.out.println("Parsed DateTime: " + parsedDateTime); // Output: 2023-10-27T10:30

        // 使用 withLocale() 建立本地化 DateTimeFormatter
        DateTimeFormatter formatter2 = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH时mm分ss秒").withLocale(Locale.CHINA);
        String formattedDateTime2 = now.format(formatter2);
        System.out.println("Formatted DateTime (China): " + formattedDateTime2); // Output: 2023年10月27日 10时30分00秒 (實際時間會不同)

        // 使用 ofLocalizedDate() 建立本地化 DateTimeFormatter
        DateTimeFormatter formatter3 = DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM).withLocale(Locale.CHINA);
        LocalDate today = LocalDate.now();
        String formattedDate = today.format(formatter3);
        System.out.println("Formatted Date (China): " + formattedDate); // Output: 2023年10月27日 (實際日期會不同)
    }
}
```

**總結：**

*   `DateTimeFormatter` 是 Java 8 中用於格式化和解析日期時間的類別。
*   `DateTimeFormatter` 是執行緒安全且不可變的。
*   `DateTimeFormatter` 提供了多種格式化模式，並支援本地化。
*   你可以使用 `ofPattern()` 方法建立自定義格式的 `DateTimeFormatter` 物件。
*   你可以使用 `format()` 方法將日期時間物件格式化為字串。
*   你可以使用 `parse()` 方法將字串解析為日期時間物件。

希望這個詳細的介紹對你有幫助！如果你還有其他關於 Java 日期時間 API 的問題，歡迎繼續提問。
