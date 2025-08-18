## [#](https://xiaolincoding.com/interview/juc.html#%E5%A4%9A%E7%BA%BF%E7%A8%8B)多线程

### [#](https://xiaolincoding.com/interview/juc.html#java%E9%87%8C%E9%9D%A2%E7%9A%84%E7%BA%BF%E7%A8%8B%E5%92%8C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E7%9A%84%E7%BA%BF%E7%A8%8B%E4%B8%80%E6%A0%B7%E5%90%97)java 里面的线程和操作系统的线程一样吗？

Java 底层会调用 pthread_create 来创建线程，所以本质上 java 程序创建的线程，就是和操作系统线程是一样的，是 1 对 1 的线程模型。

![[IMG-小林  Java并发编程面试题-20250222193718205.webp]]

### [#](https://xiaolincoding.com/interview/juc.html#%E4%BD%BF%E7%94%A8%E5%A4%9A%E7%BA%BF%E7%A8%8B%E8%A6%81%E6%B3%A8%E6%84%8F%E5%93%AA%E4%BA%9B%E9%97%AE%E9%A2%98)使用多线程要注意哪些问题？

要保证多线程的允许是安全，不要出现数据竞争造成的数据混乱的问题。

Java 的线程安全在三个方面体现：

- **原子性**：提供互斥访问，同一时刻只能有一个线程对数据进行操作，在 Java 中使用了 atomic 和 synchronized 这两个关键字来确保原子性；
- **可见性**：一个线程对主内存的修改可以及时地被其他线程看到，在 Java 中使用了 synchronized 和 volatile 这两个关键字确保可见性；
- **有序性**：一个线程观察其他线程中的指令执行顺序，由于指令重排序，该观察结果一般杂乱无序，在 Java 中使用了 happens-before 原则来确保有序性。

### [#](https://xiaolincoding.com/interview/juc.html#%E4%BF%9D%E8%AF%81%E6%95%B0%E6%8D%AE%E7%9A%84%E4%B8%80%E8%87%B4%E6%80%A7%E6%9C%89%E5%93%AA%E4%BA%9B%E6%96%B9%E6%A1%88%E5%91%A2)保证数据的一致性有哪些方案呢？

- **事务管理**：使用数据库事务来确保一组数据库操作要么全部成功提交，要么全部失败回滚。通过 ACID（原子性、一致性、隔离性、持久性）属性，数据库事务可以保证数据的一致性。
- **锁机制**：使用锁来实现对共享资源的互斥访问。在 Java 中，可以使用 synchronized 关键字、ReentrantLock 或其他锁机制来控制并发访问，从而避免并发操作导致数据不一致。
- **版本控制**：通过乐观锁的方式，在更新数据时记录数据的版本信息，从而避免同时对同一数据进行修改，进而保证数据的一致性。

### [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E7%9A%84%E5%88%9B%E5%BB%BA%E6%96%B9%E5%BC%8F%E6%9C%89%E5%93%AA%E4%BA%9B)线程的创建方式有哪些？

> 1. 继承 Thread 类

这是最直接的一种方式，用户自定义类继承 java.lang.Thread 类，重写其 run () 方法，run () 方法中定义了线程执行的具体任务。创建该类的实例后，通过调用 start () 方法启动线程。

```
class MyThread extends Thread {
    @Override
    public void run() {
        // 线程执行的代码
    }
}

public static void main(String[] args) {
    MyThread t = new MyThread();
    t.start();
}
```

采用继承 Thread 类方式

- 优点：编写简单，如果需要访问当前线程，无需使用 Thread.currentThread () 方法，直接使用 this，即可获得当前线程
- 缺点：因为线程类已经继承了 Thread 类，所以不能再继承其他的父类

> 2. 实现 Runnable 接口

如果一个类已经继承了其他类，就不能再继承 Thread 类，此时可以实现 java.lang.Runnable 接口。实现 Runnable 接口需要重写 run () 方法，然后将此 Runnable 对象作为参数传递给 Thread 类的构造器，创建 Thread 对象后调用其 start () 方法启动线程。

```
class MyRunnable implements Runnable {
    @Override
    public void run() {
        // 线程执行的代码
    }
}

public static void main(String[] args) {
    Thread t = new Thread(new MyRunnable());
    t.start();
}
```

采用实现 Runnable 接口方式：

- 优点：线程类只是实现了 Runable 接口，还可以继承其他的类。在这种方式下，可以多个线程共享同一个目标对象，所以非常适合多个相同线程来处理同一份资源的情况，从而可以将 CPU 代码和数据分开，形成清晰的模型，较好地体现了面向对象的思想。
- 缺点：编程稍微复杂，如果需要访问当前线程，必须使用 Thread.currentThread () 方法。

> 3. 实现 Callable 接口与 FutureTask

java.util.concurrent.Callable 接口类似于 Runnable，但 Callable 的 call () 方法可以有返回值并且可以抛出异常。要执行 Callable 任务，需将它包装进一个 FutureTask，因为 Thread 类的构造器只接受 Runnable 参数，而 FutureTask 实现了 Runnable 接口。

```
class MyCallable implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        // 线程执行的代码，这里返回一个整型结果
        return 1;
    }
}

public static void main(String[] args) {
    MyCallable task = new MyCallable();
    FutureTask<Integer> futureTask = new FutureTask<>(task);
    Thread t = new Thread(futureTask);
    t.start();

    try {
        Integer result = futureTask.get();  // 获取线程执行结果
        System.out.println("Result: " + result);
    } catch (InterruptedException | ExecutionException e) {
        e.printStackTrace();
    }
}
```

采用实现 Callable 接口方式：

- 缺点：编程稍微复杂，如果需要访问当前线程，必须调用 Thread.currentThread () 方法。
- 优点：线程只是实现 Runnable 或实现 Callable 接口，还可以继承其他类。这种方式下，多个线程可以共享一个 target 对象，非常适合多线程处理同一份资源的情形。

> 4. 使用线程池（Executor 框架）

从 Java 5 开始引入的 java.util.concurrent.ExecutorService 和相关类提供了线程池的支持，这是一种更高效的线程管理方式，避免了频繁创建和销毁线程的开销。可以通过 Executors 类的静态方法创建不同类型的线程池。

```
class Task implements Runnable {
    @Override
    public void run() {
        // 线程执行的代码
    }
}

public static void main(String[] args) {
    ExecutorService executor = Executors.newFixedThreadPool(10);  // 创建固定大小的线程池
    for (int i = 0; i < 100; i++) {
        executor.submit(new Task());  // 提交任务到线程池执行
    }
    executor.shutdown();  // 关闭线程池
}
```

采用线程池方式：

- 缺点：程池增加了程序的复杂度，特别是当涉及线程池参数调整和故障排查时。错误的配置可能导致死锁、资源耗尽等问题，这些问题的诊断和修复可能较为复杂。
- 优点：线程池可以重用预先创建的线程，避免了线程创建和销毁的开销，显著提高了程序的性能。对于需要快速响应的并发请求，线程池可以迅速提供线程来处理任务，减少等待时间。并且，线程池能够有效控制运行的线程数量，防止因创建过多线程导致的系统资源耗尽（如内存溢出）。通过合理配置线程池大小，可以最大化 CPU 利用率和系统吞吐量。

### [#](https://xiaolincoding.com/interview/juc.html#%E6%80%8E%E4%B9%88%E5%90%AF%E5%8A%A8%E7%BA%BF%E7%A8%8B)怎么启动线程 ？

启动线程的通过 Thread 类的 **start()**。

```
//创建两个线程，用start启动线程
MyThread myThread1 = new MyThread();  
MyThread myThread2 = new MyThread();  
myThread1.start();  
myThread2.start();  
```

### [#](https://xiaolincoding.com/interview/juc.html#%E5%A6%82%E4%BD%95%E5%81%9C%E6%AD%A2%E4%B8%80%E4%B8%AA%E7%BA%BF%E7%A8%8B%E7%9A%84%E8%BF%90%E8%A1%8C)如何停止一个线程的运行？

主要有这些方法：

- **异常法停止**：线程调用 interrupt () 方法后，在线程的 run 方法中判断当前对象的 interrupted () 状态，如果是中断状态则抛出异常，达到中断线程的效果。
- **在沉睡中停止**：先将线程 sleep，然后调用 interrupt 标记中断状态，interrupt 会将阻塞状态的线程中断。会抛出中断异常，达到停止线程的效果
- **stop () 暴力停止**：线程调用 stop () 方法会被暴力停止，方法已弃用，该方法会有不好的后果：强制让线程停止有可能使一些请理性的工作得不到完成。
- **使用 return 停止线程**：调用 interrupt 标记为中断状态后，在 run 方法中判断当前线程状态，如果为中断状态则 return，能达到停止线程的效果。

