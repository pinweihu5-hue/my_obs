在 Java 中，Finalizer 和 Cleaner 是两种用于资源管理和清理的机制。它们的主要目的是在对象被垃圾回收之前执行一些清理操作，例如释放系统资源（如文件句柄、网络连接等）。以下是对这两种机制的详细介绍，包括它们的工作原理、使用场景以及示例代码。

### 1. Finalizer

#### 工作原理

- **Finalizer** 是通过重写 `Object` 类的 `finalize()` 方法来实现的。当垃圾回收器确定一个对象不再被引用时，它会调用该对象的 `finalize()` 方法。
- `finalize()` 方法在对象被回收之前执行，可以用于释放资源或执行清理操作。

#### 使用场景

- 适用于需要在对象被垃圾回收之前执行一些清理操作的情况。
```java

public class FinalizerExample {
    @Override
    protected void finalize() throws Throwable {
        try {
            // 清理资源，例如关闭文件或网络连接
            System.out.println("Finalizer called: Cleaning up resources.");
        } finally {
            super.finalize();
        }
    }

    public static void main(String[] args) {
        FinalizerExample example = new FinalizerExample();
        example = null; // 使对象可被回收

        // 强制进行垃圾回收
        System.gc();

        // 等待一段时间以确保 finalize() 被调用
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```


#### 注意事项

- **不可靠性**：`finalize()` 方法的调用时机是不确定的，可能会导致资源未及时释放。
- **性能问题**：使用 `finalize()` 可能会影响性能，因为它会增加垃圾回收的复杂性。
- **不推荐使用**：从 Java 9 开始，`finalize()` 方法被标记为不推荐使用（deprecated），建议使用其他资源管理方式。

### 2. Cleaner

#### 工作原理

- **Cleaner** 是 Java 9 引入的一种更灵活和可靠的资源清理机制。它通过 `java.lang.ref.Cleaner` 类实现。
- `Cleaner` 允许你注册一个清理动作（`Runnable`），当对象被垃圾回收时，清理动作会被执行。

#### 使用场景

- 适用于需要在对象被回收时执行清理操作的情况，尤其是当你希望清理操作的执行时机更可控时。

```java

import java.lang.ref.Cleaner;

public class CleanerExample {
    private static final Cleaner cleaner = new Cleaner();
    private final Cleaner.Cleanable cleanable;

    public CleanerExample() {
        // 注册清理动作
        cleanable = cleaner.register(this, new CleanupAction());
    }

    private static class CleanupAction implements Runnable {
        @Override
        public void run() {
            // 清理资源，例如关闭文件或网络连接
            System.out.println("Cleaner called: Cleaning up resources.");
        }
    }

    public static void main(String[] args) {
        CleanerExample example = new CleanerExample();
        example = null; // 使对象可被回收

        // 强制进行垃圾回收
        System.gc();

        // 等待一段时间以确保清理动作被调用
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```


#### 优点

- **可靠性**：`Cleaner` 提供了更可靠的清理机制，清理动作会在对象被回收时执行。
- **灵活性**：可以注册多个清理动作，并且可以在不同的上下文中使用。

### 何时使用和何时不使用

#### 何时使用

- **使用 `Cleaner`**：
    
    - 当你需要在对象被回收时执行清理操作时，尤其是涉及到外部资源（如文件、网络连接等）。
    - 当你希望清理操作的执行时机更可控时。
- **使用 `finalize()`**：
    
    - 不推荐使用，因为它的可靠性和性能问题。

#### 何时不使用

- **不使用 `finalize()`**：
    
    - 由于其不可靠性和性能问题，Java 9 之后不推荐使用 `finalize()`。
- **不使用 `Cleaner`**：
    
    - 如果你的对象不持有任何需要清理的资源，或者可以通过其他方式（如 `try-with-resources`）管理资源，则不需要使用 `Cleaner`。