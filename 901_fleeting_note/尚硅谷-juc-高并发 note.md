## note
- [【尚硅谷】【2021最新版】跳槽大厂必备技术之JUC高并发编程 - YouTube](https://www.youtube.com/playlist?list=PLmOn9nNkQxJGuxM62QOkUwD_zroNZj-a7)
- [尚硅谷-juc-高并发 at master GitHub](https://github.com/re4388/document_classfication/tree/master/%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%8A%80%E6%9C%AF%E6%A0%88/%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B/%E5%B0%9A%E7%A1%85%E8%B0%B7-juc-%E9%AB%98%E5%B9%B6%E5%8F%91)
- [基础JUC高并发编程-尚硅谷 - 起跑线小言 - 博客园](https://www.cnblogs.com/qipaoxian/p/16860243.html)

## 1 进程与线程的概念

### 1.1 进程和线程

进程（Process）
- 计算机中的程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是操作系统结构的基础。
- 在早期面向进程设计的计算机结构中，进程是程序的基本执行实体
- 在当代面向线程设计的计算机结构中，进程是线程的容器。

线程（英语：thread）
- 是操作系统能够进行运算调度的最小单位。
- 被包含在进程之中，是进程中的实际运作单位。
- 一条线程指的是进程中一个单一顺序的控制流，一个进程中可以并发多个线程，每条线程并行执行不同的任务。


### 1.2 线程的状态

进入 java.lang.Thread 类，找到内部类 State 
可以看出 NEW（新建）、RUNNABLE（准备就绪）、BLOCKED（阻塞）、WAITING（等待 - 不见不散）、
TIMED_WAITING（等待 - 过时不候）、TERMINATED（终结） 等六种状态

### 1.3 wait 和 sleep
1. sleep 是 Thread 的静态方法；wait 是 Object 的方法，任何对象实例都能调用。
2. **sleep 不会释放锁**，**它也不需要占用锁**；**wait 会释放锁**，**但调用它的前提是当前线程占有锁（即代码要在 synchronized 中）**
3. 它们都可以被 interrupt 方法中断

![[IMG-尚硅谷-juc-高并发 note-20250805201321371.png]]

### 1.4 并发与并行

并发：同一时间间隔内多个线程正在执行，实际上是宏观上并行，微观上串行

并行：同一时刻多个线程正在执行

### 1.5 管程 

保证了同一时刻只有一个进程在管程内活动，即管程内定义的操作在同一时刻只被一个进程调用 (由编译器实现)。

### 1.6 用户线程和守护线程

用户线程：自定义线程
主线程结束了，只要有用户线程还在运行，jvm 繼續-> 程序繼續跑

守护线程：比如说垃圾回收线程
如果没有用户线程，只剩下守护线程 -> 程序結束

```java
public class MyThread {
    public static void main(String[] args) {

        //使用Lambda 表达式实现这个接口,创建 线程t1
        Thread t1 = new Thread(() -> {
            //判断是否是守护线程，（后台运行的）
            System.out.println(Thread.currentThread().getName() + "::" + Thread.currentThread().isDaemon());
    
			// 刻意用一個 while true loop
			while (true) {
                //主线程结束，程序还在运行，jvm 没停止
            }
    
        }, "t1");
    
        // 如果把他设置为守护线程  t1.setDaemon(true)，主线程结束这个程序没有用户线程了，结束了
        // 把他设置为用戶线程  t1.setDaemon(false)，或是 comment 這一行，主线程结束这个程序還有用户线程了，繼續跑
        t1.setDaemon(true);
        
        //启动线程
        t1.start();


        System.out.println(Thread.currentThread().getName() +"结束");
    }
}

```


## 2 Lock 接口

### 2.1 复习 synchronized

synchronized 的作用范围  
synchronized 是 Java 的关键字，是一种同步锁
能够修饰 一个对象，变量，方法，来控制这个所修饰的scope




使用 synchronized 实现卖票例子

多线程的编程步骤：
**第一：创建一个资源类，裡面會有属性和業務方法**
**第二：创建多线程调用资源类

```java


// 第一：创建一个资源类，属性和操作方法
class Ticket{
    private int rest = 1000;

    public synchronized void sale() {
        if (rest > 0)
        System.out.println(Thread.currentThread().getName()+"卖出一张票，还剩："+ --rest + "张；");
    }
}


public class SaleTicketTest {
    public static void main(String[] args) {

		// 建立資源類對象
        Ticket ticket = new Ticket();
    
		// 实现Runnable接口 來建立 thread
        Runnable r = new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 1000; i++) {
                    ticket.sale();
                }
            }
        };
        new Thread(r, "A").start();
        new Thread(r, "B").start();
        new Thread(r, "C").start();

    }
}
```




### 2.2 什么是 Lock 接口

##### Lock 接口介绍

Lock 实现提供比使用 synchronized 方法和语句可以获得的更广泛的锁定操作。
它们允许更灵活的结构化，可能具有完全不同的属性，并且可以支持多个相关联的对象 Condition 。

当在不同范围内发生锁定和解锁时，必须注意确保在锁定时执行的所有代码由 try-finally 或 try-catch 保护，以确保在必要时释放锁定。  

Lock 实现提供了使用 synchronized 方法和语句的附加功能，通过提供非阻塞尝试来获取锁 tryLock ()，尝试获取可被中断的锁 lockInterruptibly () ，以及尝试获取可以超时 tryLock (long, TimeUnit)。

##### Lock 实现可重入锁 ReentrantLock

```java
// 创建可重入锁
private final ReentrantLock lock = new ReentrantLock();

try {
    //上锁
    lock.lock();
    
	//功能操作
    ...
} finally {
    //解锁
    lock.unlock();
}
```

### 2.3 创建线程的多种方式 略

1. 继承 Thread 类、
2. 实现 Runnable 接口、

> 实现代码在上面已经使用，这里不再赘述。**实现 Runnable 接口** 是主要创建线程的方法之一

3. 使用 Callable 接口
4. 使用线程池
    

### 2.4 使用 Lock 实现卖票例子

```java
class Ticket{
    private int rest = 1000;

    // 创建可重入锁
    private final ReentrantLock lock = new ReentrantLock();

    public synchronized void sale() {
        //由于要防止上锁后出现异常导致无法解锁，所以使用try-catch-finally结构来解决
        try {
            //上锁
            lock.lock();

            if (rest > 0)
                System.out.println(Thread.currentThread().getName() + "sell 1, left:" + --rest);
        } finally {
            //解锁
            lock.unlock()
        }
    }
}

public class LSaleTicketTest {

    public static void main(String[] args) {
        
        Ticket ticket = new Ticket();
        
        Runnable r = new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 1000; i++) {
                    ticket.sale();
                }
            }
        };
    
        new Thread(r, "A").start();
        new Thread(r, "B").start();
        new Thread(r, "C").start();

    }
}


```

### 2.5 synchronized 和 Lock 两者差异

1. synchronized 是 java 关键字，内置，而 lock 不是内置，是一个类，可以实现同步访问且比 synchronized 中的方法更加丰富
    
2. synchronized 不会手动释放锁，而 lock 需手动释放锁（不解锁会出现死锁，需要在 finally 块中释放锁）
    
3. lock 等待锁的线程会相应中断，而 synchronized 不会相应，只会一直等待
    
4. 通过 Lock 可以知道有没有成功获取锁，而 synchronized 却无法办到
    
5. Lock 可以提高多个线程进行读操作的效率（当多个线程竞争的时候）  
    锁会出现死锁，需要在 finally 块中释放锁）
    
6. Lock 可以提高多个线程进行读操作的效率（当多个线程竞争的时候）
    

## 3 线程间通信

线程间通信的模型有两种：共享内存和消息传递

线程间的通信具体步骤：（涉及上中下部）
1. 创建资源类，在资源类中建立属性和操作方法
2. 在资源类操作方法：判断、操作、通知
3. 创建多个线程，调用资源类的操作方法
4. 防止虚拟唤醒问题

关键字 `synchronized` 与 `wait()/notify()` 这两个方法一起使用可以实现等待 / 通知模式

`Lock` 接口中的 `newContition()` 方法返回 `Condition` 对象，`Condition` 类也可以实现等待 / 通知模式

> 用 notify () 通知时，JVM 会随机唤醒某个等待的线程
> 
> 使用 Condition 类可以进行选择性通知， Condition 比较常用的两个方法：
>     1. await () 会使当前线程等待，同时会释放锁，当其他线程调用 signal () 时，线程会重新获得锁并继续执行  
>     2. signal () 用于唤醒一个等待的线程

操作线程的时候，等待线程使用 wait ()  
通知另外的线程操作用 notify ()、notifyAll ()  
假设有两个线程，该线程在执行过程中，判断值（不是该值等待，让其他线程抢），操作值，通知另外一个线程的调度

### 3.1 synchronized 案例

实现两个线程对 num 这个值操作，一个线程加 1，一个线程减 1，交替实现多次

```java
// 创建一个资源类
class Share{
    // 设置临界资源
    private int number = 0;

    // 实现+1操作
    public synchronized void incr() throws InterruptedException {
        // 操作：判断、干活、通知
        if (number != 0) {
            // number不为0，等待
            // wait 有一个特点，在哪里睡，就在哪里醒
            this.wait();	
        }
        number++;
        System.out.print(Thread.currentThread().getName()+"::"+number);
        // 唤醒其他线程
        // 注意这里的通知是随机的，就是只能通知全部
        this.notifyAll();
    }

    // 实现-1操作
    public synchronized void decr() throws InterruptedException {
        // 操作：判断、干活、通知
        if (number != 1) {
            // number不为0，等待
            this.wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName()+"::"+number);
        this.notifyAll();
    }
}
public class InterThreadCommunication {
    public static void main(String[] args) {
        Share share = new Share();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    for (int i = 0; i < 100; i++) {
                        share.incr();
                    }

                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"A").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    for (int i = 0; i < 100; i++) {
                        share.decr();
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"B").start();
    }
}
```

![[IMG-尚硅谷-juc-高并发 note-20250805201321622.png]]

### 3.2 虚假唤醒问题

虚假唤醒主要出现在多线程中出现。

同样使用上述案例，现在有四个线程，分别为 A，B，C，D，其中 A，C 线程做 + 1 操作，B，D 线程做 - 1 操作，想要的结尾应该是 A，C 线程输出值为 1，B，D 线程输出值为 0 。修改上述代码如下

```java
// 创建一个资源类
class Share{
    // 设置临界资源
    private int number = 0;

    // 实现+1操作
    public synchronized void incr() throws InterruptedException {
        // 操作：判断、干活、通知
        if (number != 0) {
            // number不为0，等待
            this.wait();
        }
        number++;
        System.out.print(Thread.currentThread().getName()+"::"+number+"--->");
        // 唤醒其他线程
        this.notifyAll();
    }

    // 实现-1操作
    public synchronized void decr() throws InterruptedException {
        // 操作：判断、干活、通知
        if (number != 1) {
            // number不为0，等待
            this.wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName()+"::"+number);
        this.notifyAll();
    }
}
public class InterThreadCommunication {
    public static void main(String[] args) {
        Share share = new Share();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    for (int i = 0; i < 100; i++) {
                        share.incr();
                    }

                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"A").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    for (int i = 0; i < 100; i++) {
                        share.decr();
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"B").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    for (int i = 0; i < 100; i++) {
                        share.incr();
                    }

                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"C").start();

            new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    for (int i = 0; i < 100; i++) {
                        share.decr();
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"D").start();
    }
}
```

输出结尾如下，显然最后输出的结果和我们预想的是不一样的。那么问题出在哪里呢？

![[IMG-尚硅谷-juc-高并发 note-20250805201322108.png]]

查找 JDK1.8 文档，在 `Object` 的 `wait()` 方法中有如下介绍

> 在一个参数版本中，中断和虚假唤醒是可能的，并且该方法应该始终在循环中使用

也就是说，这种现象叫做【虚假唤醒】。所谓虚假唤醒，就是 wait () 方法的一个特点，总结来说 wait () 方法使线程**在哪里睡就在哪里醒**。 这是什么意思呢？那就以上述代码为例。

当 A 进入临界区，BCD 三个线程在 if 判断后进入 wait () 等待，当 A 线程完成操作，此时 number 值为 1，notifyAll () 会随机唤醒一个线程。

现在 C 被唤醒，由于 wait () 方法使线程**在哪里睡就在哪里醒**，所以接下来 C 在执行时不会再通过 if 判断而是直接 + 1，此时 number 就是 2 了。从而导致最后输出的结果和我们预想的不一致。

按照 JDK1.8 文档的提示，将资源类的 incr () 方法和 decr () 方法中的 if 语句改为 *while 循环语句*，修改代码如下：

```java
// 创建一个资源类
class Share{
    // 设置临界资源
    private int number = 0;

    // 实现+1操作
    public synchronized void incr() throws InterruptedException {
        // 操作：判断、干活、通知
        while (number != 0) {
            // number不为0，等待
            // 哪里睡哪里起
            this.wait();
        }
        number++;
        System.out.print(Thread.currentThread().getName()+"::"+number+"--->");
        // 唤醒其他线程
        this.notifyAll();
    }

    // 实现-1操作
    public synchronized void decr() throws InterruptedException {
        // 操作：判断、干活、通知
        while (number != 1) {
            // number不为0，等待
            this.wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName()+"::"+number);
        this.notifyAll();
    }
}
```

此时输出结果符合我们预期：

![[IMG-尚硅谷-juc-高并发 note-20250805201322658.png]]

### 3.3 Lock 实现案例

在 Lock 接口中，有一个 newCondition () 方法，该方法返回一个新 Condition 绑定到该实例 Lock 实例。

Condition 类中有 await () 和 signalAll () 等方法，和 synchronized 实现案例中的 wait () 和 notifyAll () 方法相同。所以通过 Lock 接口创建一个 Condition 对象，由该对象的方法进行等待和唤醒操作

实例代码如下，主要改动的是资源类，main 方法中代码不变
```java
package multithread.juc1;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class Share {
    // 设置临界资源
    private int number = 0;
    
    // 创建一个Com  可重入锁
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    // 实现+1操作
    public void incr() {
        // 上锁
        lock.lock();
        try {
            // 判断 （放在循环中，防止虚假唤醒）
            while (number != 0) {
                condition.await();
            }
            // 干活
            number++;
            System.out.print(Thread.currentThread().getName() + "::" + number + "--->");
            // 通知其他线程
            condition.signalAll();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    // 实现-1操作
    public void decr() throws InterruptedException {
        // 上锁
        lock.lock();
        try {
            // 判断（wait()放在循环中，防止虚假唤醒）
            while (number != 1) {
                condition.await();
            }
            // 干活
            number--;
            System.out.println(Thread.currentThread().getName() + "::" + number);
            // 通知其他线程
            condition.signalAll();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) {

        Share share = new Share();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                share.incr();
            }
        }, "AA").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    share.decr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "BB").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                share.incr();
            }
        }, "CC").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    share.decr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "DD").start();
    }
}
```

测试结果如下：

![[IMG-尚硅谷-juc-高并发 note-20250805201323089.png]]

## 4 线程间定制化通信

**案例实现**  
案列：启动三个线程，按照如下要求：

> AA 打印 5 此，BB 打印 10 次，CC 打印 15 次，一共进行 10 轮
> 可以打印多轮

**具体思路：**  
每个线程添加一个标志位，是该标志位则执行操作，并且修改为下一个标志位，通知下一个标志 位的线程
创建一个可重入锁 private Lock lock = new ReentrantLock ();  
分别创建三个开锁通知 private Condition c1 = lock.newCondition ();（他们能实现指定唤醒）

**（注意）具体资源类中的 A 线程代码操作**  
上锁，（执行具体操作（判断、操作、通知），解锁）放于 try、finally，具体代码如下

```java
class Share{
    private int flag = 1;

    private Lock lock = new ReentrantLock();
    // 创建三个Comdition对象，为了定向唤醒相乘
    Condition c1 = lock.newCondition();
    Condition c2 = lock.newCondition();
    Condition c3 = lock.newCondition();

    public void Aprint(int loop) {
        //上锁
        lock.lock();
        try{
            // 判断
            while(flag!=1) {
                c1.await();
            }
            // 干活
            for (int i = 1; i <= 5; i++) {
                System.out.println(Thread.currentThread().getName() + " ::本次第" + i + "次打印，是第" + loop+ "次循环");
            }
            flag = 2; //修改标志位，定向唤醒 线程b
            // 唤醒
            c2.signal();// 定向唤醒
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            // 解锁
            lock.unlock();
        }
    }
    public void Bprint(int loop) {
        //上锁
        lock.lock();
        try{
            // 判断
            while(flag!=2) {
                c2.await();
            }
            // 干活
            for (int i = 1; i <= 10; i++) {
                System.out.println(Thread.currentThread().getName() + " ::本次第" + i + "次打印，是第" + loop+ "次循环");
            }
            flag = 3; //修改标志位，定向唤醒 线程b
            // 唤醒
            c3.signal();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            // 解锁
            lock.unlock();
        }
    }

    public void Cprint(int loop) {
        //上锁
        lock.lock();
        try{
            // 判断
            while(flag!=3) {
                c3.await();
            }
            // 干活
            for (int i = 1; i <= 15; i++) {
                System.out.println(Thread.currentThread().getName() + " ::本次第" + i + "次打印，是第" + loop+ "次循环");
            }
            flag = 1; //修改标志位，定向唤醒 线程b
            // 唤醒
            c1.signal();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            // 解锁
            lock.unlock();
        }
    }
}

public class CustomInterThreadCommunication {
    public static void main(String[] args) {
        Share share = new Share();

        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 1; i <= 10; i++) {
                    share.Aprint(i);
                }

            }
        },"A").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 1; i <= 10; i++) {
                    share.Bprint(i);
                }
            }
        },"B").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 1; i <= 100; i++) {
                    share.Cprint(i);
                }

            }
        },"C").start();
    }
}
```

注意：上面主要使用了 flag 变量和 c2.signal () 方法

测试结果如下：

![[IMG-尚硅谷-juc-高并发 note-20250805201323852.png]]

### 该案例需要注意

我们在学习操作系统中的同步可以知道，进程 / 线程同步有四个原则，都是为了禁止两个进程同时进入临界区。同步机制应该遵循以下原则

- 空闲让进：临界区空闲时，可以允许一个请求进入临界区的进程立即进入临界区
- 忙则等待：当已经有进程进入临界区的时候，其他试图进入临界区的进程必须等待
- 有限等待：对请求访问的进程，应保证能在有限时间内进入临界区
- 让权等待：当进程不能进入临界区的时候，应立即释放处理机，防止进程忙等待

很显然，该案例被称为单标志法。因为该案例设置一个公用整型变量 flag，用于指示被允许进入临界区的进程编号。

若 flag =1，则允许 P 1 进程进入临界区；若 flag =2，则允许 P2 进程进入临界区；若 flag =3，则允许 P3 进程进入临界区

该算法可确保每次只允许一个进程进入临界区。

但两个进程必须交替进入临界区，若某个进程不再进入临界区，则另一个进程也无法进入临界区

比如，若 P3 顺利进入临界区并从临界区离开，则此时临界区是空闲的，但 P1 并没有进入临界区的打算，flag = 1 一直成立，P3 就无法再次进入临界区。

违背了 "空闲让进" 原则，让资源利用不充分・

比如，将上述代码中的 main () 方法的 C 线程从 10 改为 20 ，C 线程不能访问 Share 资源了，因为 A 线程已经不再访问同时 flag 值不再改变了。

单标志法伪代码如下

```java
//P_0进程
while(turn!=0);
critical section;
turn=1;
remainder section;
```

```java
//P_1进程
while(turn!=1);
critical section；
turn=0;
remainder section;
```

## 5 集合的线程安全

### 5.1 集合线程不安全演示

在学习集合线程安全之前，先来看一下为什么在多线程中会出现不安全。

以 ArrayList 为例，我们进入 ArrayList 源码，找到 add () 方法，源码如下

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
```

显然，add () 方法没有使用同步互斥，所以在多线程并发是，会出现线程异常，测试代码如下

```java
public class SetUnsefertyTest {
    public static void main(String[] args) {
    
        // 创建ArrayList 集合
        ArrayList<String> list = new ArrayList<>();

        // 创建10个线程，往 list 中添加元素
        for (int i = 0; i < 10; i++) {
            new Thread(()->{
                // 向集合中添加内容
                list.add(UUID.randomUUID().toString().substring(0,8));
                // 从集合中取出内容
                System.out.println(list);
            },String.valueOf(i)).start();
        }
    }
}
```

会出现如下异常

![[IMG-尚硅谷-juc-高并发 note-20250805201324884.png]]

解决该方法主要有三种，即使用这三个类：Vector、Collections、CopyOnWriteArrayList（常用）

### 5.2 解决方案 - Vector

进入在 Vector 的底层实现，找到 add () 方法是线程安全的，源代码如下，可以发现 Vector 将 add () 加上同步关键字了

```java
public synchronized boolean add(E e) {
    modCount++;
    ensureCapacityHelper(elementCount + 1);
    elementData[elementCount++] = e;
    return true;
}
```

但是 Vector 用的不多，因为每次对添加的元素上锁，而且使用的是重量级锁 synchronized 是十分占用资源的，效率是十分低下的，也是 java1.0 就出现的古老的方法。其用法和 ArrayList 一样。

### 5.3 解决方案 - Collections

进入 Collections 的底层，找到 synchronizedList (List list) 方法，源代码如下，synchronizedList (List list) 方法返回指定列表支持的同步（线程安全的）列表
```java
public static <T> List<T> synchronizedList(List<T> list) {
    return (list instanceof RandomAccess ?
            new SynchronizedRandomAccessList<>(list) :
            new SynchronizedList<>(list));
}

static <T> List<T> synchronizedList(List<T> list, Object mutex) {
    return (list instanceof RandomAccess ?
            new SynchronizedRandomAccessList<>(list, mutex) :
            new SynchronizedList<>(list, mutex));
}
```

**对 Collections 工具类的使用如下**
```java
List<String> list = Collections.synchronizedList(new ArrayList<>());
```

换成对这个集合操作就不会发生并发异常

### 5.4 解决方案 - CopyOnWriteArrayList

这种方法用的最多。

CopyOnWriteArrayList 涉及的底层原理为 **写时复制技术**

- 读的时候并发（多个线程操作）
- 写的时候独立，先复制相同的空间到某个区域，将其写到新区域，旧新合并，并且读新区域（每次加新内容都写到新区域，覆盖合并之前旧区域，读取新区域添加的内容）

![[IMG-尚硅谷-juc-高并发 note-20250805201326331.png]]

进入 CopyOnWriteArrayList 底层，来看一下它是怎么实现的，其 add () 源代码如下
使用 ReentrantLock
```java
public boolean add(E e) {
    // 声明一个重进入锁
    final ReentrantLock lock = this.lock;
    // 上锁
    lock.lock();
    try {
        // 获取原来的列表
        Object[] elements = getArray();
        // 获取原来列表的长度
        int len = elements.length;
        // 复制一个与原来的列表一样的列表
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        // 将新加入的元素放到列表末尾
        newElements[len] = e;
        // 旧新合并
        setArray(newElements);
        return true;
    } finally {
        // 解锁
        lock.unlock();
    }
}
```

对 CopyOnWriteArrayList 的使用如下
```java
List<String> list = new CopyOnWriteArrayList<>();
```

### 5.5 对上述三种方式的总结

对比三者来看，Vector 和 Collections 虽然也可以实现同步，但由于这两种方法在底层都使用了 synchronized 重量级锁，使其效率很低，**所以对 ArrayList 的同步主要采用 CopyOnWriteArrayList**

### 5.6 HashSet 的线程不安全

HashSet 同时读写时也会出现 ConcurrentModificationException 异常

进入 HashSet 底层，来看一下它是怎么实现的，其 add () 源代码如下

```java
public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}
```

显然，他的问题和 ArrayList 一样，没有对 add (E e) 方法做同步处理

其解决方法与 CopyOnWriteArrayList 类似，在 JDK1.8 中，也有一个类叫做 CopyOnWriteArraySet ，其底层代码如下

```java
public boolean add(E e) {
    // private final CopyOnWriteArrayList<E> al;
    return al.addIfAbsent(e);
}
```

通过 Debug 找到了对关键的一个函数，发现其实现方式与 CopyOnWriteArrayList 底层实现方式类似

```java
// e 表示添加的元素
// snapshot 表示被复制的列表
private boolean addIfAbsent(E e, Object[] snapshot) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        // 获取当前列表和列表长度
        Object[] current = getArray();
        int len = current.length;
        // 如果现在的列表和之前的列表不同，其实显然是不同的
        if (snapshot != current) {
            // Optimize for lost race to another addXXX operation
            // 取较小的长度列表
            int common = Math.min(snapshot.length, len);
            for (int i = 0; i < common; i++)
                if (current[i] != snapshot[i] && eq(e, current[i]))
                    return false;
            if (indexOf(e, current, common, len) >= 0)
                return false;
        }
        // 后面就是将其写到新区域，旧新合并
        Object[] newElements = Arrays.copyOf(current, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}
```

对 CopyOnWriteArraySet 的使用如下

```java
CopyOnWriteArraySet<String> list = new CopyOnWriteArraySet<>();
```

### 5.7 HashMap 的线程不安全

HashMap 同时读写时一样会出现 ConcurrentModificationException 异常

进入 HashMap 底层，来看一下它是怎么实现的，其 put (K key, V value) 源代码如下

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```

## 6 多线程锁 （公平锁和非公平锁，死锁，可重锁）

某一个时刻内，**只能有唯一一个线程去访问这些 synchronized 方法**

- 所有的静态同步方法用的也是同一把锁，就是类对象本身
- 这两把锁是两个不同的对象，所以静态同步方法与非静态同步方法之间是不会有竞态条件
- 一旦一个静态同步方法获取锁后，其他的静态同步方法都必须等待该方法释放锁后才能获取锁
	- 其他的静态同步方法的意思？不管是同一个实例对象的静态同步方法，还是不同的实例对象的静态同步方法，只要它们同一个类的实例对象，就要等

- synchronized 锁的是方法，则是对象锁，同个对象锁的机制要等待，不同对象锁的机制调用同一个不用等待
- 加了 static 则为 class 锁而不是对象锁
- 对于同步方法块，锁是 synchronized 括号里配置对象

### 6.1 synchronized 锁的八种情况

**通过具体例子进行分析**

```java
class Phone {

    public synchronized void sendSMS() throws Exception {
        //停留4秒
        TimeUnit.SECONDS.sleep(4);
        System.out.println("------sendSMS");
    }
    public synchronized void sendEmail() throws Exception {
        System.out.println("------sendEmail");
    }
    public void getHello() {
        System.out.println("------getHello");
    }
}

public class SynchronizedLockTest {
    public static void main(String[] args) throws Exception {
        Phone phone = new Phone();
        Phone phone2 = new Phone();
        new Thread(() -> {
            try {
                phone.sendSMS();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "A").start();
        Thread.sleep(100);
        new Thread(() -> {
            try {
               // phone.sendEmail();
               // phone.getHello();
                phone2.sendEmail();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "B").start();
    }
} 
```

不同案例输出的不同结果

1 标准访问，先打印短信还是邮件  
------sendSMS  
------sendEmail  
2 停 4 秒在短信方法内，先打印短信还是邮件  
------sendSMS  
------sendEmail  

(因為都是同一個鎖)


3 新增普通的 hello 方法，是先打短信还是 hello  
------getHello  
------sendSMS  
4 现在有两部手机，先打印短信还是邮件  
------sendEmail  
------sendSMS  
5 两个静态同步方法，1 部手机，先打印短信还是邮件  
------sendSMS  
------sendEmail  
6 两个静态同步方法，2 部手机，先打印短信还是邮件  
------sendSMS  
------sendEmail  
7 1 个静态同步方法，1 个普通同步方法，1 部手机，先打印短信还是邮件  
------sendEmail  
------sendSMS  
8 1 个静态同步方法，1 个普通同步方法，2 部手机，先打印短信还是邮件  
------sendEmail  
------sendSMS

进行其他测试：

```java
package multithread.juc1;

import java.util.concurrent.TimeUnit;

class Phone {

    public synchronized void sendSMS() throws Exception {
        System.out.println("进入了synchronized void sendSMS()");
        //停留4秒
        TimeUnit.SECONDS.sleep(4);
        System.out.println("------sendSMS");
    }
    public synchronized void sendEmail() throws Exception {
        System.out.println("------sendEmail");
    }
    public void getHello() {
        System.out.println("------getHello");
    }
}

public class SynchronizedLockTest {
    public static void main(String[] args) throws Exception {
        Phone phone = new Phone();
        Phone phone2 = new Phone();
        System.out.println("Thread.sleep(10000);======0");
        new Thread(() -> {
            try {
                phone.sendSMS();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "A").start();
        System.out.println("Thread.sleep(10000);======1" + Thread.currentThread().getName());
        Thread.sleep(10000);
        System.out.println("Thread.sleep(10000);======2");
        new Thread(() -> {
            try {
                // phone.sendEmail();
                phone.getHello();
                // phone2.sendEmail();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "B").start();
        System.out.println("Thread.sleep(10000);======3");
    }
} 
```

打印结果如下：

```xml
Thread.sleep(2000);======0
Thread.sleep(2000);======1main
进入了synchronized void sendSMS()
------sendSMS
Thread.sleep(2000);======2
Thread.sleep(2000);======3
------getHello

Process finished with exit code 0
```

线程执行休眠之后，会让出 cpu 继续执行其他

可以参看文章：

Java Thread 之 Sleep () 使用方法总结

[https://blog.csdn.net/Weixiaohuai/article/details/83994146](https://blog.csdn.net/Weixiaohuai/article/details/83994146)

### 6.2 对上述例子的总结

> 同样的对象访问不同的同步锁，是按照顺序执行  
> 不同对象访问不同的同步锁，按照顺序执行
> 同样的对象访问同步锁和不同步锁，不同步锁先执行  

> 同一对象访问不同的静态同步锁，按照顺序执行  
> 不同对象访问不同的静态同步锁，按照顺序执行

> 同一对象访问一个静态同步锁，一个同步锁，先执行同步锁  
> 不同对象访问一个静态同步锁，一个同步锁，先执行同步锁，即先出同步锁在出静态同步锁

**概括来说，锁之间的执行顺序是：不同步锁 > 同步锁 > 静态同步锁**

synchronized 实现同步的基础：Java 中的每一个对象都可以作为锁。真体表现为以下 3 种形式。

- 对于普通同步方法，锁是当前实例对象。
- 对于静态同步方法，锁是当前类的 class 对象。
- 对于同步方法块，锁是 synchonized 括号里配置的对象

### 6.3 公平锁和非公平锁

- **公平锁**：效率相对低 ，但是 cpu 的利用高了
- **非公平锁**：效率高，但是线程容易饿死（所有的工作，有一个线程完成）

**用法：** 在创建可 [重入锁] 时，想构造器中传入 true
```java
private final ReentrantLock lock = new ReentrantLock(true);
```

因为 ReentrantLock 的构造器源码如下：
```java
// 在没有传入参数时，默认创建一个非公平锁
public ReentrantLock() {
    sync = new NonfairSync();
}


// 当传入一个true值时，为公平锁
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

### 6.4 可重入锁

synchronized 和 lock 都是可重入锁

- sychronized 是隐式锁，不用手工上锁与解锁，而 lock 为显示锁，需要手工上锁与解锁
- 可重入锁也叫递归锁

而且有了可重入锁之后，破解第一把之后就可以一直进入到内层结构

**嵌套实现代码 他能进入下一个锁内而不会出现死锁**

**synchronized 的示例代码**

```java
/**
 * 演示可重入锁是什么意思，可重入，就是可以重复获取相同的锁而不会出现死锁
 * synchronized和ReentrantLock都是可重入的
 * */
public class WhatReentrantSynchronized {
    // 创建一个锁对象
    static Object mylock = new Object();
    public static void main(String[] args) {
        new Thread(()->{
            // 创建第一个锁
            synchronized (mylock){
                System.out.println("这是第一层锁");
                synchronized (mylock){
                    System.out.println("这是第二层锁");
                }
            }
        }).start();
    }
}
```

**ReentrantLock 的示例代码**

```java
/**
 * lock和unlock的数量必须一致，否则会出现死锁
 * */
public class WhatReentrantLock {
    public static void main(String[] args) {
        ReentrantLock lock = new ReentrantLock();
        new Thread(()->{
            // 上锁
            lock.lock();
            try {
                System.out.println("这是第一层锁");
                // 再次上锁
                lock.lock();
                try{
                    System.out.println("这是第二层锁");
                }finally {
                    lock.unlock();
                }
            }finally {
                lock.unlock();
            }
        }).start();
    }
}

```

### 6.5 死锁

**两个或以上的进程因为争夺资源而造成互相等待资源的现象称为死锁**

![[IMG-尚硅谷-juc-高并发 note-20250805201327929.png]]

**产生死锁的原因：**

1. 系统资源不足
2. 系统资源分配不当
3. 进程运行顺序不当

**我们有时候不知道是否是死锁 。那么怎么来验证呢？ （电脑配置的有环境变量，在命令窗口）**

1. jps 类似于 linux 中的 `ps -ef` 查看进程号
2. jstack 自带的堆栈跟踪工具

**具体死锁的操作代码实列**  
可理解背下来，大厂面试可考，死锁的简单案例

```java
public class DeadLock {
 
    //创建两个对象
    static Object a = new Object();
    static Object b = new Object();
 
    public static void main(String[] args) {
        new Thread(()->{
            // 获取a这把锁
            synchronized (a) {
                System.out.println(Thread.currentThread().getName()+" 持有锁a，试图获取锁b");
                try {
                    TimeUnit.SECONDS.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (b) {
                    System.out.println(Thread.currentThread().getName()+" 获取锁b");
                }
            }
        },"A").start();
 
        new Thread(()->{
            // 获取b这把锁
            synchronized (b) {
                System.out.println(Thread.currentThread().getName()+" 持有锁b，试图获取锁a");
                try {
                    TimeUnit.SECONDS.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (a) {
                    System.out.println(Thread.currentThread().getName()+" 获取锁a");
                }
            }
        },"B").start();
    }
}
```

## 7 Callable 接口

**创建线程的多种方式：**

1. 继承 Thread 类
2. 实现 Runnable 接口
3. Callable 接口
4. 线程池

### 7.1 Callable 接口创建线程

目前学习了有两种创建线程的方法，一种是通过创建 Thread 类，另一种是通过使用 Runnable 创建线程，但是，Runnable 缺少的一项功能是，当线程终止时（即 run () 完成时），我们无法使线程返回结果。为了支持此功能，Java 中提供了 Callable 接口

> 比较 Runnable 接口和 Callable 接口
> 
> 1. Callable 中的 call () 计算结果，如果无法计算结果，会抛出异常
>     
> 2. Runnable 中的 run () 使用实现接口 Runnable 的对象创建一个线程时，启动该线程将导致在独立执行的线程中调用该对象的 run 方法
>     
> 
> 总的来说：run () 没有返回值，不会抛出异常。而 call () 有返回值，会抛出异常

因为 Thread 的构造函数中没有 Callable 接口的参数设置，直接替换不可以，只能用下面这种线程创建方法（找一个类，即和 Runnable 接口有关系，又和 Callable 接口有关系）

发现 Runnable 接口有实现类 FutureTask（中间对象）

FutureTask 的构造函数有 Callable 参数，通过 FutureTask 创建线程对象

使用 lambda 方式创建代码如下：

```java
public class CallableTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        new Thread(()->{
            System.out.println(Thread.currentThread().getName()+"执行Runnable");
        }).start();
        FutureTask<String> task = new FutureTask<>(() -> {
            System.out.println(Thread.currentThread().getName() + "使用Callable接口");
            return "Callable接口返回值";
        });
        new Thread(task).start();
        System.out.println("Callable返回值：" + task.get());
    }
}
```

输出为：

```java
Thread-0执行Runnable
Thread-1使用Callable接口
Callable返回值：Callable接口返回值
```

详细代码可以参考如下代码

```java
//比较 callable和runnable 的区别
class MyThread1 implements Runnable{
    @Override
    public void run() {
        //这里没有返回值
    }
}
class MyThread2 implements Callable{
    @Override
    public Object call() throws Exception {
        System.out.println(Thread.currentThread().getName()+"线程运行");
        return "Callable 的实现线程";  //有返回值
    }
}
 
public class diffentence {
 
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //创建 实现Runnable 的线程
        new Thread( new MyThread1(),"t1" ).start();
        //创建 实现Callable 的线程 不能直接替换 ，没有这个类型的构造方法
        // new Thread( new MyThread2(),"t2" ).start();
        //选择FutureTask 他是 Runnable 的实现类，而且构造方法含有Callable类型
        FutureTask<String>  task = new FutureTask(new MyThread2());
        new Thread(task,"hhh").start();
        System.out.println("返回值"+task.get()); //调用里面的返回值
    }
}
```

### 7.2 FutureTask

进入 FutureTask 底层源码可以看到它的构造器

```java
// 创建一个FutureTask，一旦运行就执行给定的Callable
public FutureTask(Callable<V> callable) {
    if (callable == null)
        throw new NullPointerException();
    this.callable = callable;
    this.state = NEW;       // ensure visibility of callable
}
// 创建一个FutureTask，一旦运行就执行给定的Ru你那边了，并安排成功完成时get返回给定的结果
public FutureTask(Runnable runnable, V result) {
    this.callable = Executors.callable(runnable, result);
    this.state = NEW;       // ensure visibility of callable
}
```

其他常用的代码：

```java
// get()获取结果
public V get() throws InterruptedException, ExecutionException {
    int s = state;
    if (s <= COMPLETING)
        s = awaitDone(false, 0L);
    return report(s);
}
// outcome就是返回值
private V report(int s) throws ExecutionException {
    Object x = outcome;
    if (s == NORMAL)
        return (V)x;
    if (s >= CANCELLED)
        throw new CancellationException();
    throw new ExecutionException((Throwable)x);
}
```

**未来的任务，如果运行过一次，那么下一次，就直接得到结果**

## 8 JUC 强大辅助类

**该辅助类主要讲述三个**

1. 减少计数 [CountDownLatch]
2. 循环栅栏 [CyclicBarrier]
3. 信号灯 [Semaphore]

### 8.1 减少计数 CountDownLatch

该类的[构造方法](https://so.csdn.net/so/search?q=%E6%9E%84%E9%80%A0%E6%96%B9%E6%B3%95&spm=1001.2101.3001.7020)为 `CountDownLatch(int count)` 构造一个用给定计数初始化的 `CountDownLatch` 在这里插入代码片

```java
public CountDownLatch(int count) {
    if (count < 0) throw new IllegalArgumentException("count < 0");
    this.sync = new Sync(count);
}
```

CountDownLatch 类可以设置一个计数器，然后通过 countDown 方法来进行减 1 的操作，使用 await 方法等待计数器不大于 0，然后继续执行 await 方法之后的语句。具体步骤可以演化为**定义一个类，减 1 操作，并等待到 0，为 0 执行结果。**

**两个常用的主要方法**  
await () 使当前线程在锁存器倒计数至零之前一直在等待，除非线程被中断

```java
public void await() throws InterruptedException {
    sync.acquireSharedInterruptibly(1);
}
```

countDown () 递减锁存器的计数，如果计数达到零，将释放所有等待的线程

```java
public void countDown() {
    sync.releaseShared(1);
}
public final boolean releaseShared(int arg) {
    if (tryReleaseShared(arg)) {
        doReleaseShared();
        return true;
    }
    return false;
}
```

通过下面具体的案例进行加深代码：

6 个同学陆续离开教室之后，班长才能锁门  
如果不加 CountDownLatch 类，会出现线程混乱执行，同学还未离开教室班长就已经锁门了

在不使用 CountDownLatch 的时候

```java
public class CountDownLatchTest {
    //6个同学陆续离开教室之后，班长锁门
    public static void main(String[] args) throws InterruptedException {
        // 创建六个线程，模拟六个学生
        for (int i = 1; i <=6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"离开教室");
            },String.valueOf(i)).start();
        }
        System.out.println(Thread.currentThread().getName()+"锁门");
    }
}
```

输出结果为：

> 2 离开教室  
> 6 离开教室  
> 5 离开教室  
> 4 离开教室  
> 1 离开教室  
> main 锁门  
> 3 离开教室

这就造成了，线程的混乱问题 ，可以通过 CountDownLatch 计数，直到为 0 ，才结束

```java
public class CountDownLatchTest {
    public static void main(String[] args) throws InterruptedException {
        // 创建CountDown对象并设置初始值
        CountDownLatch countDownLatch = new CountDownLatch(6);
        // 创建六个线程，模拟六个学生
        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"离开教室");
                // 计数 -1
                countDownLatch.countDown();
            },String.valueOf(i)).start();
        }
        // 等待，直到达到零
        countDownLatch.await();
        System.out.println(Thread.currentThread().getName()+"锁门");
    }
}
```

### 8.2 循环栅栏 CyclicBarrier

该类是 允许一组线程 互相 等待，直到到达某个公共屏障点，在设计一组固定大小的线程的程序中，这些线程必须互相等待，因为 barrier 在释放等待线程后可以重用，所以称为循环 barrier

常用的构造方法有：CyclicBarrier (int parties，Runnable barrierAction)，其底层代码如下

```java
public CyclicBarrier(int parties, Runnable barrierAction) {
    if (parties <= 0) throw new IllegalArgumentException();
    this.parties = parties;
    this.count = parties;
    // 最后需要执行的方法
    this.barrierCommand = barrierAction;
}
```

创建一个新的 CyclicBarrier，它将在给定数量的参与者（线程）处于等待状态时启动，并在启动 barrier 时执行给定的屏障操作，该操作由最后一个进入 barrier 的线程操作

常用的方法有：

await () 在所有的参与者都已经在此 barrier 上调用 await 方法之前一直等待。

```java
public int await() throws InterruptedException, BrokenBarrierException {
    try {
        return dowait(false, 0L);
    } catch (TimeoutException toe) {
        throw new Error(toe); // cannot happen
    }
}
private int dowait(boolean timed, long nanos)
    throws InterruptedException, BrokenBarrierException,