### [#](https://xiaolincoding.com/interview/juc.html#%E8%B0%83%E7%94%A8-interrupt-%E6%98%AF%E5%A6%82%E4%BD%95%E8%AE%A9%E7%BA%BF%E7%A8%8B%E6%8A%9B%E5%87%BA%E5%BC%82%E5%B8%B8%E7%9A%84)调用 interrupt 是如何让线程抛出异常的？

每个线程都一个与之关联的布尔属性来表示其中断状态，中断状态的初始值为 false，当一个线程被其它线程调用 `Thread.interrupt()` 方法中断时，会根据实际情况做出响应。

- 如果该线程正在执行低级别的可中断方法（如 `Thread.sleep()`、`Thread.join()` 或 `Object.wait()`），则会解除阻塞并**抛出 `InterruptedException` 异常**。
- 否则 `Thread.interrupt()` 仅设置线程的中断状态，在该被中断的线程中稍后可通过轮询中断状态来决定是否要停止当前正在执行的任务。

### [#](https://xiaolincoding.com/interview/juc.html#java%E7%BA%BF%E7%A8%8B%E7%9A%84%E7%8A%B6%E6%80%81%E6%9C%89%E5%93%AA%E4%BA%9B)Java 线程的状态有哪些？

![[IMG-小林  Java并发编程面试题-20250222193846572.webp]]

源自《Java 并发编程艺术》 java.lang.Thread.State 枚举类中定义了六种线程的状态，可以调用线程 Thread 中的 getState () 方法**获取当前线程的状态**。

|线程状态|解释|
|---|---|
|NEW|尚未启动的线程状态，即线程创建，**还未调用 start 方法**|
|RUNNABLE|**就绪状态**（调用 start，等待调度）+ **正在运行**|
|BLOCKED|**等待监视器锁**时，陷入阻塞状态|
|WAITING|等待状态的线程正在**等待**另一线程执行特定的操作（如 notify）|
|TIMED_WAITING|具有**指定等待时间**的等待状态|
|TERMINATED|线程完成执行，**终止状态**|

### [#](https://xiaolincoding.com/interview/juc.html#blocked%E5%92%8Cwaiting%E6%9C%89%E5%95%A5%E5%8C%BA%E5%88%AB)blocked 和 waiting 有啥区别

- **触发条件**: 线程进入 BLOCKED 状态通常是因为试图获取一个对象的锁（monitor lock），但该锁已经被另一个线程持有。这通常发生在尝试进入 synchronized 块或方法时，如果锁已被占用，则线程将被阻塞直到锁可用。线程进入 WAITING 状态是因为它正在等待另一个线程执行某些操作，例如调用 Object.wait () 方法、Thread.join () 方法或 LockSupport.park () 方法。在这种状态下，线程将不会消耗 CPU 资源，并且不会参与锁的竞争。
- **唤醒机制**: 当一个线程被阻塞等待锁时，一旦锁被释放，线程将有机会重新尝试获取锁。如果锁此时未被其他线程获取，那么线程可以从 BLOCKED 状态变为 RUNNABLE 状态。线程在 WAITING 状态中需要被显式唤醒。例如，如果线程调用了 Object.wait ()，那么它必须等待另一个线程调用同一对象上的 Object.notify () 或 Object.notifyAll () 方法才能被唤醒。

### [#](https://xiaolincoding.com/interview/juc.html#wait-%E7%8A%B6%E6%80%81%E4%B8%8B%E7%9A%84%E7%BA%BF%E7%A8%8B%E5%A6%82%E4%BD%95%E8%BF%9B%E8%A1%8C%E6%81%A2%E5%A4%8D%E5%88%B0-running-%E7%8A%B6%E6%80%81)wait 状态下的线程如何进行恢复到 running 状态？

- 等待的线程**被其他线程对象唤醒**，`notify()` 和 `notifyAll()`。
- 如果线程没有获取到锁则会直接进入 Waiting 状态，其实这种本质上它就是执行了 LockSupport.park () 方法进入了 Waiting 状态，那么解锁的时候会执行 `LockSupport.unpark(Thread)`，与上面 park 方法对应，给出许可证，**解除等待状态**。

### [#](https://xiaolincoding.com/interview/juc.html#notify-%E5%92%8C-notifyall-%E7%9A%84%E5%8C%BA%E5%88%AB)notify 和 notifyAll 的区别？

同样是唤醒等待的线程，同样最多只有一个线程能获得锁，同样不能控制哪个线程获得锁。

区别在于：

- notify：唤醒一个线程，其他线程依然处于 wait 的等待唤醒状态，如果被唤醒的线程结束时没调用 notify，其他线程就永远没人去唤醒，只能等待超时，或者被中断
- notifyAll：所有线程退出 wait 的状态，开始竞争锁，但只有一个线程能抢到，这个线程执行完后，其他线程又会有一个幸运儿脱颖而出得到锁

### [#](https://xiaolincoding.com/interview/juc.html#notify-%E9%80%89%E6%8B%A9%E5%93%AA%E4%B8%AA%E7%BA%BF%E7%A8%8B)notify 选择哪个线程？

notify 在源码的注释中说到 notify 选择唤醒的线程是任意的，但是依赖于具体实现的 jvm。

![[100_attachements/645f626d5f184ac6f630c079d1191723_MD5.webp]]

JVM 有很多实现，比较流行的就是 hotspot，hotspot 对 notofy () 的实现并不是我们以为的随机唤醒，，而是 “先进先出” 的顺序唤醒。

## [#](https://xiaolincoding.com/interview/juc.html#%E5%B9%B6%E5%8F%91%E5%AE%89%E5%85%A8)并发安全

### [#](https://xiaolincoding.com/interview/juc.html#%E6%80%8E%E4%B9%88%E4%BF%9D%E8%AF%81%E5%A4%9A%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8)怎么保证多线程安全？

- **synchronized 关键字**: 可以使用 `synchronized` 关键字来同步代码块或方法，确保同一时刻只有一个线程可以访问这些代码。对象锁是通过 `synchronized` 关键字锁定对象的监视器（monitor）来实现的。

```
public synchronized void someMethod() { /* ... */ }

public void anotherMethod() {
    synchronized (someObject) {
        /* ... */
    }
}
```

- **volatile 关键字**:`volatile` 关键字用于变量，确保所有线程看到的是该变量的最新值，而不是可能存储在本地寄存器中的副本。

```
public volatile int sharedVariable;
```

- **Lock 接口和 ReentrantLock 类**:`java.util.concurrent.locks.Lock` 接口提供了比 `synchronized` 更强大的锁定机制，`ReentrantLock` 是一个实现该接口的例子，提供了更灵活的锁管理和更高的性能。

```
private final ReentrantLock lock = new ReentrantLock();

public void someMethod() {
    lock.lock();
    try {
        /* ... */
    } finally {
        lock.unlock();
    }
}
```

- **原子类**：Java 并发库（`java.util.concurrent.atomic`）提供了原子类，如 `AtomicInteger`、`AtomicLong` 等，这些类提供了原子操作，可以用于更新基本类型的变量而无需额外的同步。

示例：

```
AtomicInteger counter = new AtomicInteger(0);

int newValue = counter.incrementAndGet();
```

- **线程局部变量**:`ThreadLocal` 类可以为每个线程提供独立的变量副本，这样每个线程都拥有自己的变量，消除了竞争条件。

```
ThreadLocal<Integer> threadLocalVar = new ThreadLocal<>();

threadLocalVar.set(10);
int value = threadLocalVar.get();
```

- **并发集合**: 使用 `java.util.concurrent` 包中的线程安全集合，如 `ConcurrentHashMap`、`ConcurrentLinkedQueue` 等，这些集合内部已经实现了线程安全的逻辑。
- **JUC 工具类**: 使用 `java.util.concurrent` 包中的一些工具类可以用于控制线程间的同步和协作。例如：`Semaphore` 和 `CyclicBarrier` 等。

### [#](https://xiaolincoding.com/interview/juc.html#java%E4%B8%AD%E6%9C%89%E5%93%AA%E4%BA%9B%E5%B8%B8%E7%94%A8%E7%9A%84%E9%94%81-%E5%9C%A8%E4%BB%80%E4%B9%88%E5%9C%BA%E6%99%AF%E4%B8%8B%E4%BD%BF%E7%94%A8)Java 中有哪些常用的锁，在什么场景下使用？

ava 中的锁是用于管理多线程并发访问共享资源的关键机制。锁可以确保在任意给定时间内只有一个线程可以访问特定的资源，从而避免数据竞争和不一致性。Java 提供了多种锁机制，可以分为以下几类：

