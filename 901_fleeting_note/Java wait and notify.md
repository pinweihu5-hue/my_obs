`wait()` 和 `notify()` 是 Java 中用於線程間通訊的重要方法，它們允許線程協同工作，以達成特定的目標。  
這些方法都屬於 `Object` 類，因此所有 Java 對象都擁有它們。

**核心概念：**
* **等待 (Waiting):**  當一個線程調用 `wait()` 方法時，它會釋放當前對象的鎖，並進入等待狀態，直到另一個線程調用同一個對象的 `notify()` 或 `notifyAll()` 方法。
* **通知 (Notification):**  當一個線程調用 `notify()` 方法時，它會喚醒在同一個對象上等待的一個線程。  `notifyAll()` 方法則會喚醒所有在同一個對象上等待的線程。
* **鎖 (Lock):**  `wait()` 和 `notify()` 方法必須在同步程式碼塊或方法中使用，也就是說，調用這些方法的線程必須持有當前對象的鎖。


**`wait()` 方法：**

`wait()` 方法有多個版本：

* `wait()`:  無限期地等待，直到被通知。
* `wait(long timeout)`:  最多等待 `timeout` 毫秒，如果在超时之前沒有被通知，則會自動返回。
* `wait(long timeout, int nanos)`:  最多等待 `timeout` 毫秒加上 `nanos` 納秒，如果在超时之前沒有被通知，則會自動返回。

當一個線程調用 `wait()` 方法時：

1. 它會釋放當前對象的鎖。
2. 它會進入等待狀態，並被添加到與該對象關聯的等待隊列中。
3. 當另一個線程調用同一個對象的 `notify()` 或 `notifyAll()` 方法，或者等待時間超时時，該線程會被喚醒。
4. 喚醒後，該線程需要重新獲取對象的鎖，才能繼續執行。


**`notify()` 和 `notifyAll()` 方法：**

* `notify()`:  喚醒在同一個對象上等待的一個線程。  JVM 會選擇哪個線程被喚醒，通常是等待時間最長的線程。
* `notifyAll()`:  喚醒所有在同一個對象上等待的線程。


**使用示例：生產者-消費者模型**

```java
public class ProducerConsumer {

    private final Object lock = new Object();
    private boolean dataAvailable = false;
    private int data;

    public void produce(int value) {
        synchronized (lock) {
            
            while (dataAvailable) { // 使用 while 避免 spurious wakeup
                try {
                    // 釋放當前對象的鎖，並進入等待狀態，直到另一個線程調用同一個對象的 `notify()` 或 `notifyAll()` 方法
                    lock.wait(); 
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt(); // 重新設置中斷標誌
                    return; // 或其他處理方式
                }
            }
            
            data = value;
            dataAvailable = true;
            lock.notifyAll(); // 通知所有等待的消費者
        }
    }

    public int consume() {
    
        synchronized (lock) {
            
            while (!dataAvailable) { // 使用 while 避免 spurious wakeup
                try {
                    lock.wait();
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt(); // 重新設置中斷標誌
                    return -1; // 或其他處理方式
                }
            }
            
            dataAvailable = false;
            lock.notifyAll(); // 通知所有等待的生產者
            return data;
            
        }
    }
}
```

**注意事項：**

* **Spurious Wakeup:**  線程有可能在沒有被 `notify()` 或 `notifyAll()` 喚醒的情況下被喚醒，這種情況稱為 spurious wakeup。  為了避免 spurious wakeup 導致的錯誤，應該在 `wait()` 方法外面使用 `while` 循環來檢查等待條件。
* **中斷處理：**  `wait()` 方法可能會拋出 `InterruptedException`，需要進行適當的異常處理。  通常的做法是重新設置中斷標誌 (`Thread.currentThread().interrupt();`)，並根據需要退出循環或採取其他處理措施。


**`java.util.concurrent` 包中的替代方案：**

Java 的 `java.util.concurrent` 包提供了更高級別的并发工具，例如 `BlockingQueue`, `CountDownLatch`, `Semaphore` 等，可以更方便地實現線程間通訊，並且避免了 `wait()` 和 `notify()` 的一些複雜性。  在新的程式碼中，建議優先使用這些更高級別的工具。


儘管 `wait()` 和 `notify()` 的使用比較複雜，但理解它們的工作原理對於理解 Java 的并发機制至關重要。  它們是構建更複雜并发工具的基礎。


