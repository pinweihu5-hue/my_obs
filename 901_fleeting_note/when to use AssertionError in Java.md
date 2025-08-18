`AssertionError` 在 Java 中用於表示斷言失敗。斷言是一種在開發過程中用於驗證程式碼假設的機制。當斷言失敗時，會拋出 `AssertionError`，指示程式存在錯誤。

那麼，什麼時候應該使用 `AssertionError` 呢？

**1. 內部不變量:**

`AssertionError` 主要用於檢查程式碼中的內部不變量。內部不變量是指在程式執行過程中必須始終保持為真的條件。例如，在一個方法的開頭，你可能需要確保某些參數的值在有效範圍內。

```java
private void processData(int value) {
    assert value > 0 : "value must be positive"; // 如果 value <= 0，則拋出 AssertionError

    // ... 方法的其餘部分 ...
}
```

**2. 控制流程不應該到達的地方:**

在某些情況下，程式碼的某些部分不應該被執行到。例如，在 `switch` 語句中，`default` 分支通常用於處理未預期的情況。如果程式執行到 `default` 分支，則可能表示存在錯誤。

```java
switch (value) {
    case 1:
        // ... 處理 case 1 ...
        break;
    case 2:
        // ... 處理 case 2 ...
        break;
    default:
        assert false : "Unexpected value: " + value; // 不應該執行到這裡
}
```

**3. 私有輔助方法的先決條件:**

對於私有輔助方法，可以使用斷言來檢查先決條件。由於私有方法只能在類內部被調用，因此可以假設調用者會遵守這些先決條件。

```java
private void helperMethod(Object obj) {
    assert obj != null : "obj cannot be null"; // 確保 obj 不為 null

    // ... 方法的其餘部分 ...
}
```

**注意事項:**

* **不要用斷言來驗證公共方法的參數:** 公共方法的參數應該通過正常的錯誤處理機制（例如拋出 `IllegalArgumentException`）來驗證，因為你無法控制調用者。
* **斷言預設是關閉的:** 需要使用 `-ea` 或 `-enableassertions` JVM 參數來啟用斷言。
* **不要在斷言中執行副作用操作:** 斷言中的程式碼不應該修改程式的狀態，因為斷言可能會被關閉。


總而言之，`AssertionError` 應該用於在開發過程中檢查程式碼的內部一致性和正確性，而不是用於處理運行時錯誤或驗證用戶輸入。  它是一種幫助開發者快速發現和定位錯誤的工具。