- **内置锁（synchronized）**：Java 中的 `synchronized` 关键字是内置锁机制的基础，可以用于方法或代码块。当一个线程进入 `synchronized` 代码块或方法时，它会获取关联对象的锁；当线程离开该代码块或方法时，锁会被释放。如果其他线程尝试获取同一个对象的锁，它们将被阻塞，直到锁被释放。其中，syncronized 加锁时有无锁、偏向锁、轻量级锁和重量级锁几个级别。偏向锁用于当一个线程进入同步块时，如果没有任何其他线程竞争，就会使用偏向锁，以减少锁的开销。轻量级锁使用线程栈上的数据结构，避免了操作系统级别的锁。重量级锁则涉及操作系统级的互斥锁。
- **ReentrantLock**：`java.util.concurrent.locks.ReentrantLock` 是一个显式的锁类，提供了比 `synchronized` 更高级的功能，如可中断的锁等待、定时锁等待、公平锁选项等。`ReentrantLock` 使用 `lock()` 和 `unlock()` 方法来获取和释放锁。其中，公平锁按照线程请求锁的顺序来分配锁，保证了锁分配的公平性，但可能增加锁的等待时间。非公平锁不保证锁分配的顺序，可以减少锁的竞争，提高性能，但可能造成某些线程的饥饿。
- **读写锁（ReadWriteLock）**：`java.util.concurrent.locks.ReadWriteLock` 接口定义了一种锁，允许多个读取者同时访问共享资源，但只允许一个写入者。读写锁通常用于读取远多于写入的情况，以提高并发性。
- **乐观锁和悲观锁**：悲观锁（Pessimistic Locking）通常指在访问数据前就锁定资源，假设最坏的情况，即数据很可能被其他线程修改。`synchronized` 和 `ReentrantLock` 都是悲观锁的例子。乐观锁（Optimistic Locking）通常不锁定资源，而是在更新数据时检查数据是否已被其他线程修改。乐观锁常使用版本号或时间戳来实现。
- **自旋锁**：自旋锁是一种锁机制，线程在等待锁时会持续循环检查锁是否可用，而不是放弃 CPU 并阻塞。通常可以使用 CAS 来实现。这在锁等待时间很短的情况下可以提高性能，但过度自旋会浪费 CPU 资源。

### [#](https://xiaolincoding.com/interview/juc.html#%E6%80%8E%E4%B9%88%E5%9C%A8%E5%AE%9E%E8%B7%B5%E4%B8%AD%E7%94%A8%E9%94%81%E7%9A%84)怎么在实践中用锁的？

Java 提供了多种锁的实现，包括 `synchronized` 关键字、`java.util.concurrent.locks` 包下的 `Lock` 接口及其具体实现如 `ReentrantLock`、`ReadWriteLock` 等。下面我们来看看这些锁的使用方式。

> 1. `synchronized`

`synchronized` 关键字可以用于方法或代码块，它是 Java 中最早的锁实现，使用起来非常简单。

**示例：synchronized 方法**

```
public class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
    }
}
```

**示例：synchronized 代码块**

```
public class Counter {
    private Object lock = new Object();
    private int count = 0;

    public void increment() {
        synchronized (lock) {
            count++;
        }
    }
}
```

> 2. 使用 `Lock` 接口

`Lock` 接口提供了比 `synchronized` 更灵活的锁操作，包括尝试锁、可中断锁、定时锁等。`ReentrantLock` 是 `Lock` 接口的一个实现。

**示例：使用 ReentrantLock**

```
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Counter {
    private Lock lock = new ReentrantLock();
    private int count = 0;

    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
}
```

> 3. 使用 `ReadWriteLock`

`ReadWriteLock` 接口提供了一种读写锁的实现，允许多个读操作同时进行，但写操作是独占的。

**示例：使用 ReadWriteLock**

```
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class Cache {
    private ReadWriteLock lock = new ReentrantReadWriteLock();
    private Lock readLock = lock.readLock();
    private Lock writeLock = lock.writeLock();
    private Object data;

    public Object readData() {
        readLock.lock();
        try {
            return data;
        } finally {
            readLock.unlock();
        }
    }

    public void writeData(Object newData) {
        writeLock.lock();
        try {
            data = newData;
        } finally {
            writeLock.unlock();
        }
    }
}
```

### [#](https://xiaolincoding.com/interview/juc.html#synchronized%E5%92%8Creentrantlock%E5%8F%8A%E5%85%B6%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF)synchronized 和 reentrantlock 及其应用场景？

> synchronized 工作原理

synchronized 是 Java 提供的原子性内置锁，这种内置的并且使用者看不到的锁也被称为**监视器锁**，

使用 synchronized 之后，会在编译之后在同步的代码块前后加上 monitorenter 和 monitorexit 字节码指令，他依赖操作系统底层互斥锁实现。他的作用主要就是实现原子性操作和解决共享变量的内存可见性问题。

执行 monitorenter 指令时会尝试获取对象锁，如果对象没有被锁定或者已经获得了锁，锁的计数器 + 1。此时其他竞争锁的线程则会进入等待队列中。执行 monitorexit 指令时则会把计数器 - 1，当计数器值为 0 时，则锁释放，处于等待队列中的线程再继续竞争锁。

synchronized 是排它锁，当一个线程获得锁之后，其他线程必须等待该线程释放锁后才能获得锁，而且由于 Java 中的线程和操作系统原生线程是一一对应的，线程被阻塞或者唤醒时时会从用户态切换到内核态，这种转换非常消耗性能。

从内存语义来说，加锁的过程会清除工作内存中的共享变量，再从主内存读取，而释放锁的过程则是将工作内存中的共享变量写回主内存。

实际上大部分时候我认为说到 monitorenter 就行了，但是为了更清楚的描述，还是再具体一点。

如果再深入到源码来说，synchronized 实际上有两个队列 waitSet 和 entryList。

1. 当多个线程进入同步代码块时，首先进入 entryList
2. 有一个线程获取到 monitor 锁后，就赋值给当前线程，并且计数器 + 1
3. 如果线程调用 wait 方法，将释放锁，当前线程置为 null，计数器 - 1，同时进入 waitSet 等待被唤醒，调用 notify 或者 notifyAll 之后又会进入 entryList 竞争锁
4. 如果线程执行完毕，同样释放锁，计数器 - 1，当前线程置为 null![[IMG-小林  Java并发编程面试题-20250807203911801.png]]

> reentrantlock 工作原理

ReentrantLock 的底层实现主要依赖于 AbstractQueuedSynchronizer（AQS）这个抽象类。AQS 是一个提供了基本同步机制的框架，其中包括了队列、状态值等。

ReentrantLock 在 AQS 的基础上通过内部类 Sync 来实现具体的锁操作。不同的 Sync 子类实现了公平锁和非公平锁的不同逻辑：

- **可中断性**： ReentrantLock 实现了可中断性，这意味着线程在等待锁的过程中，可以被其他线程中断而提前结束等待。在底层，ReentrantLock 使用了与 LockSupport.park () 和 LockSupport.unpark () 相关的机制来实现可中断性。
- **设置超时时间**： ReentrantLock 支持在尝试获取锁时设置超时时间，即等待一定时间后如果还未获得锁，则放弃锁的获取。这是通过内部的 tryAcquireNanos 方法来实现的。
- **公平锁和非公平锁**： 在直接创建 ReentrantLock 对象时，默认情况下是非公平锁。公平锁是按照线程等待的顺序来获取锁，而非公平锁则允许多个线程在同一时刻竞争锁，不考虑它们申请锁的顺序。公平锁可以通过在创建 ReentrantLock 时传入 true 来设置，例如：

```
ReentrantLock fairLock = new ReentrantLock(true);
```

- **多个条件变量**： ReentrantLock 支持多个条件变量，每个条件变量可以与一个 ReentrantLock 关联。这使得线程可以更灵活地进行等待和唤醒操作，而不仅仅是基于对象监视器的 wait () 和 notify ()。多个条件变量的实现依赖于 Condition 接口，例如：

```
ReentrantLock lock = new ReentrantLock();
Condition condition = lock.newCondition();
// 使用下面方法进行等待和唤醒
condition.await();
condition.signal();
```

- **可重入性**： ReentrantLock 支持可重入性，即同一个线程可以多次获得同一把锁，而不会造成死锁。这是通过内部的 holdCount 计数来实现的。当一个线程多次获取锁时，holdCount 递增，释放锁时递减，只有当 holdCount 为零时，其他线程才有机会获取锁。

> 应用场景的区别

**synchronized**：

- **简单同步需求**： 当你需要对代码块或方法进行简单的同步控制时，`synchronized` 是一个很好的选择。它使用起来简单，不需要额外的资源管理，因为锁会在方法退出或代码块执行完毕后自动释放。
- **代码块同步**： 如果你想对特定代码段进行同步，而不是整个方法，可以使用 `synchronized` 代码块。这可以让你更精细地控制同步的范围，从而减少锁的持有时间，提高并发性能。
- **内置锁的使用**： `synchronized` 关键字使用对象的内置锁（也称为监视器锁），这在需要使用对象作为锁对象的情况下很有用，尤其是在对象状态与锁保护的代码紧密相关时。

**ReentrantLock：**