TimeoutException {
    final ReentrantLock lock = this.lock;
    // 同步锁
    lock.lock();
    try {
        final Generation g = generation;

        if (g.broken)
            throw new BrokenBarrierException();

        if (Thread.interrupted()) {
            breakBarrier();
            throw new InterruptedException();
        }
		// 每次执行 CyclicBarrier 一次障碍数会加一，距离目标障碍数-1
        int index = --count;
        // 当达到目标障碍数执行if内代码
        if (index == 0) {  // tripped
            boolean ranAction = false;
            try {
                final Runnable command = barrierCommand;
                if (command != null)
                    // 执行Runnable代码
                    command.run();
                ranAction = true;
                nextGeneration();
                // 返回0
                return 0;
            } finally {
                if (!ranAction)
                    breakBarrier();
            }
        }

        // 循环，直到触发、中断、中断或超时  
        for (;;) {
            try {
                if (!timed)
                    trip.await();
                else if (nanos > 0L)
                    nanos = trip.awaitNanos(nanos);
            } catch (InterruptedException ie) {
                if (g == generation && ! g.broken) {
                    breakBarrier();
                    throw ie;
                } else {
                    // We're about to finish waiting even if we had not
                    // been interrupted, so this interrupt is deemed to
                    // "belong" to subsequent execution.
                    Thread.currentThread().interrupt();
                }
            }

            if (g.broken)
                throw new BrokenBarrierException();

            if (g != generation)
                return index;

            if (timed && nanos <= 0L) {
                breakBarrier();
                throw new TimeoutException();
            }
        }
    } finally {
        lock.unlock();
    }
}
```

从底层代码可知， CyclicBarrier 的构造方法第一个参数是目标障碍数，每次执行 CyclicBarrier 一次障碍数会 + 1，如果达到了目标障碍数，才会执行 cyclicBarrier.await () 之后的语句。可以将 CyclicBarrier 理解为 + 1 操作（指与目标障碍数的距离

**具体案例**  
集齐 7 颗龙珠就可以召唤神龙

```java
public class CyclicBarrirtTest {
    // 创建固定值
    private static final int NUMBER  = 7;
    public static void main(String[] args) {
        // 每次执行 CyclicBarrier 一次障碍数会加一，如果达到了目标障碍数，才会执行 cyclicBarrier.await()之后的语句。
        CyclicBarrier cyclicBarrier = new CyclicBarrier(NUMBER, () -> {
            System.out.println("****集齐7颗龙珠就可以召唤神龙");
        });
        // 创建六个线程，模拟六个学生
        for (int i = 1; i <= 7; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+" 星龙被收集到了");
                try {
                    // 计数 +1
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }

            },String.valueOf(i)).start();
        }
    }
}
```

输出结果为：

> 3 星龙被收集到了  
> 5 星龙被收集到了  
> 4 星龙被收集到了  
> 2 星龙被收集到了  
> 1 星龙被收集到了  
> 6 星龙被收集到了  
> 7 星龙被收集到了  
> **** 集齐 7 颗龙珠就可以召唤神龙

### 8.3 信号灯 Semaphore

一个计数信号量，从概念上将，信号量维护了一个许可集，如有必要，在许可可用前会阻塞每一个 acquire ()，然后在获取该许可。每个 release () 添加一个许可，从而可能释放一个正在阻塞的获取者。但是，不使用实际的许可对象，Semaphore 只对可用许可的号码进行计数，并采取相应的行动

具体常用的构造方法有：

Semaphore (int permits) 创建具有给定的许可数和非公平的公平设置的 Semapore

```java
public Semaphore(int permits) {
    // 默认创建非公平锁
    sync = new NonfairSync(permits);
}
public Semaphore(int permits, boolean fair) {
    // fair为true时，为公平锁
    sync = fair ? new FairSync(permits) : new NonfairSync(permits);
}
```

设置许可数量 `Semaphore semaphore = new Semaphore(3);`

一般 acquire (）都会抛出异常，release 在 finally 中执行

> 通过具体案例  
> 6 辆汽车，停 3 个车位

示例代码如下:

```java
public class SemaphoreTest {
    public static void main(String[] args) {
        //创建Semaphore，设置许可数量
        Semaphore semaphore = new Semaphore(3);
        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                try {
                    // 抢占
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+"抢到了车位");
                    // 设置停车时间
                    TimeUnit.SECONDS.sleep(new Random().nextInt(5));
                    // 离开车位
                    System.out.println(Thread.currentThread().getName()+"------离开了车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    //释放
                    semaphore.release();
                }
            },String.valueOf(i)).start();
        }
    }
}
```

输出结果如下：

> 2 抢到了车位  
> 1 抢到了车位  
> 3 抢到了车位  
> 2------ 离开了车位  
> 4 抢到了车位  
> 4------ 离开了车位  
> 5 抢到了车位  
> 3------ 离开了车位  
> 6 抢到了车位  
> 1------ 离开了车位  
> 5------ 离开了车位  
> 6------ 离开了车位

## 9 读写锁 ReentrantReadWriteLock

### 9.1 悲观锁和乐观锁

悲观锁：顾名思义，它是干什么都很悲观，所以在操作的时候，每次都先上锁，使用时解锁

乐观锁：它很乐观，多线程，并不上锁，但是会发生线程安全问题，通过比较版本号来同步

### 9.2 表锁 | 行锁 | 读锁 | 写锁

表锁：整个表操作，不会发生死锁

行锁：每个表中的单独一行进行加锁，会发生死锁

读锁：共享锁（可以有多个人读），会发生死锁

写锁：独占锁（只能有一个人写），会发生死锁

比如说，

1 线程修改时候，需要等待 2 线程读之后

2 线程修改时候，需要等待 1 线程读之后

所以，1-2 线程发生死锁

### 9.3 读写锁概述

读写锁：一个资源可以被多个读线程访问，也可以被一个写线程访问，但不能同时存在读写线程，读写互斥，读读共享

读写锁 ReentrantReadWriteLock

读锁为 ReentrantReadWriteLock.ReadLock，readLock () 方法

写锁为 ReentrantReadWriteLock.WriteLock，writeLock () 方法

创建读写锁对象 private ReadWriteLock rwLock = new ReentrantReadWriteLock ();  
写锁 加锁 rwLock.writeLock ().lock ();，解锁为 rwLock.writeLock ().unlock ();  
读锁 加锁 rwLock.readLock ().lock ();，解锁为 rwLock.readLock ().unlock ();

在不加读写锁的情况下：

```java
class MyCache{
    // 需要模仿从Map中取对象，所以先穿件一个map对象
    private volatile Map<String, Object> map = new HashMap<>();

