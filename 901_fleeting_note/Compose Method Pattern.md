Compose Method Pattern（組合方法模式）是一種程式碼重構的技巧，旨在簡化複雜的方法，提高程式碼的可讀性和可維護性。它的核心思想是將一個長而複雜的方法分解成多個更小、更專注、更易理解的子方法，然後在原方法中依序調用這些子方法，就像組合積木一樣。

**目標：**

* **降低程式碼複雜度：** 將一個長方法分解成多個短方法，更容易理解和維護。
* **提高程式碼可讀性：**  每個子方法都專注於一個特定的任務，程式碼更清晰易懂。
* **提高程式碼重用性：**  分解出的子方法可能在其他地方也能夠被重用。
* **更容易除錯：**  當程式碼出現問題時，更容易定位到具體的子方法。


**實作步驟：**

1. **識別複雜方法：** 找出程式碼中過長、邏輯複雜、難以理解的方法。
2. **分解成子方法：** 將複雜方法分解成多個更小、更專注的子方法。每個子方法應該只負責一個特定的任務或邏輯步驟。
3. **依序調用子方法：** 在原方法中，按照邏輯順序依序調用這些子方法。
4. **測試：** 確保重構後的程式碼功能與原程式碼一致。


**範例 (Java)：**

**重構前：**

```java
public void processOrder(Order order) {
    // 1. 驗證訂單
    if (!order.isValid()) {
        throw new InvalidOrderException();
    }

    // 2. 計算總價
    double totalPrice = order.calculateTotalPrice();

    // 3. 更新庫存
    order.updateInventory();

    // 4. 發送確認郵件
    sendEmailConfirmation(order);

    // 5. 記錄訂單
    logOrder(order);
}
```

**重構後：**

```java
public void processOrder(Order order) {
    validateOrder(order);
    double totalPrice = calculateTotalPrice(order);
    updateInventory(order);
    sendEmailConfirmation(order);
    logOrder(order);
}

private void validateOrder(Order order) {
    if (!order.isValid()) {
        throw new InvalidOrderException();
    }
}

private double calculateTotalPrice(Order order) {
    return order.calculateTotalPrice();
}

private void updateInventory(Order order) {
    order.updateInventory();
}

private void sendEmailConfirmation(Order order) {
    // ... 發送郵件邏輯 ...
}

private void logOrder(Order order) {
    // ... 記錄訂單邏輯 ...
}
```

**好處：**

重構後的程式碼更清晰易懂，每個子方法的職責明確，更容易維護和除錯。例如，如果需要修改庫存更新的邏輯，只需要修改 `updateInventory()` 方法即可，而不需要修改整個 `processOrder()` 方法。


**與其他重構技巧的關係：**

Compose Method Pattern 通常會與其他重構技巧一起使用，例如 Extract Method（提煉方法）、Rename Method（重新命名方法）等，以達到更好的重構效果。


總而言之，Compose Method Pattern 是一種簡單而有效的程式碼重構技巧，可以幫助開發者提高程式碼的品質，降低維護成本。


