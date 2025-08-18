[[db_idx]]
[[tx and lock]]
[[DDIA DB transaction 交易]]


---


- 讀操作可並發 → 多個執行緒可以同時讀數據，拿共享鎖
- 寫操作是互斥 → 但寫數據時需要拿獨占鎖
    - 當寫者寫數據時，其它寫者或讀者需要等待，直到這個寫者完成寫操作。


# 相關 config and 可選 stgy, opt

- 讀操作優先
	- 讀就先拿鎖
	- 缺點是寫可能會飢餓(一直拿不到)，如果一直都是讀拿到
	- 優點是並發量可以大
- 寫操作優先
	- 如果有寫過來，就先不接受讀。
	- 這樣可以解決讀操作優先下的寫飢餓→ 缺點是讀的並發量會受影響
- 不對讀和寫進行優先

### 很多語言都有實作, 這邊是 java 如何 使用 `ReentrantReadWriteLock` 介面

```java
package com.jcg.examples;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ThreadSafeArrayList<E> {
    // new 一個 readWriteLock
    private final ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    // readWriteLock 提供 readLock and writeLock
    private final Lock readLock = readWriteLock.readLock();
    private final Lock writeLock = readWriteLock.writeLock();

    private final List<E> list = new ArrayList<>();

    // 寫就是先上鎖，操作完解鎖
    public void set(E o) {
        writeLock.lock();
        try {
            list.add(o);
            System.out.println("Adding element by thread" + Thread.currentThread().getName());
        } finally {
            writeLock.unlock();
        }
    }

    // 讀也是同上
    public E get(int i) {
        readLock.lock();
        try {
            System.out.println("Printing elements by thread" + Thread.currentThread().getName());
            return list.get(i);
        } finally {
            readLock.unlock();
        }
    }

    // 使用上是透明的
    public static void main(String[] args) {
        ThreadSafeArrayList<String> threadSafeArrayList = new ThreadSafeArrayList<>();
        threadSafeArrayList.set("1");
        threadSafeArrayList.set("2");
        threadSafeArrayList.set("3");

        System.out.println("Printing the First Element : " + threadSafeArrayList.get(1));
    }
}
```

### 其他語言實作

POSIX 標準的 pthread_rwlock_t 與相關操作 [10]
ReadWriteLock[11] 接口，ReentrantReadWriteLock[5] 鎖，從 Java 版本 5 開始
Microsoft System.Threading.ReaderWriterLockSlim 鎖，用於 C#[與.NET](http://xn--oc1a.net/) 語言程序 [12]
std::shared_mutex read/write 鎖在 C++17[13]
boost::shared_mutex 與 boost::upgrade_mutex 鎖在 Boost C++ Libraries[14]
sync.RWMutex 在 Go 語言[15]
Phase fair reader–writer lock[16]
std::sync::RwLock，在 Rust 語言 [17]
Poco::RWLock，在 POCO C++ Libraries
mse::recursive_shared_timed_mutex 在 SaferCPlusPlus 庫，是支持 std::recursive_mutex（頁面存檔備份，存於網際網路檔案館）的 recursive ownership 語義的 std::shared_timed_mutex（頁面存檔備份，存於網際網路檔案館）的一個實現
txrwlock.ReadersWriterDeferredLock，用於 Twisted[18]

db 相關實作  [Concurrency Freaks: 50 years later, is Two-Phase Locking the best we can do?](http://concurrencyfreaks.blogspot.com/2023/09/50-years-later-is-two-phase-locking.html)