- **高级锁功能需求**： `ReentrantLock` 提供了 `synchronized` 所不具备的高级功能，如公平锁、响应中断、定时锁尝试、以及多个条件变量。当你需要这些功能时，`ReentrantLock` 是更好的选择。
- **性能优化**： 在高度竞争的环境中，`ReentrantLock` 可以提供比 `synchronized` 更好的性能，因为它提供了更细粒度的控制，如尝试锁定和定时锁定，可以减少线程阻塞的可能性。
- **复杂同步结构**： 当你需要更复杂的同步结构，如需要多个条件变量来协调线程之间的通信时，`ReentrantLock` 及其配套的 `Condition` 对象可以提供更灵活的解决方案。

综上，`synchronized` 适用于简单同步需求和不需要额外锁功能的场景，而 `ReentrantLock` 适用于需要更高级锁功能、性能优化或复杂同步逻辑的情况。选择哪种同步机制取决于具体的应用需求和性能考虑。

### [#](https://xiaolincoding.com/interview/juc.html#synchronized%E5%92%8Creentrantlock%E5%8C%BA%E5%88%AB)synchronized 和 reentrantlock 区别？

synchronized 和 ReentrantLock 都是 Java 中提供的可重入锁：

- **用法不同**：synchronized 可用来修饰普通方法、静态方法和代码块，而 ReentrantLock 只能用在代码块上。
- **获取锁和释放锁方式不同**：synchronized 会自动加锁和释放锁，当进入 synchronized 修饰的代码块之后会自动加锁，当离开 synchronized 的代码段之后会自动释放锁。而 ReentrantLock 需要手动加锁和释放锁
- **锁类型不同**：synchronized 属于非公平锁，而 ReentrantLock 既可以是公平锁也可以是非公平锁。
- **响应中断不同**：ReentrantLock 可以响应中断，解决死锁的问题，而 synchronized 不能响应中断。
- **底层实现不同**：synchronized 是 JVM 层面通过监视器实现的，而 ReentrantLock 是基于 AQS 实现的。

### [#](https://xiaolincoding.com/interview/juc.html#%E6%80%8E%E4%B9%88%E7%90%86%E8%A7%A3%E5%8F%AF%E9%87%8D%E5%85%A5%E9%94%81)怎么理解可重入锁？

可重入锁是指同一个线程在获取了锁之后，可以再次重复获取该锁而不会造成死锁或其他问题。当一个线程持有锁时，如果再次尝试获取该锁，就会成功获取而不会被阻塞。

ReentrantLock 实现可重入锁的机制是基于线程持有锁的计数器。

- 当一个线程第一次获取锁时，计数器会加 1，表示该线程持有了锁。在此之后，如果同一个线程再次获取锁，计数器会再次加 1。每次线程成功获取锁时，都会将计数器加 1。
- 当线程释放锁时，计数器会相应地减 1。只有当计数器减到 0 时，锁才会完全释放，其他线程才有机会获取锁。

这种计数器的设计使得同一个线程可以多次获取同一个锁，而不会造成死锁或其他问题。每次获取锁时，计数器加 1；每次释放锁时，计数器减 1。只有当计数器减到 0 时，锁才会完全释放。

ReentrantLock 通过这种计数器的方式，实现了可重入锁的机制。它允许同一个线程多次获取同一个锁，并且能够正确地处理锁的获取和释放，避免了死锁和其他并发问题。

### [#](https://xiaolincoding.com/interview/juc.html#synchronized-%E6%94%AF%E6%8C%81%E9%87%8D%E5%85%A5%E5%90%97-%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0%E7%9A%84)synchronized 支持重入吗？如何实现的？

synchronized 是基于原子性的内部锁机制，是可重入的，因此在一个线程调用 synchronized 方法的同时在其方法体内部调用该对象另一个 synchronized 方法，也就是说一个线程得到一个对象锁后再次请求该对象锁，是允许的，这就是 synchronized 的可重入性。

synchronized 底层是利用计算机系统 mutex Lock 实现的。每一个可重入锁都会关联一个线程 ID 和一个锁状态 status。

当一个线程请求方法时，会去检查锁状态。

1. 如果锁状态是 0，代表该锁没有被占用，使用 CAS 操作获取锁，将线程 ID 替换成自己的线程 ID。
2. 如果锁状态不是 0，代表有线程在访问该方法。此时，如果线程 ID 是自己的线程 ID，如果是可重入锁，会将 status 自增 1，然后获取到该锁，进而执行相应的方法；如果是非重入锁，就会进入阻塞队列等待。

在释放锁时，

1. 如果是可重入锁的，每一次退出方法，就会将 status 减 1，直至 status 的值为 0，最后释放该锁。
2. 如果非可重入锁的，线程退出方法，直接就会释放该锁。

### [#](https://xiaolincoding.com/interview/juc.html#syncronized%E9%94%81%E5%8D%87%E7%BA%A7%E7%9A%84%E8%BF%87%E7%A8%8B%E8%AE%B2%E4%B8%80%E4%B8%8B)syncronized 锁升级的过程讲一下

具体的锁升级的过程是：**无锁 -> 偏向锁 -> 轻量级锁 -> 重量级锁**。

- **无锁**：这是没有开启偏向锁的时候的状态，在 JDK1.6 之后偏向锁的默认开启的，但是有一个偏向延迟，需要在 JVM 启动之后的多少秒之后才能开启，这个可以通过 JVM 参数进行设置，同时是否开启偏向锁也可以通过 JVM 参数设置。
- **偏向锁**：这个是在偏向锁开启之后的锁的状态，如果还没有一个线程拿到这个锁的话，这个状态叫做匿名偏向，当一个线程拿到偏向锁的时候，下次想要竞争锁只需要拿线程 ID 跟 MarkWord 当中存储的线程 ID 进行比较，如果线程 ID 相同则直接获取锁（相当于锁偏向于这个线程），不需要进行 CAS 操作和将线程挂起的操作。
- **轻量级锁**：在这个状态下线程主要是通过 CAS 操作实现的。将对象的 MarkWord 存储到线程的虚拟机栈上，然后通过 CAS 将对象的 MarkWord 的内容设置为指向 Displaced Mark Word 的指针，如果设置成功则获取锁。在线程出临界区的时候，也需要使用 CAS，如果使用 CAS 替换成功则同步成功，如果失败表示有其他线程在获取锁，那么就需要在释放锁之后将被挂起的线程唤醒。
- **重量级锁**：当有两个以上的线程获取锁的时候轻量级锁就会升级为重量级锁，因为 CAS 如果没有成功的话始终都在自旋，进行 while 循环操作，这是非常消耗 CPU 的，但是在升级为重量级锁之后，线程会被操作系统调度然后挂起，这可以节约 CPU 资源。

了解完 4 种锁状态之后，我们就可以整体的来看一下锁升级的过程了。 ![[IMG-小林  Java并发编程面试题-20250807203913592.webp]] 线程 A 进入 synchronized 开始抢锁，JVM 会判断当前是否是偏向锁的状态，如果是就会根据 Mark Word 中存储的线程 ID 来判断，当前线程 A 是否就是持有偏向锁的线程。如果是，则忽略 check，线程 A 直接执行临界区内的代码。

但如果 Mark Word 里的线程不是线程 A，就会通过自旋尝试获取锁，如果获取到了，就将 Mark Word 中的线程 ID 改为自己的；如果竞争失败，就会立马撤销偏向锁，膨胀为轻量级锁。

后续的竞争线程都会通过自旋来尝试获取锁，如果自旋成功那么锁的状态仍然是轻量级锁。然而如果竞争失败，锁会膨胀为重量级锁，后续等待的竞争的线程都会被阻塞。

### [#](https://xiaolincoding.com/interview/juc.html#jvm%E5%AF%B9synchornized%E7%9A%84%E4%BC%98%E5%8C%96)JVM 对 Synchornized 的优化？

synchronized 核心优化方案主要包含以下 4 个：

- **锁膨胀**：synchronized 从无锁升级到偏向锁，再到轻量级锁，最后到重量级锁的过程，它叫做锁膨胀也叫做锁升级。JDK 1.6 之前，synchronized 是重量级锁，也就是说 synchronized 在释放和获取锁时都会从用户态转换成内核态，而转换的效率是比较低的。但有了锁膨胀机制之后，synchronized 的状态就多了无锁、偏向锁以及轻量级锁了，这时候在进行并发操作时，大部分的场景都不需要用户态到内核态的转换了，这样就大幅的提升了 synchronized 的性能。
- **锁消除**：指的是在某些情况下，JVM 虚拟机如果检测不到某段代码被共享和竞争的可能性，就会将这段代码所属的同步锁消除掉，从而到底提高程序性能的目的。
- **锁粗化**：将多个连续的加锁、解锁操作连接在一起，扩展成一个范围更大的锁。
- **自适应自旋锁**：指通过自身循环，尝试获取锁的一种方式，优点在于它避免一些线程的挂起和恢复操作，因为挂起线程和恢复线程都需要从用户态转入内核态，这个过程是比较慢的，所以通过自旋的方式可以一定程度上避免线程挂起和恢复所造成的性能开销。