    // 放数据
    public void put(String key, Object value) {
        try {
            System.out.println(Thread.currentThread().getName()+"正在写操作"+key);
            // 暂停一会
            TimeUnit.MICROSECONDS.sleep(300);
            // 放数据
            map.put(key, value);
            System.out.println(Thread.currentThread().getName()+"写完了"+key);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    // 取数据
    public void get(String key) {
        try {
            System.out.println(Thread.currentThread().getName()+"正在取操作"+key);
            // 暂停一会
            TimeUnit.MICROSECONDS.sleep(300);
            // 放数据
            map.get(key);
            System.out.println(Thread.currentThread().getName()+"取完了"+key);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class ReadWriteLockTest {

    public static void main(String[] args) {
        MyCache myCache = new MyCache();
        for (int i = 1; i <= 6; i++) {
            final int num = i;
            new Thread(()->{
                    myCache.put(String.valueOf(num),String.valueOf(num));
            },String.valueOf(i)).start();
        }

        for (int i = 1; i <= 6; i++) {
            final int num = i;
            new Thread(()->{
                myCache.get(String.valueOf(num));
            },String.valueOf(i)).start();
        }
    }
}
```

输出结果如下：

> 1 正在写操作 1  
> 4 正在写操作 4  
> 3 正在写操作 3  
> 2 正在写操作 2  
> 5 正在写操作 5  
> 6 正在写操作 6  
> 1 正在取操作 1  
> 2 正在取操作 2  
> 3 正在取操作 3  
> 4 正在取操作 4  
> 5 正在取操作 5  
> 6 正在取操作 6  
> 2 取完了 2  
> 2 写完了 2  
> 5 写完了 5  
> 5 取完了 5  
> 6 取完了 6  
> 3 写完了 3  
> 4 写完了 4  
> 1 写完了 1  
> 6 写完了 6  
> 4 取完了 4  
> 3 取完了 3  
> 1 取完了 1

很显然，线程在写操作的时候，有线程在读操作，这可能会出现脏数据

加上读写锁：

```java
class MyCache{
    // 需要模仿从Map中取对象，所以先穿件一个map对象
    private volatile Map<String, Object> map = new HashMap<>();

    // 创建读写锁
    private ReadWriteLock rwlock = new ReentrantReadWriteLock();

    // 放数据
    public void put(String key, Object value) {
        // 添加写锁
        rwlock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName()+"正在写操作"+key);
            // 暂停一会
            TimeUnit.MICROSECONDS.sleep(300);
            // 放数据
            map.put(key, value);
            System.out.println(Thread.currentThread().getName()+"写完了"+key);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            // 释放写锁
            rwlock.writeLock().unlock();
        }
    }

    // 取数据
    public void get(String key) {
        // 添加读锁
        rwlock.readLock().lock();;
        try {
            System.out.println(Thread.currentThread().getName()+"正在取操作"+key);
            // 暂停一会
            TimeUnit.MICROSECONDS.sleep(300);
            // 放数据
            map.get(key);
            System.out.println(Thread.currentThread().getName()+"取完了"+key);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            // 释放读锁
            rwlock.readLock().unlock();
        }
    }
}
```

> 得到的结果为：
> 
> 2 正在写操作 2  
> 2 写完了 2  
> 1 正在写操作 1  
> 1 写完了 1  
> 3 正在写操作 3  
> 3 写完了 3  
> 4 正在写操作 4  
> 4 写完了 4  
> 5 正在写操作 5  
> 5 写完了 5  
> 6 正在写操作 6  
> 6 写完了 6  
> 1 正在取操作 1  
> 2 正在取操作 2  
> 5 正在取操作 5  
> 3 正在取操作 3  
> 6 正在取操作 6  
> 4 正在取操作 4  
> 1 取完了 1  
> 3 取完了 3  
> 2 取完了 2  
> 4 取完了 4  
> 6 取完了 6  
> 5 取完了 5

### 9.4 读写锁的演变

无锁：多线程抢夺资源

synchronized 和 ReentrantLock，都是独占，每次只可以一个操作，不能共享

**ReentrantReadWriteLock，**读读可以共享，提升性能，但是不能多人写。缺点：造成死锁（一直读，不能写），读进程不能写，写进程可以读。

写锁降级为读锁（一般等级写锁高于读锁）

具体第四步演练的代码:

获取写锁 -> 获取读锁 -> 释放写锁 -> 释放读锁

```java
//演示读写锁降级
public class Demo1 {
    public static void main(String[] args) {
        //可重入读写锁对象
        ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();
        ReentrantReadWriteLock.ReadLock readLock = rwLock.readLock();//读锁
        ReentrantReadWriteLock.WriteLock writeLock = rwLock.writeLock();//写锁
        //锁降级
        //1 获取写锁
        writeLock.lock();
        System.out.println("---write");
        //2 获取读锁
        readLock.lock();
        System.out.println("---read");
        //3 释放写锁
        writeLock.unlock();
        //4 释放读锁
        readLock.unlock();
    }
}
```

**倘若先读再写，由于读写锁的特点，在读锁后不允许写，所以会被阻塞**

### 9.5 锁降级的必要性

锁降级中读锁的获取是否必要呢？答案是必要的。

**我们在使用读写锁时遵守下面的获取规则**

> 1. 如果有一个线程已经占用了读锁，则此时其他线程如果要申请读锁，可以申请成功。
> 
> 2. 如果有一个线程已经占用了读锁，则此时其他线程如果要申请写锁，则申请写锁的线程会一直等待释放读锁，因为读写不能同时操作。
> 
> 3. 如果有一个线程已经占用了写锁，则此时其他线程如果申请写锁或者读锁，都必须等待之前的线程释放写锁，同样也因为读写不能同时，并且两个线程不应该同时写

主要是为了保证数据的可见性，如果当前线程不获取读锁而是直接释放写锁， 假设此刻另一个线程（记作线程 T）获取了写锁并修改了数据，那么当前线程无法感知线程 T 的数据更新。如果当前线程获取读锁，即遵循锁降级的步骤，则线程 T 将会被阻塞，直到当前线程使用数据并释放读锁之后，线程 T 才能获取写锁进行数据更新。

这时因为可能存在一个事务线程不希望自己的操作被别的线程中断，而这个事务操作可能分成多部分操作更新不同的数据（或表）甚至非常耗时。如果长时间用写锁独占，显然对于某些高响应的应用是不允许的，所以在完成部分写操作后，退而使用读锁降级，来允许响应其他进程的读操作。只有当全部事务完成后才真正释放锁。

所以总结下锁降级的意义应该就是：在一边读一边写的情况下提高性能。

![[IMG-尚硅谷-juc-高并发 note-20250805201330381.png]]

**如果是读之后再写，执行不了 因为读锁权限小于写锁 需要读完之后释放读锁，在进行写锁**

## 10 阻塞队列 BlockingQueue

### 10.1 阻塞队列概述

**阻塞队列是共享队列（多线程操作），一端输入，一端输出，不能无限放队列，满了之后就会进入阻塞，取出也同理**

> 当队列是空的，从队列中获取元素的操作将会被阻塞  
> 当队列是满的，从队列中添加元素的操作将会被阻塞  
> 试图从空的队列中获取元素的线程将会被阻塞，直到其他线程往空的队列插入新的元素  
> 试图向已满的队列中添加新元素的线程将会被阻塞，直到其他线程从队列中移除一个或多个元素或者完全清空，使队列变得空闲起来并后续新增

### 10.2 阻塞队列架构

BlockingQueue 是一个接口，它的父接口有： Collection ， Iterable ， Queue ；

![[IMG-尚硅谷-juc-高并发 note-20250805201333558.png]]

它的子接口有：BlockingDeque ， TransferQueue ；它的实现类有：ArrayBlockingQueue ， DelayQueue ， LinkedBlockingDeque ， LinkedBlockingQueue ， LinkedTransferQueue ， PriorityBlockingQueue ， SynchronousQueue

![[IMG-尚硅谷-juc-高并发 note-20250805201335901.png]]

### 10.3 阻塞队列分类

1. ArrayBlockingQueue（常用）

> 基于数组的阻塞队列
> 由数组结构组成的有界阻塞队列
> -ArrayBlockingQueue 在生产者放入数据和消费者获取数据，都是共用同一个锁对象，无法并行 2.

2. LinkedBlockingQueue（常用）

> 基于链表的阻塞队列
> 由链表结构组成的有界（但大小默认值为 Integer.MAX_VALUE）阻塞队列
> 之所以能够高效的处理并发数据，还因为其对于生产者端和消费者端分别采用了独立的锁来控制数据同步，这也意味着在高并发的情况下生产者和消费者可以并行地操作队列中的数据，以此来提高整个队列的并发性能

3. DelayQueue

> 3. 使用优先级队列实现的延迟无界阻塞队列
> DelayQueue 中的元素只有当其指定的延迟时间到了，才能够从队列中获取到该元素。
> DelayQueue 是一个没有大小限制的队列，因此往队列中插入数据的操作（生产者）永远不会被阻塞，而只有获取数据的操作（消费者）才会被阻塞

4. PriorityBlockingQueue

> 基于优先级的阻塞队列
> 支持优先级排序的无界阻塞队列
> 不会阻塞数据生产者，而只会在没有可消费的数据时，阻塞数据的消费者

5. SynchronousQueue

> 一种无缓冲的等待队列
> 相对于有缓冲的 BlockingQueue 来说，少了一个中间经销商的环节（缓冲区）
> 不存储元素的阻塞队列，也即单个元素的队列



声明一个 SynchronousQueue 有两种不同的方式，它们之间有着不太一样的行为。公平模式和非公平模式的区别:
- 公平模式：SynchronousQueue 会采用公平锁，并配合一个 FIFO 队列来阻塞，多余的生产者和消费者，从而体系整体的公平策略；
- 非公平模式（SynchronousQueue 默认）：SynchronousQueue 采用非公平锁，同时配合一个 LIFO 队列来管理多余的生产者和消费者
    

**而后一种模式，如果生产者和消费者的处理速度有差距，则很容易出现饥渴的情况，即可能有某些生产者或者是消费者的数据永远都得不到处理**

6. LinkedTransferQueue

> 由链表结构组成的无界阻塞 TransferQueue 队列
> 
> 由链表组成的无界阻塞队列

**预占模式**。 意思就是消费者线程取元素时，如果队列不为空，则直接取走数据，若队列为空，生成一个节点（节点元素为 null）入队，消费者线程被等待在这个节点上，生产者线程入队时发现有一个元素为 null 的节点，生产者线程就不入队了，直接就将元素填充到该节点，并唤醒该节点等待的线程，被唤醒的消费者线程取走元素，从调用的方法返回

7. LinkedBlockingDeque

> 由链表结构组成的双向阻塞队列
> 
> 阻塞有两种情况

**插入元素时**：如果当前队列已满将会进入阻塞状态，一直等到队列有空的位置时再该元素插入，该操作可以通过设置超时参数，超时后返回 false 表示操作失败，也可以不设置超时参数一直阻塞，中断后抛出 InterruptedException 异常  
**读取元素时**：如果当前队列为空会阻塞住直到队列不为空然后返回元素，同样可以通过设置超时参数

### 10.4 阻塞队列核心方法

|方法类型|抛出异常|特殊值|阻塞|超时|
|---|---|---|---|---|
|插入|add(e)|offer()|put(e)|offer(e,time,unit)|
|移除|remove()|poll()|take()|poll(time,unit)|
|检查|element()|peek()|-|-|

|抛出异常|当阻塞队列满时，再往队列里 add 插入元素会抛出 IllegalStateException:Queue full 当阻塞队列空时，再往队列里 remove 移除元素会抛出 NoSuchElementException|
|---|---|
|**特殊值**|插入方法，成功 true，失败 false 移除方法，成功返回出队列的元素，列表里没有就返回 null|
|**阻塞**|当阻塞队列满时，生产者线程继续往队列里 put 元素，队列会一直阻塞生产者线程知道 put 数据或响应中断退出 当阻塞队列空时，消费者线程视图从队列里 take 元素，队列会一直阻塞消费者线程直到队列可用|
|**超时**|当阻塞队列满时，队列会阻塞生产者线程一定时间，超过限时后生产者线程会退出|

**第一种使用模式**

```java
public class BlockingQueueTest {
    public static void main(String[] args) {
        // 创建阻塞队列
        BlockingQueue<Object> blockingQueue = new ArrayBlockingQueue<>(3);
        // 当队列中加元素
        System.out.println(blockingQueue.add("a"));
        System.out.println(blockingQueue.add("b"));
        System.out.println(blockingQueue.add("c"));
        // 检查
        System.out.println(blockingQueue.element());
        /** 此时输出的结果为
         * true
         * true
         * true
         * a
         * */
        System.out.println(blockingQueue.add("d"));
        // 此时再添加元素，会抛出异常:IllegalStateException: Queue full
        // 取出元素
        System.out.println(blockingQueue.remove());
        // 检查
        System.out.println(blockingQueue.element());
        /** 此时输出的结果为
         * true
         * true
         * true
         * a
         * a (取出数据)
         * b (队首数据)
         * */
    }
}
```

综述

```java
// 加入元素,成功为true，失败为false
System.out.println(blockingQueue.add("a"));
// 检查元素
System.out.println(blockingQueue.element());
// 取出元素,先进先出
System.out.println(blockingQueue.remove());
```

**第二种方法：**

```java
System.out.println(blockingQueue.offer("a"));
// 检查元素
System.out.println(blockingQueue.element());
// 取出元素,先进先出
System.out.println(blockingQueue.poll());
```

**第三种方法：**

```java
System.out.println(blockingQueue.put("a"));
// 检查元素
System.out.println(blockingQueue.element());
// 取出元素,先进先出
System.out.println(blockingQueue.take());
```

该方法加入元素或者取出元素，如果满了或者空了，还进行下一步加入或者取出操作，会出现阻塞的状态，而第一二种方法是直接抛出异常

**第四种方法：**

加入元素 System.out.println (blockingQueue.offer (“a”));

该方法满了或者空了在进行会有阻塞，但可以加入参数，超时退出

```java
System.out.println(blockingQueue.offer("w",3L, TimeUnit.SECONDS));
```

## 11 线程池 Executor

### 11.1 线程池概述

连接池是创建和管理一个连接的缓冲池的技术，这些连接准备好被任何需要它们的线程使用

线程池（英语：thread pool）一种线程使用模式。线程过多会带来调度开销，进而影响缓存局部性和整体性能。而线程池维护着多个线程，等待着监督管理者分配可并发执行的任务。这避免了在处理短时间任务时创建与销毁线程的代价。线程池不仅能够保证内核的充分利用，还能防止过分调度

线程池的优势： 线程池做的工作只要是控制运行的线程数量，处理过程中将任务放入队列，然后在线程创建后启动这些任务，如果线程数量超过了最大数量，超过数量的线程排队等候，等其他线程执行完毕，再从队列中取出任务来执行。

线程池的特点：

降低资源消耗：通过重复利用已创建的线程降低线程创建和销毁造成的销耗。  
提高响应速度：当任务到达时，任务可以不需要等待线程创建就能立即执行。  
提高线程的可管理性：线程是稀缺资源，如果无限制的创建，不仅会销耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

### 11.2 线程池架构

Java 中的线程池是通过 Executor 框架实现的，该框架中用到了 Executor，Executors，ExecutorService，ThreadPoolExecutor 这几个类

**![[IMG-尚硅谷-juc-高并发 note-20250805201338553.png]]**

说明：Executors 为工具类，I 为接口类，C 为实现类

### 11.3 线程池使用方式

Executors.newFixedThreadPool (int)：一池 N 线程

```java
ExecutorService threadPool1 = Executors.newFixedThreadPool(5); //5个窗口
```

Executors.newSingleThreadExecutor ()：一池一线程

```java
ExecutorService threadPool2 = Executors.newSingleThreadExecutor(); //一个窗口
```

Executors.newCachedThreadPool ()：一池可扩容根据需求创建线程

```java
 ExecutorService threadPool3 = Executors.newCachedThreadPool();
```

**执行线程**：execute()  
关闭线程：shutdown ()

void execute (Runnable command); 参数为 Runnable 接口类，可以通过设置 lambda

**具体案例代码案例**

```java
public class ThreadPoolTest {
    public static void main(String[] args) {
        ExecutorService threadPool1 = Executors.newFixedThreadPool(5);
        ExecutorService threadPool2 = Executors.newSingleThreadExecutor();
        ExecutorService threadPool3 = Executors.newCachedThreadPool();

        // 是个顾客请求
        try{
            for (int i = 1; i <= 10; i++) {
                // 到此时执行execute()方法才创建线程
                threadPool2.execute(()->{
                    System.out.println(Thread.currentThread().getName()+" 办理业务");
                });
            }
        }finally {
            // 关闭线程
            threadPool1.shutdown();
        }
    }
}
```

### 11.4 线程池底层原则

通过查看上面三种方式创建对象的类源代码  
都有 `new ThreadPoolExecutor` 具体查看该类的源代码，涉及七个参数

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) {
    if (corePoolSize < 0 ||
        maximumPoolSize <= 0 ||
        maximumPoolSize < corePoolSize ||
        keepAliveTime < 0)
        throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
        throw new NullPointerException();
    this.acc = System.getSecurityManager() == null ?
        null :
    AccessController.getContext();
    // 常驻线程数量（核心）
    this.corePoolSize = corePoolSize;
    // 最大线程数量
    this.maximumPoolSize = maximumPoolSize;
    // 阻塞队列（排队的线程放入）
    this.workQueue = workQueue;
    // 线程存活时间
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    // 线程工厂，用于创建线程 线程工厂，用于创建线程
    this.threadFactory = threadFactory;
    // 拒绝测试（线程满了
    this.handler = handler;
}
```

### 11.5 线程池的七个参数

具体代码中的七个参数讲解：

int corePoolSize，常驻线程数量（核心）

int maximumPoolSize，最大线程数量

long keepAliveTime,TimeUnit unit，线程存活时间

BlockingQueue workQueue，阻塞队列（排队的线程放入）

ThreadFactory threadFactory，线程工厂，用于创建线程

RejectedExecutionHandler handler，拒绝测试（线程满了）

具体工作流程是：

1. 在执行创建对象的时候不会创建线程，创建线程的时候 execute (）才会创建
2. 先到常驻线程，满了之后再到阻塞队列进行等待，阻塞队列满了之后，在往外扩容线程，扩容线程不能大于最大线程数。大于最大线程数和阻塞队列之和后，会执行拒绝策略。
3. 阻塞队列为 3，常驻线程数 2，最大线程数 5

### 11.6 线程池底层工作流程

![[IMG-尚硅谷-juc-高并发 note-20250805201341393.png]]

**对流程图的解释**

现在假设来了 9 个线程，在执行 execute () 方法才创建线程。

第 1-2 个线程进入线程池创建

第 3-5 个线程进入阻塞队列

第 6-8 个线程会为他们创建新线程执行（直接运行线程 6 而非线程 3）

第 9 个线程会被拒绝

总结来说：先到常驻线程，满了之后再到阻塞队列进行等待，阻塞队列满了之后，在往外扩容线程，扩容线程不能大于最大线程数。大于最大线程数和阻塞队列之和后，会执行拒绝策略。

**具体的拒绝策略有：**

1. 抛异常 - AbortPolicy (默认)：直接抛出 RejectedExecutionException 异常阻止系统正常运行
2. 谁调用找谁 - CallerRunsPolicy：“调用者运行” 一种调节机制，该策略既不会抛弃任务，也不会抛出异常，而是将某些任务回退到调用者，从而降低新任务的流量
3. 抛弃最久执行当前 - DiscardOldestPolicy：抛弃队列中等待最久的任务，然后把当前任务加入队列中，尝试再次提交当前任务
4. 不理不问 - Policydiscard：该策略默默地丢弃无法处理的任务，不予任何处理也不抱出异常。如果允许任务丢失，这是最好的一种策略

### 11.7 自定义线程池

**实际在开发中不允许使用 Executors 创建，而是通过 ThreadPoolExecutor 的方式**，规避资源耗尽风险

说明：Executors 返回的线程池对象的弊端如下：

1）FixedThreadPool 和 SingleThreadPool:

允许的请求队列长度为 Integer.MAX_VALUE，可能会堆积大量的请求，从而导致 OOM。

2）CachedThreadPool 和 ScheduledThreadPool:

允许的创建线程数量为 Integer.MAX_VALUE，可能会创建大量的线程，从而导致 OOM。

```java
ExecutorService threadPool = new ThreadPoolExecutor(
    // 常驻核心线程
        2,
    // 最大线程数量
        5,
    // 线程存活时间
        2L,
        TimeUnit.SECONDS,
    // 阻塞队列
        new ArrayBlockingQueue<>(3),
    // 线程工厂
        Executors.defaultThreadFactory(),
    // 拒绝策略
        new ThreadPoolExecutor.AbortPolicy()
);
```

**其他都同理，只是调用 ThreadPoolExecutor 类，自定义参数**

```java
public class ThreadPoolTest {
    public static void main(String[] args) {
        // 组定义线程池
        ExecutorService threadPool = new ThreadPoolExecutor(
                // 常驻线程数量（核心）2个
                2,
                // 最大线程数量5个
                5,
                // 线程存活时间:2秒
                2L,
                TimeUnit.SECONDS,
                // 阻塞队列
                new ArrayBlockingQueue<>(3),
                // 默认线程工厂
                Executors.defaultThreadFactory(),
                // 拒绝策略。抛出异常
                new ThreadPoolExecutor.AbortPolicy()
        );

        try{
            for (int i = 1; i <= 8; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName()+" 办理业务");
                });
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            // 关闭线程池
            threadPool.shutdown();
        }
    }
}
```

输出结果:

pool-1-thread-1 办理业务  
pool-1-thread-4 办理业务  
pool-1-thread-3 办理业务  
pool-1-thread-2 办理业务  
pool-1-thread-3 办理业务  
pool-1-thread-4 办理业务  
pool-1-thread-1 办理业务  
pool-1-thread-5 办理业务

如果线程数大于 **最 大 线 程 数 量** **+ 阻 塞 队 列 容 量** 则 抛出异常

## 12 Fork 与 Join 分支

将一个大的任务拆分成多个子任务进行并行处理，最后将子任务结果合并成最后的计算结果。

该算法相当于递归，且是`二分查找`思路

```java
class Fibonacci extends RecursiveTask<Integer> {
   final int n;
   Fibonacci(int n) { this.n = n; }
   Integer compute() {
     if (n <= 1)
        return n;
     Fibonacci f1 = new Fibonacci(n - 1);
     // 分支
     f1.fork();
     Fibonacci f2 = new Fibonacci(n - 2);
     // 合并
     return f2.compute() + f1.join();
   }
 }
```

> Fork：把一个复杂任务进行分拆，大事化小
> 
> Join：把分拆任务的结果进行合并

ForkJoinTask：我们要使用 Fork/Join 框架，首先需要创建一个 ForkJoin 任务。该类提供了在任务中执行 fork 和 join 的机制。通常情况下我们不需要直接集成 ForkJoinTask 类，只需要继承它的子类，Fork/Join 框架提供了两个子类：

RecursiveAction：用于没有返回结果的任务

RecursiveTask：用于有返回结果的任务

ForkJoinPool：ForkJoinTask 需要通过 ForkJoinPool 来执行

RecursiveTask：继承后可以实现递归 (自己调自己) 调用的任务

创建分支合并对象 通过该对象调用内部方法

**具体案例：**1 加到 100，相加两个数值不能大于 10

```java
class MyTask extends RecursiveTask<Integer> {

    // 拆分差值不能超过10
    private static final Integer VALUE  = 10;
    private int begin;
    private int end;
    private int result;

    public MyTask(int begin, int end){
        this.begin = begin;
        this.end = end;
    }

    @Override
    protected Integer compute() {
        // 判断两个值的差值是否大于10
        if((end-begin)<=10) {
            // 相加操作
            for (int i = begin; i <= end ; i++) {
                result = result + i;
            }
        } else {
            // 大于10 继续拆分
            int middle = (begin + end) / 2;
            // 拆分左边
            MyTask task01 = new MyTask(begin, middle);
            // 拆分右边
            MyTask task02 = new MyTask(middle + 1, end);
            // 调用方法拆分
            task01.fork();
            task02.fork();
            // 合并结果
            result = task01.join() + task02.join();
        }
        return result;
    }
}

public class ForkJoinTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 创建MyTask对象
        MyTask task = new MyTask(0, 100);
        // 创建分支合并池对象
        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkJoinTask<Integer> forkJoinTask = forkJoinPool.submit(task);

        // 获取最终合并之后结果
        Integer reslut = forkJoinTask.get();
        System.out.println(reslut);
    }
}
```

输出结果： 5050

## 13 异步回调

同步：指等待资源（阻塞）

异步：指设立哨兵，资源空闲通知线程，否则该线程去做其他事情（非阻塞）

### 13.1 CompletableFuture

CompletableFuture 在 Java 里面被用于异步编程，异步通常意味着非阻塞，可以使得我们的任务单独运行在与主线程分离的其他线程中，并且通过回调可以在主线程中得到异步任务的执行状态，是否完成，和是否异常等信息


CompletableFuture 实现了 Future, CompletionStage 接口，实现了 Future 接口就可以兼容现在有线程池框架，而 CompletionStage 接口才是异步编程的接口抽象，里面定义多种异步方法，通过这两者集合，从而打造出了强大的 CompletableFuture 类：

- 异步调用没有返回值方法 runAsync
- 异步调用有返回值方法 supplyAsync

**主线程调用 get 方法会阻塞**

```java
public class CompletableFutureTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 异步调用没有返回值
        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(()->{
            System.out.println(Thread.currentThread().getName()+" : CompletableFuture");
        });
        completableFuture.get();

        // 异步调用
        // mq消息队列
        CompletableFuture<Integer> completableFuture1 = CompletableFuture.supplyAsync(()->{
            System.out.println(Thread.currentThread().getName()+" : CompletableFuture1");
            // 模拟异常
            int i = 10/0;
            return 1024;
        });
        // 完成之后调用
        completableFuture1.whenComplete((t,u)->{
            System.out.println("-----t:"+t);    // 方法的返回值
            System.out.println("-----u:"+u);    // 异常的返回信息
        }).get();
    }
}
```

**具体 whenComplete 的源代码为：**

t 为返回结果，u 为异常信息

```java
public CompletableFuture<T> whenComplete(
    BiConsumer<? super T, ? super Throwable> action) {
    return uniWhenCompleteStage(null, action);
}
```

### 13.2 Future 与 CompletableFuture

对比这两种方法，一个为同步一个为异步

Futrue 在 Java 里面，通常用来表示一个异步任务的引用，比如我们将任务提交到线程池里面，然后我们会得到一个 Futrue，在 Future 里面有 isDone 方法来 判断任务是否处理结束，还有 get 方法可以一直阻塞直到任务结束然后获取结果，但整体来说这种方式，还是同步的，因为需要客户端不断阻塞等待或者不断轮询才能知道任务是否完成

1. **不支持手动完成**： 我提交了一个任务，但是执行太慢了，我通过其他路径已经获取到了任务结果，现在没法把这个任务结果通知到正在执行的线程，所以必须主动取消或者一直等待它执行完成
2. **不支持进一步的非阻塞调用**： 通过 Future 的 get 方法会一直阻塞到任务完成，但是想在获取任务之后执行额外的任务，因为 Future 不支持回调函数，所以无法实现这个功能
3. **不支持链式调用**： 对于 Future 的执行结果，我们想继续传到下一个 Future 处理使用，从而形成一个链式的 pipline 调用，这在 Future 中是没法实现的。
4. **不支持多个 Future 合并**： 比如我们有 10 个 Future 并行执行，我们想在所有的 Future 运行完毕之后，执行某些函数，是没法通过 Future 实现的。
5. **不支持异常处理：**Future 的 API 没有任何的异常处理的 api，所以在异步运行时，如果出了问题是不好定位的  
    的。
6. **不支持异常处理：**Future 的 API 没有任何的异常处理的 api，所以在异步运行时，如果出了问题是不好定位的