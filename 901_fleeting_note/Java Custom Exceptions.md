[(808) Java Custom Exceptions Tutorial - It's Way Easier Than You Think - YouTube](https://www.youtube.com/watch?v=OIozDnGYqIU)




例子
用 extends
extends 的目標越具體越好

下面給出三種 ctor的例子
- msg
- throwable -> 就是你還可以吃 exception
- msg + throwable 兩個放一起
![[IMG-Java Custom Exceptions-20250805201154464.png]]


使用
![[IMG-Java Custom Exceptions-20250805201155252.png]]



---

## Java Custom Exceptions 完整介紹

Java Custom Exceptions（自定義異常）是指使用者自行建立的異常類別，用於表示應用程式中特定的錯誤或異常情況。Java 提供了豐富的內建異常類別，但有時這些內建異常不足以表達應用程式特有的錯誤情況，這時就需要建立自定義異常。

**建立 Custom Exception:**

自定義異常類別必須繼承自 `Exception` 類別（對於 checked exception）或 `RuntimeException` 類別（對於 unchecked exception）。

**Checked Exception vs. Unchecked Exception:**

* **Checked Exception (已檢查異常):**  編譯器會強制要求處理 checked exception，例如捕獲或拋出。 繼承自 `Exception` (但不是 `RuntimeException` 的子類)。
* **Unchecked Exception (未檢查異常):** 編譯器不會強制要求處理 unchecked exception。 繼承自 `RuntimeException`。
PS: compiler 要你處理，一種就是加上 函數後面加上 throwsXXX, 一種就是 try catch



會噴掉的種類
一種是 error -> 系統的， app level 通常無法 handle, like OOM 之類的
excpetion 就是 app level 可以handle
又分為 compiler 可以幫你檢查的, 和 compiler 無法幫你解查的(runtime), like null ptr

PS: Java 這邊還比 TS 弱 XD, TS type寫好可以幫你解查 undefined 類的 err, 部分 idxOutofBound error

![[IMG-Java Custom Exceptions-20250805201156120.png]]
ref
[Checked vs. Unchecked Exceptions in Java Tutorial - What's The Difference? - YouTube](https://www.youtube.com/watch?v=bCPClyGsVhc)






**範例：**

```java
// Checked Exception
class InsufficientFundsException extends Exception {
    public InsufficientFundsException(String message) {
        super(message);
    }
}

// Unchecked Exception
class InvalidInputException extends RuntimeException {
    public InvalidInputException(String message) {
        super(message);
    }
}

public class CustomExceptionExample {

    public static void withdraw(double amount) throws InsufficientFundsException {
        if (amount < 0) {
            throw new IllegalArgumentException("提款金額不能為負數"); // 使用內建的 IllegalArgumentException
        }
        double balance = 1000; // 假設餘額為 1000
        if (balance < amount) {
            throw new InsufficientFundsException("餘額不足"); // 拋出自定義的 InsufficientFundsException
        }
        balance -= amount;
        System.out.println("提款成功，剩餘餘額：" + balance);
    }


    public static void processInput(String input) {
        if (input == null || input.isEmpty()) {
            throw new InvalidInputException("輸入不能為空"); // 拋出自定義的 InvalidInputException
        }
        // ... 處理輸入 ...
    }


    public static void main(String[] args) {
        try {
            withdraw(1500);
        } catch (InsufficientFundsException e) {
            System.err.println("錯誤：" + e.getMessage()); // 處理 InsufficientFundsException
        }

        try {
            processInput(null);
        } catch (InvalidInputException e) {
            System.err.println("錯誤：" + e.getMessage()); // 處理 InvalidInputException
        }

    }
}
```

**何時使用 Custom Exception：**

* **表示特定業務邏輯錯誤：** 當內建異常無法準確描述應用程式特有的錯誤情況時，例如「餘額不足」、「使用者名稱已存在」等。
* **提高程式碼可讀性：** 使用自定義異常可以使程式碼更易於理解和維護，因為異常名稱可以清晰地表達錯誤的含義。
* **模組化錯誤處理：** 可以針對不同的自定義異常定義不同的錯誤處理邏輯。
* **建立更具體的異常層次結構：** 可以建立繼承自自定義異常的更具體的異常類別，以表示更細粒度的錯誤情況。


**何時不應該使用 Custom Exception：**

* **可以使用內建異常的情況：**  如果內建異常已經能夠準確描述錯誤情況，則無需建立自定義異常。例如，使用 `IllegalArgumentException` 表示無效的參數，使用 `IOException` 表示 I/O 錯誤。
* **過度使用：**  避免為每個可能的錯誤情況都建立自定義異常，這會使程式碼變得臃腫且難以維護。


**總結：**

Java Custom Exceptions 提供了一種機制，讓開發者可以定義應用程式特有的異常情況，提高程式碼的可讀性、可維護性和模組化程度。  合理地使用自定義異常可以使錯誤處理更精確、更有效。  但是，也應該避免過度使用，在可以使用內建異常的情況下，優先使用內建異常。