### [#](https://xiaolincoding.com/interview/juc.html#%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8Baqs)介绍一下 AQS

AQS 全称为 AbstractQueuedSynchronizer，是 Java 中的一个抽象类。 AQS 是一个用于构建锁、同步器、协作工具类的工具类（框架）。

AQS 核心思想是，如果被请求的共享资源空闲，那么就将当前请求资源的线程设置为有效的工作线程，将共享资源设置为锁定状态；如果共享资源被占用，就需要一定的阻塞等待唤醒机制来保证锁分配。这个机制主要用的是 CLH 队列的变体实现的，将暂时获取不到锁的线程加入到队列中。

CLH：Craig、Landin and Hagersten 队列，是单向链表，AQS 中的队列是 CLH 变体的虚拟双向队列（FIFO），AQS 是通过将每条请求共享资源的线程封装成一个节点来实现锁的分配。

主要原理图如下： ![[IMG-小林  Java并发编程面试题-20250807203915503.png]]

AQS 使用一个 Volatile 的 int 类型的成员变量来表示同步状态，通过内置的 FIFO 队列来完成资源获取的排队工作，通过 CAS 完成对 State 值的修改。

AQS 广泛用于控制并发流程的类，如下图：

![[IMG-小林  Java并发编程面试题-20250807203917822.webp]]

其中 `Sync` 是这些类中都有的内部类，其结构如下：

![[IMG-小林  Java并发编程面试题-20250807203817366.png]]

可以看到：`Sync` 是 `AQS` 的实现。 `AQS` 主要完成的任务：

- 同步状态（比如说计数器）的原子性管理；
- 线程的阻塞和解除阻塞；
- 队列的管理。

> AQS 原理

AQS 最核心的就是三大部分：

- 状态：state；
- 控制线程抢锁和配合的 FIFO 队列（双向链表）；
- 期望协作工具类去实现的获取 / 释放等重要方法（重写）。

**状态 state**

- 这里 state 的具体含义，会根据具体实现类的不同而不同：比如在 Semapore 里，他表示剩余许可证的数量；在 CountDownLatch 里，它表示还需要倒数的数量；在 ReentrantLock 中，state 用来表示 “锁” 的占有情况，包括可重入计数，当 state 的值为 0 的时候，标识该 Lock 不被任何线程所占有。
- state 是 volatile 修饰的，并被并发修改，所以修改 state 的方法都需要保证线程安全，比如 getState、setState 以及 compareAndSetState 操作来读取和更新这个状态。这些方法都依赖于 unsafe 类。

**FIFO 队列**

- 这个队列用来存放 “等待的线程，AQS 就是 “排队管理器”，当多个线程争用同一把锁时，必须有排队机制将那些没能拿到锁的线程串在一起。当锁释放时，锁管理器就会挑选一个合适的线程来占有这个刚刚释放的锁。
- AQS 会维护一个等待的线程队列，把线程都放到这个队列里，这个队列是双向链表形式。

**实现获取 / 释放等方法**

- 这里的获取和释放方法，是利用 AQS 的协作工具类里最重要的方法，是由协作类自己去实现的，并且含义各不相同；
- 获取方法：获取操作会以来 state 变量，经常会阻塞（比如获取不到锁的时候）。在 Semaphore 中，获取就是 acquire 方法，作用是获取一个许可证； 而在 CountDownLatch 里面，获取就是 await 方法，作用是等待，直到倒数结束；
- 释放方法：在 Semaphore 中，释放就是 release 方法，作用是释放一个许可证； 在 CountDownLatch 里面，获取就是 countDown 方法，作用是将倒数的数减一；
- 需要每个实现类重写 tryAcquire 和 tryRelease 等方法。

### [#](https://xiaolincoding.com/interview/juc.html#threadlocal%E4%BD%9C%E7%94%A8-%E5%8E%9F%E7%90%86-%E5%85%B7%E4%BD%93%E9%87%8C%E9%9D%A2%E5%AD%98%E7%9A%84key-value%E6%98%AF%E5%95%A5-%E4%BC%9A%E6%9C%89%E4%BB%80%E4%B9%88%E9%97%AE%E9%A2%98-%E5%A6%82%E4%BD%95%E8%A7%A3%E5%86%B3)Threadlocal 作用，原理，具体里面存的 key value 是啥，会有什么问题，如何解决？

`ThreadLocal` 是 Java 中用于解决线程安全问题的一种机制，它允许创建线程局部变量，即每个线程都有自己独立的变量副本，从而避免了线程间的资源共享和同步问题。

![[IMG-小林  Java并发编程面试题-20250807203922914.png]]

从内存结构图，我们可以看到：

- Thread 类中，有个 ThreadLocal.ThreadLocalMap 的成员变量。
- ThreadLocalMap 内部维护了 Entry 数组，每个 Entry 代表一个完整的对象，key **是** ThreadLocal **本身**，value 是 ThreadLocal 的泛型对象值。

> ThreadLocal 的作用

- **线程隔离**：`ThreadLocal` 为每个线程提供了独立的变量副本，这意味着线程之间不会相互影响，可以安全地在多线程环境中使用这些变量而不必担心数据竞争或同步问题。
- **降低耦合度**：在同一个线程内的多个函数或组件之间，使用 `ThreadLocal` 可以减少参数的传递，降低代码之间的耦合度，使代码更加清晰和模块化。
- **性能优势**：由于 `ThreadLocal` 避免了线程间的同步开销，所以在大量线程并发执行时，相比传统的锁机制，它可以提供更好的性能。

> ThreadLocal 的原理

`ThreadLocal` 的实现依赖于 `Thread` 类中的一个 `ThreadLocalMap` 字段，这是一个存储 `ThreadLocal` 变量本身和对应值的映射。每个线程都有自己的 `ThreadLocalMap` 实例，用于存储该线程所持有的所有 `ThreadLocal` 变量的值。

当你创建一个 `ThreadLocal` 变量时，它实际上就是一个 `ThreadLocal` 对象的实例。每个 `ThreadLocal` 对象都可以存储任意类型的值，这个值对每个线程来说是独立的。

- 当调用 `ThreadLocal` 的 `get()` 方法时，`ThreadLocal` 会检查当前线程的 `ThreadLocalMap` 中是否有与之关联的值。
    
- 如果有，返回该值；
    
- 如果没有，会调用 `initialValue()` 方法（如果重写了的话）来初始化该值，然后将其放入 `ThreadLocalMap` 中并返回。
    
- 当调用 `set()` 方法时，`ThreadLocal` 会将给定的值与当前线程关联起来，即在当前线程的 `ThreadLocalMap` 中存储一个键值对，键是 `ThreadLocal` 对象自身，值是传入的值。
    
- 当调用 `remove()` 方法时，会从当前线程的 `ThreadLocalMap` 中移除与该 `ThreadLocal` 对象关联的条目。
    

> 可能存在的问题

当一个线程结束时，其 `ThreadLocalMap` 也会随之销毁，但是 `ThreadLocal` 对象本身不会立即被垃圾回收，直到没有其他引用指向它为止。

因此，在使用 `ThreadLocal` 时需要注意，**如果不显式调用**`remove()`**方法，或者线程结束时未正确清理**`ThreadLocal`**变量，可能会导致内存泄漏，因为**`ThreadLocalMap`**会持续持有**`ThreadLocal`**变量的引用，即使这些变量不再被其他地方引用。**

因此，实际应用中需要在使用完 `ThreadLocal` 变量后调用 `remove()` 方法释放资源。

### [#](https://xiaolincoding.com/interview/juc.html#%E6%82%B2%E8%A7%82%E9%94%81%E5%92%8C%E4%B9%90%E8%A7%82%E9%94%81%E7%9A%84%E5%8C%BA%E5%88%AB)悲观锁和乐观锁的区别？

- 乐观锁： 就像它的名字一样，对于并发间操作产生的线程安全问题持乐观状态，乐观锁认为竞争不总 是会发生，因此它不需要持有锁，将比较 - 替换这两个动作作为一个原子操作尝试去修改内存中的变量，如果失败则表示发生冲突，那么就应该有相应的重试逻辑。
- 悲观锁： 还是像它的名字一样，对于并发间操作产生的线程安全问题持悲观状态，悲观锁认为竞争总 是会发生，因此每次对某资源进行操作时，都会持有一个独占的锁，就像 synchronized，不管三七二十一，直接上了锁就操作资源了。

### [#](https://xiaolincoding.com/interview/juc.html#java%E4%B8%AD%E6%83%B3%E5%AE%9E%E7%8E%B0%E4%B8%80%E4%B8%AA%E4%B9%90%E8%A7%82%E9%94%81-%E9%83%BD%E6%9C%89%E5%93%AA%E4%BA%9B%E6%96%B9%E5%BC%8F)Java 中想实现一个乐观锁，都有哪些方式？

