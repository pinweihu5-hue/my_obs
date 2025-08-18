ref: [undo log 与 redo log 原理分析](https://zhuanlan.zhihu.com/p/35574452)


2013 年了解[事务日志](https://zhida.zhihu.com/search?content_id=6420091&content_type=Article&match_order=1&q=%E4%BA%8B%E5%8A%A1%E6%97%A5%E5%BF%97&zhida_source=entity)时写的总结文章，最近在研究[关系型数据库](https://zhida.zhihu.com/search?content_id=6420091&content_type=Article&match_order=1&q=%E5%85%B3%E7%B3%BB%E5%9E%8B%E6%95%B0%E6%8D%AE%E5%BA%93&zhida_source=entity)，重新 review 一下，内容不一定准确，请指正。[原文链接](https://link.zhihu.com/?target=http%3A//paperman825.m.blog.chinaunix.net/uid-20196318-id-3812190.html)

数据库通常借助日志来实现事务，常见的有 undo log、redo log，undo/redo log 都能保证事务特性，这里主要是原子性和持久性，即事务相关的操作，要么全做，要么不做，并且修改的数据能得到持久化。

假设数据库在操作时，按如下约定记录日志：

```text
1. 事务开始时，记录START T
2. 事务修改时，记录（T，x，v），说明事务T操作对象x，x的值为v 
3. 事务结束时，记录COMMIT T 
```

**undo log 原理**

[undo log](https://zhida.zhihu.com/search?content_id=6420091&content_type=Article&match_order=3&q=undo+log&zhida_source=entity) 是把所有没有 COMMIT 的事务回滚到事务开始前的状态，系统崩溃时，可能有些事务还没有 COMMIT，在系统恢复时，这些没有 COMMIT 的事务就需要借助 undo log 来进行回滚。

使用 undo log 时，要求：

```text
1. 记录修改日志时，(T，x，v）中v为x修改前的值，这样才能借助这条日志来回滚；
2. 事务提交后，必须在事务的所有修改（包括记录的修改日志）都持久化后才能写COMMIT T日志；这样才能保证，宕机恢复时，已经COMMIT的事务的所有修改都已经持久化，不需要回滚。 
```

使用 undo log 时事务执行顺序

```text
1. 记录START T 
2. 记录需要修改的记录的旧值（要求持久化）
3. 根据事务的需要更新数据库（要求持久化）
4. 记录COMMIT T 
```

使用 undo log 进行宕机回滚

```text
1. 扫描日志，找出所有已经START,还没有COMMIT的事务。
2. 针对所有未COMMIT的日志，根据undo log来进行回滚。 
```

如果数据库访问很多，日志量也会很大，宕机恢复时，回滚的工作量也就很大，为了加快回滚，可以通过 [checkpoint 机制](https://zhida.zhihu.com/search?content_id=6420091&content_type=Article&match_order=1&q=checkpoint%E6%9C%BA%E5%88%B6&zhida_source=entity)来加速回滚，

1. 在日志中记录 checkpoint_start (T1,T2…Tn)
	- (Tx 代表做 checkpoint 时，正在进行还未 COMMIT 的事务）
2. 等待所有正在进行的事务（T1~Tn）COMMIT
3. 在日志中记录 checkpoint_end

借助 checkpoint 来进行回滚
```text

从后往前，扫描undo log
1.如果先遇到checkpoint_start, 则将checkpoint_start之后的所有未提交的事务进行回滚；
2.如果先遇到checkpoint_end, 则将前一个checkpoint_start之后所有未提交的事务进行回滚；（在checkpoint的过程中，可能有很多新的事务START或者COMMIT)。 

```

使用 undo log，在写 COMMIT 日志时，要求 redo log 以及事务的所有修改都必须已经持久化，这种做法通常很影响性能。

**redo log 原理**

redo log 是指在回放日志的时候把已经 COMMIT 的事务重做一遍，对于没有 commit 的事务按照 abort 处理，不进行任何操作。

使用 redo log 时，要求：

```text
1. 记录redo log时，(T,x，v）中的v必须是x修改后的值，否则不能通过redo log来恢复已经COMMIT的事务。
2. 写COMMIT T日志之前，事务的修改不能进行持久化，否则恢复时，对于未COMMIT的操作，可能有数据已经修改，但重放redo log不会对该事务做任何处理，从而不能保证事务的原子性。 
```

使用 redo log 时事务执行顺序

```text
1. 记录START T
2. 记录事务需要修改记录的新值（要求持久化）
3. 记录COMMIT T（要求持久化）
4. 将事务相关的修改写入数据库 
```

使用 redo log 重做事务

```text
1. 扫描日志，找到所有已经COMMIT的事务；
2. 对于已经COMMIT的事务，根据redo log重做事务； 
```

在日志中使用 checkpoint

```text
1. 在日志中记录checkpoint_start (T1,T2...Tn) (Tx代表做checkpoint时，正在进行还未COMMIT的日志）
2. 将所有已提交的事务的更改进行持久化；
3. 在日志中记录checkpoint_end 
```

根据 checkpoint 来加速恢复

```text
从后往前，扫描redo log
1，如果先遇到checkpoint_start, 则把T1~Tn以及checkpoint_start之后的所有已经COMMIT的事务进行重做；
2. 如果先遇到checkpoint_end, 则T1~Tn以及前一个checkpoint_start之后所有已经COMMIT的事务进行重做； 
```

与 undo log 类似，在使用时对持久化以及事务操作顺序的要求都比较高，可以将两者结合起来使用，在恢复时，对于已经 COMMIT 的事务使用 redo log 进行重做，对于没有 COMMIT 的事务，使用 undo log 进行回滚。redo/undo log 结合起来使用时，要求同时记录操作修改前和修改后的值，如（T，x，v，w），v 为 x 修改前的值，w 为 x 修改后的值，具体操作顺序为：

```text
1. 记录START T
2. 记录修改日志（T，x，v，w）（要求持久化，其中v用于undo，w用于redo）
3. 更新数据库
4. 记录 COMMIT T 
```

4 和 3 的操作顺序没有严格要求，并且都不要求持久化；因为如果宕机时 4 已经持久化，则恢复时可通过 redo log 来重做；如果宕机时 4 未持久化，则恢复时可通过 undo log 来回滚；在处理 checkpoint 时，可采用与 redo log 相同的处理方式。