1. **CAS（Compare and Swap）操作：** CAS 是乐观锁的基础。Java 提供了 java.util.concurrent.atomic 包，包含各种原子变量类（如 AtomicInteger、AtomicLong），这些类使用 CAS 操作实现了线程安全的原子操作，可以用来实现乐观锁。
2. **版本号控制**：增加一个版本号字段记录数据更新时候的版本，每次更新时递增版本号。在更新数据时，同时比较版本号，若当前版本号和更新前获取的版本号一致，则更新成功，否则失败。
3. **时间戳**：使用时间戳记录数据的更新时间，在更新数据时，在比较时间戳。如果当前时间戳大于数据的时间戳，则说明数据已经被其他线程更新，更新失败。

### [#](https://xiaolincoding.com/interview/juc.html#cas-%E6%9C%89%E4%BB%80%E4%B9%88%E7%BC%BA%E7%82%B9)CAS 有什么缺点？

CAS 的缺点主要有 3 点：

- **ABA 问题**：ABA 的问题指的是在 CAS 更新的过程中，当读取到的值是 A，然后准备赋值的时候仍然是 A，但是实际上有可能 A 的值被改成了 B，然后又被改回了 A，这个 CAS 更新的漏洞就叫做 ABA。只是 ABA 的问题大部分场景下都不影响并发的最终效果。Java 中有 AtomicStampedReference 来解决这个问题，他加入了预期标志和更新后标志两个字段，更新时不光检查值，还要检查当前的标志是否等于预期标志，全部相等的话才会更新。
- **循环时间长开销大**：自旋 CAS 的方式如果长时间不成功，会给 CPU 带来很大的开销。
- **只能保证一个共享变量的原子操作**：只对一个共享变量操作可以保证原子性，但是多个则不行，多个可以通过 AtomicReference 来处理或者使用锁 synchronized 实现。

### [#](https://xiaolincoding.com/interview/juc.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E4%B8%8D%E8%83%BD%E6%89%80%E6%9C%89%E7%9A%84%E9%94%81%E9%83%BD%E7%94%A8cas)为什么不能所有的锁都用 CAS？

CAS 操作是基于循环重试的机制，如果 CAS 操作一直未能成功，线程会一直自旋重试，占用 CPU 资源。在高并发情况下，大量线程自旋会导致 CPU 资源浪费。

### [#](https://xiaolincoding.com/interview/juc.html#voliatle%E5%85%B3%E9%94%AE%E5%AD%97%E6%9C%89%E4%BB%80%E4%B9%88%E4%BD%9C%E7%94%A8)voliatle 关键字有什么作用？

volatite 作用有 2 个：

- **保证变量对所有线程的可见性**。当一个变量被声明为 volatile 时，它会保证对这个变量的写操作会立即刷新到主存中，而对这个变量的读操作会直接从主存中读取，从而确保了多线程环境下对该变量访问的可见性。这意味着一个线程修改了 volatile 变量的值，其他线程能够立刻看到这个修改，不会受到各自线程工作内存的影响。
    
- **禁止指令重排序优化**。volatile 关键字在 Java 中主要通过内存屏障来禁止特定类型的指令重排序。
    
    - 1）**写 - 写（Write-Write）屏障**：在对 volatile 变量执行写操作之前，会插入一个写屏障。这确保了在该变量写操作之前的所有普通写操作都已完成，防止了这些写操作被移到 volatile 写操作之后。
        
    - 2）**读 - 写（Read-Write）屏障**：在对 volatile 变量执行读操作之后，会插入一个读屏障。它确保了对 volatile 变量的读操作之后的所有普通读操作都不会被提前到 volatile 读之前执行，保证了读取到的数据是最新的。
        
    - 3）**写 - 读（Write-Read）屏障**：这是最重要的一个屏障，它发生在 volatile 写之后和 volatile 读之前。这个屏障确保了 volatile 写操作之前的所有内存操作（包括写操作）都不会被重排序到 volatile 读之后，同时也确保了 volatile 读操作之后的所有内存操作（包括读操作）都不会被重排序到 volatile 写之前。
        

### [#](https://xiaolincoding.com/interview/juc.html#%E6%8C%87%E4%BB%A4%E9%87%8D%E6%8E%92%E5%BA%8F%E7%9A%84%E5%8E%9F%E7%90%86%E6%98%AF%E4%BB%80%E4%B9%88)指令重排序的原理是什么？

在执行程序时，为了提高性能，处理器和编译器常常会对指令进行重排序，但是重排序要满足下面 2 个条件才能进行：

- 在单线程环境下不能改变程序运行的结果
- 存在数据依赖关系的不允许重排序。

所以重排序不会对单线程有影响，只会破坏多线程的执行语义。

我们看这个例子，A 和 C 之间存在数据依赖关系，同时 B 和 C 之间也存在数据依赖关系。因此在最终执行的指令序列中，C 不能被重排序到 A 和 B 的前面，如果 C 排到 A 和 B 的前面，那么程序的结果将会被改变。但 A 和 B 之间没有数据依赖关系，编译器和处理器可以重排序 A 和 B 之间的执行顺序。

![[IMG-小林  Java并发编程面试题-20250807203925547.webp]]

### [#](https://xiaolincoding.com/interview/juc.html#volatile%E5%8F%AF%E4%BB%A5%E4%BF%9D%E8%AF%81%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8%E5%90%97)volatile 可以保证线程安全吗？

volatile 关键字可以保证可见性，但不能保证原子性，因此不能完全保证线程安全。volatile 关键字用于修饰变量，当一个线程修改了 volatile 修饰的变量的值，其他线程能够立即看到最新的值，从而避免了线程之间的数据不一致。

但是，volatile 并不能解决多线程并发下的复合操作问题，比如 i++ 这种操作不是原子操作，如果多个线程同时对 i 进行自增操作，volatile 不能保证线程安全。对于复合操作，需要使用 synchronized 关键字或者 Lock 来保证原子性和线程安全。

### [#](https://xiaolincoding.com/interview/juc.html#volatile%E5%92%8Csychronized%E6%AF%94%E8%BE%83)volatile 和 sychronized 比较？

Synchronized 解决了多线程访问共享资源时可能出现的竞态条件和数据不一致的问题，保证了线程安全性。Volatile 解决了变量在多线程环境下的可见性和有序性问题，确保了变量的修改对其他线程是可见的。

- Synchronized: Synchronized 是一种排他性的同步机制，保证了多个线程访问共享资源时的互斥性，即同一时刻只允许一个线程访问共享资源。通过对代码块或方法添加 Synchronized 关键字来实现同步。
- Volatile: Volatile 是一种轻量级的同步机制，用来保证变量的可见性和禁止指令重排序。当一个变量被声明为 Volatile 时，线程在读取该变量时会直接从内存中读取，而不会使用缓存，同时对该变量的写操作会立即刷回主内存，而不是缓存在本地内存中。

### [#](https://xiaolincoding.com/interview/juc.html#%E4%BB%80%E4%B9%88%E6%98%AF%E5%85%AC%E5%B9%B3%E9%94%81%E5%92%8C%E9%9D%9E%E5%85%AC%E5%B9%B3%E9%94%81)什么是公平锁和非公平锁？

- **公平锁：** 指多个线程按照申请锁的顺序来获取锁，线程直接进入队列中排队，队列中的第一个线程才能获得锁。公平锁的优点在于各个线程公平平等，每个线程等待一段时间后，都有执行的机会，而它的缺点就在于整体执行速度更慢，吞吐量更小。
- **非公平锁：** 多个线程加锁时直接尝试获取锁，能抢到锁到直接占有锁，抢不到才会到等待队列的队尾等待。非公平锁的优势就在于整体执行速度更快，吞吐量更大，但同时也可能产生线程饥饿问题，也就是说如果一直有线程插队，那么在等待队列中的线程可能长时间得不到运行。

### [#](https://xiaolincoding.com/interview/juc.html#%E9%9D%9E%E5%85%AC%E5%B9%B3%E9%94%81%E5%90%9E%E5%90%90%E9%87%8F%E4%B8%BA%E4%BB%80%E4%B9%88%E6%AF%94%E5%85%AC%E5%B9%B3%E9%94%81%E5%A4%A7)非公平锁吞吐量为什么比公平锁大？

- **公平锁执行流程**：获取锁时，先将线程自己添加到等待队列的队尾并休眠，当某线程用完锁之后，会去唤醒等待队列中队首的线程尝试去获取锁，锁的使用顺序也就是队列中的先后顺序，在整个过程中，线程会从运行状态切换到休眠状态，再从休眠状态恢复成运行状态，但线程每次休眠和恢复都需要从用户态转换成内核态，而这个状态的转换是比较慢的，所以公平锁的执行速度会比较慢。
- **非公平锁执行流程**：当线程获取锁时，会先通过 CAS 尝试获取锁，如果获取成功就直接拥有锁，如果获取锁失败才会进入等待队列，等待下次尝试获取锁。这样做的好处是，获取锁不用遵循先到先得的规则，从而避免了线程休眠和恢复的操作，这样就加速了程序的执行效率。

### [#](https://xiaolincoding.com/interview/juc.html#synchronized%E6%98%AF%E5%85%AC%E5%B9%B3%E9%94%81%E5%90%97)Synchronized 是公平锁吗？

Synchronized 不属于公平锁，ReentrantLock 是公平锁。

### [#](https://xiaolincoding.com/interview/juc.html#reentrantlock%E6%98%AF%E6%80%8E%E4%B9%88%E5%AE%9E%E7%8E%B0%E5%85%AC%E5%B9%B3%E9%94%81%E7%9A%84)ReentrantLock 是怎么实现公平锁的？

我们来看一下公平锁与非公平锁的加锁方法的源码。公平锁的锁获取源码如下：

```
protected final boolean tryAcquire(int acquires) {

    final Thread current = Thread.currentThread();
    int c = getState();

    if (c == 0) {

        if (!hasQueuedPredecessors() && //这里判断了 hasQueuedPredecessors()
                compareAndSetState(0, acquires)) {
            
            setExclusiveOwnerThread(current);
            
            return true;
        }

    } else if (current == getExclusiveOwnerThread()) {

        int nextc = c + acquires;

        if (nextc < 0) {
            throw new Error("Maximum lock count exceeded");
        }
        setState(nextc);
        return true;

    }
    return false;
}
```

非公平锁的锁获取源码如下：

```
final boolean nonfairTryAcquire(int acquires) {

    final Thread current = Thread.currentThread();
    int c = getState();

    if (c == 0) {

        if (compareAndSetState(0, acquires)) { //这里没有判断      hasQueuedPredecessors()

            setExclusiveOwnerThread(current);

            return true;

        }

    }

    else if (current == getExclusiveOwnerThread()) {

        int nextc = c + acquires;

        if (nextc < 0) // overflow

        throw new Error("Maximum lock count exceeded");

        setState(nextc);

        return true;

    }

    return false;

}
```

通过对比，我们可以明显的看出公平锁与非公平锁的 lock () 方法唯一的区别就在于公平锁在获取锁时多了一个限制条件：hasQueuedPredecessors () 为 false，这个方法就是判断在等待队列中是否已经有线程在排队了。

这也就是公平锁和非公平锁的核心区别，如果是公平锁，那么一旦已经有线程在排队了，当前线程就不再尝试获取锁；对于非公平锁而言，无论是否已经有线程在排队，都会尝试获取一下锁，获取不到的话，再去排队。这里有一个特例需要我们注意，针对 tryLock () 方法，它不遵守设定的公平原则。

例如，当有线程执行 tryLock () 方法的时候，一旦有线程释放了锁，那么这个正在 tryLock 的线程就能获取到锁，即使设置的是公平锁模式，即使在它之前已经有其他正在等待队列中等待的线程，简单地说就是 tryLock 可以插队。

看它的源码就会发现：

```
public boolean tryLock() {

    return sync.nonfairTryAcquire(1);

}
```

这里调用的就是 nonfairTryAcquire ()，表明了是不公平的，和锁本身是否是公平锁无关。综上所述，公平锁就是会按照多个线程申请锁的顺序来获取锁，从而实现公平的特性。

非公平锁加锁时不考虑排队等待情况，直接尝试获取锁，所以存在后申请却先获得锁的情况，但由此也提高了整体的效率。

## [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E6%B1%A0)线程池

### [#](https://xiaolincoding.com/interview/juc.html#%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8B%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%9A%84%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86)介绍一下线程池的工作原理

线程池是为了减少频繁的创建线程和销毁线程带来的性能损耗，线程池的工作原理如下图：

![[IMG-小林  Java并发编程面试题-20250807203928204.png]]

线程池分为核心线程池，线程池的最大容量，还有等待任务的队列，提交一个任务，如果核心线程没有满，就创建一个线程，如果满了，就是会加入等待队列，如果等待队列满了，就会增加线程，如果达到最大线程数量，如果都达到最大线程数量，就会按照一些丢弃的策略进行处理。

### [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%9A%84%E5%8F%82%E6%95%B0%E6%9C%89%E5%93%AA%E4%BA%9B)线程池的参数有哪些？

线程池的构造函数有 7 个参数：

![[IMG-小林  Java并发编程面试题-20250807204002799.webp]]

- **corePoolSize**：线程池核心线程数量。默认情况下，线程池中线程的数量如果 <= corePoolSize，那么即使这些线程处于空闲状态，那也不会被销毁。
- **maximumPoolSize**：线程池中最多可容纳的线程数量。当一个新任务交给线程池，如果此时线程池中有空闲的线程，就会直接执行，如果没有空闲的线程且当前线程池的线程数量小于 corePoolSize，就会创建新的线程来执行任务，否则就会将该任务加入到阻塞队列中，如果阻塞队列满了，就会创建一个新线程，从阻塞队列头部取出一个任务来执行，并将新任务加入到阻塞队列末尾。如果当前线程池中线程的数量等于 maximumPoolSize，就不会创建新线程，就会去执行拒绝策略。
- **keepAliveTime**：当线程池中线程的数量大于 corePoolSize，并且某个线程的空闲时间超过了 keepAliveTime，那么这个线程就会被销毁。
- **unit**：就是 keepAliveTime 时间的单位。
- **workQueue**：工作队列。当没有空闲的线程执行新任务时，该任务就会被放入工作队列中，等待执行。
- **threadFactory**：线程工厂。可以用来给线程取名字等等
- **handler**：拒绝策略。当一个新任务交给线程池，如果此时线程池中有空闲的线程，就会直接执行，如果没有空闲的线程，就会将该任务加入到阻塞队列中，如果阻塞队列满了，就会创建一个新线程，从阻塞队列头部取出一个任务来执行，并将新任务加入到阻塞队列末尾。如果当前线程池中线程的数量等于 maximumPoolSize，就不会创建新线程，就会去执行拒绝策略

### [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E6%B1%A0%E5%B7%A5%E4%BD%9C%E9%98%9F%E5%88%97%E6%BB%A1%E4%BA%86%E6%9C%89%E5%93%AA%E4%BA%9B%E6%8B%92%E6%8E%A5%E7%AD%96%E7%95%A5)线程池工作队列满了有哪些拒接策略？

当线程池的任务队列满了之后，线程池会执行指定的拒绝策略来应对，常用的四种拒绝策略包括：CallerRunsPolicy、AbortPolicy、DiscardPolicy、DiscardOldestPolicy，此外，还可以通过实现 RejectedExecutionHandler 接口来自定义拒绝策略。

四种预置的拒绝策略：

- CallerRunsPolicy，使用线程池的调用者所在的线程去执行被拒绝的任务，除非线程池被停止或者线程池的任务队列已有空缺。
- AbortPolicy，直接抛出一个任务被线程池拒绝的异常。
- DiscardPolicy，不做任何处理，静默拒绝提交的任务。
- DiscardOldestPolicy，抛弃最老的任务，然后执行该任务。
- 自定义拒绝策略，通过实现接口可以自定义任务拒绝策略。

### [#](https://xiaolincoding.com/interview/juc.html#%E6%9C%89%E7%BA%BF%E7%A8%8B%E6%B1%A0%E5%8F%82%E6%95%B0%E8%AE%BE%E7%BD%AE%E7%9A%84%E7%BB%8F%E9%AA%8C%E5%90%97)有线程池参数设置的经验吗？

- CPU 密集型：corePoolSize = CPU 核数 + 1
- IO 密集型：corePoolSize = CPU 核数 * 2

### [#](https://xiaolincoding.com/interview/juc.html#%E6%A0%B8%E5%BF%83%E7%BA%BF%E7%A8%8B%E6%95%B0%E8%AE%BE%E7%BD%AE%E4%B8%BA0%E5%8F%AF%E4%B8%8D%E5%8F%AF%E4%BB%A5)**核心线程数设置为 0 可不可以？**

可以，当核心线程数为 0 的时候，会创建一个非核心线程进行执行。

从下面的源码也可以看到，当核心线程数为 0 时，来了一个任务之后，会先将任务添加到任务队列，同时也会判断当前工作的线程数是否为 0，如果为 0，则会创建线程来执行线程池的任务。

![[IMG-小林  Java并发编程面试题-20250807203937944.webp]]

### [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%A7%8D%E7%B1%BB%E6%9C%89%E5%93%AA%E4%BA%9B)线程池种类有哪些？

- ScheduledThreadPool：可以设置定期的执行任务，它支持定时或周期性执行任务，比如每隔 10 秒钟执行一次任务，我通过这个实现类设置定期执行任务的策略。
- FixedThreadPool：它的核心线程数和最大线程数是一样的，所以可以把它看作是固定线程数的线程池，它的特点是线程池中的线程数除了初始阶段需要从 0 开始增加外，之后的线程数量就是固定的，就算任务数超过线程数，线程池也不会再创建更多的线程来处理任务，而是会把超出线程处理能力的任务放到任务队列中进行等待。而且就算任务队列满了，到了本该继续增加线程数的时候，由于它的最大线程数和核心线程数是一样的，所以也无法再增加新的线程了。
- CachedThreadPool：可以称作可缓存线程池，它的特点在于线程数是几乎可以无限增加的（实际最大可以达到 Integer.MAX_VALUE，为 2^31-1，这个数非常大，所以基本不可能达到），而当线程闲置时还可以对线程进行回收。也就是说该线程池的线程数量不是固定不变的，当然它也有一个用于存储提交任务的队列，但这个队列是 SynchronousQueue，队列的容量为 0，实际不存储任何任务，它只负责对任务进行中转和传递，所以效率比较高。
- SingleThreadExecutor：它会使用唯一的线程去执行任务，原理和 FixedThreadPool 是一样的，只不过这里线程只有一个，如果线程在执行任务的过程中发生异常，线程池也会重新创建一个线程来执行后续的任务。这种线程池由于只有一个线程，所以非常适合用于所有任务都需要按被提交的顺序依次执行的场景，而前几种线程池不一定能够保障任务的执行顺序等于被提交的顺序，因为它们是多线程并行执行的。
- SingleThreadScheduledExecutor：它实际和 ScheduledThreadPool 线程池非常相似，它只是 ScheduledThreadPool 的一个特例，内部只有一个线程。

### [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E6%B1%A0%E4%B8%80%E8%88%AC%E6%98%AF%E6%80%8E%E4%B9%88%E7%94%A8%E7%9A%84)线程池一般是怎么用的？

Java 中的 Executors 类定义了一些快捷的工具方法，来帮助我们快速创建线程池。《阿里巴巴 Java 开发手册》中提到，禁止使用这些方法来创建线程池，而应该手动 new ThreadPoolExecutor 来创建线程池。这一条规则的背后，是大量血淋淋的生产事故，最典型的就是 newFixedThreadPool 和 newCachedThreadPool，可能因为资源耗尽导致 OOM 问题。

所以，不建议使用 Executors 提供的两种快捷的线程池，原因如下：

- 我们需要根据自己的场景、并发情况来评估线程池的几个核心参数，包括核心线程数、最大线程数、线程回收策略、工作队列的类型，以及拒绝策略，确保线程池的工作行为符合需求，一般都需要设置有界的工作队列和可控的线程数。
- 任何时候，都应该为自定义线程池指定有意义的名称，以方便排查问题。当出现线程数量暴增、线程死锁、线程占用大量 CPU、线程执行出现异常等问题时，我们往往会抓取线程栈。此时，有意义的线程名称，就可以方便我们定位问题。

除了建议手动声明线程池以外，我还建议用一些监控手段来观察线程池的状态。线程池这个组件往往会表现得任劳任怨、默默无闻，除非是出现了拒绝策略，否则压力再大都不会抛出一个异常。如果我们能提前观察到线程池队列的积压，或者线程数量的快速膨胀，往往可以提早发现并解决问题。

### [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E6%B1%A0%E4%B8%ADshutdown-shutdownnow-%E8%BF%99%E4%B8%A4%E4%B8%AA%E6%96%B9%E6%B3%95%E6%9C%89%E4%BB%80%E4%B9%88%E4%BD%9C%E7%94%A8)线程池中 shutdown ()，shutdownNow () 这两个方法有什么作用？

从源码【高亮】注释可以很清晰的看出两者的区别：

- shutdown 使用了以后会置状态为 SHUTDOWN，正在执行的任务会继续执行下去，没有被执行的则中断。此时，则不能再往线程池中添加任何任务，否则将会抛出 RejectedExecutionException 异常
- 而 shutdownNow 为 STOP，并试图停止所有正在执行的线程，不再处理还在池队列中等待的任务，当然，它会返回那些未执行的任务。 它试图终止线程的方法是通过调用 Thread.interrupt () 方法来实现的，但是这种方法的作用有限，如果线程中没有 sleep 、wait、Condition、定时锁等应用，interrupt () 方法是无法中断当前的线程的。所以，ShutdownNow () 并不代表线程池就一定立即就能退出，它可能必须要等待所有正在执行的任务都执行完成了才能退出。

**shutdown 源码：**

```
public void shutdown() {
	final ReentrantLock mainLock = this.mainLock;
	mainLock.lock();
	try {
		checkShutdownAccess();
		// 高亮
		advanceRunState(SHUTDOWN);
		interruptIdleWorkers();
		onShutdown();
	} finally {
		mainLock.unlock();
	}
	tryTerminate();
}
```

**shutdownNow 源码：**

```
public List<Runnable> shutdownNow() {
	List<Runnable> tasks;
	final ReentrantLock mainLock = this.mainLock;
	mainLock.lock();
	try {
		checkShutdownAccess();
		// 高亮
		advanceRunState(STOP);
		interruptWorkers();
		// 高亮
		tasks = drainQueue();
	} finally {
		mainLock.unlock();
	}
	tryTerminate();
	// 高亮
	return tasks;
}
```

### [#](https://xiaolincoding.com/interview/juc.html#%E6%8F%90%E4%BA%A4%E7%BB%99%E7%BA%BF%E7%A8%8B%E6%B1%A0%E4%B8%AD%E7%9A%84%E4%BB%BB%E5%8A%A1%E5%8F%AF%E4%BB%A5%E8%A2%AB%E6%92%A4%E5%9B%9E%E5%90%97)提交给线程池中的任务可以被撤回吗？

可以，当向线程池提交任务时，会得到一个 `Future` 对象。这个 `Future` 对象提供了几种方法来管理任务的执行，包括取消任务。

取消任务的主要方法是 `Future` 接口中的 `cancel(boolean mayInterruptIfRunning)` 方法。这个方法尝试取消执行的任务。参数 `mayInterruptIfRunning` 指示是否允许中断正在执行的任务。如果设置为 `true`，则表示如果任务已经开始执行，那么允许中断任务；如果设置为 `false`，任务已经开始执行则不会被中断。

```
public interface Future<V> {
    // 是否取消线程的执行
    boolean cancel(boolean mayInterruptIfRunning);
    // 线程是否被取消
    boolean isCancelled();
    //线程是否执行完毕
    boolean isDone();
      // 立即获得线程返回的结果
    V get() throws InterruptedException, ExecutionException;
      // 延时时间后再获得线程返回的结果
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

取消线程池中任务的方式，代码如下，通过 future 对象的 cancel (boolean) 函数来定向取消特定的任务。

```
public static void main(String[] args) {
        ExecutorService service = Executors.newSingleThreadExecutor();
        Future future = service.submit(new TheradDemo());

        try {
          // 可能抛出异常
            future.get();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }finally {
          //终止任务的执行
            future.cancel(true);
        }
 }
```

## [#](https://xiaolincoding.com/interview/juc.html#%E5%9C%BA%E6%99%AF)场景

### [#](https://xiaolincoding.com/interview/juc.html#%E5%A4%9A%E7%BA%BF%E7%A8%8B%E6%89%93%E5%8D%B0%E5%A5%87%E5%81%B6%E6%95%B0-%E6%80%8E%E4%B9%88%E6%8E%A7%E5%88%B6%E6%89%93%E5%8D%B0%E7%9A%84%E9%A1%BA%E5%BA%8F)多线程打印奇偶数，怎么控制打印的顺序

可以利用 wait () 和 notify () 来控制线程的执行顺序。

以下是一个基于这种方法的简单示例：

```
public class PrintOddEven {
    private static final Object lock = new Object();
    private static int count = 1;
    private static final int MAX_COUNT = 10;

    public static void main(String[] args) {
        Runnable printOdd = () -> {
            synchronized (lock) {
                while (count <= MAX_COUNT) {
                    if (count % 2 != 0) {
                        System.out.println(Thread.currentThread().getName() + ": " + count++);
                        lock.notify();
                    } else {
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        };

        Runnable printEven = () -> {
            synchronized (lock) {
                while (count <= MAX_COUNT) {
                    if (count % 2 == 0) {
                        System.out.println(Thread.currentThread().getName() + ": " + count++);
                        lock.notify();
                    } else {
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        };

        Thread oddThread = new Thread(printOdd, "OddThread");
        Thread evenThread = new Thread(printEven, "EvenThread");

        oddThread.start();
        evenThread.start();
    }
}
```

在上面的示例中，通过一个共享的锁对象 lock 来控制两个线程的交替执行。一个线程负责打印奇数，另一个线程负责打印偶数，通过 wait () 和 notify () 方法来在两个线程之间实现顺序控制。当当前应该打印奇数时，偶数线程会进入等待状态，反之亦然。