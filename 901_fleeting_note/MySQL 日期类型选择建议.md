我们平时开发中不可避免的就是要存储时间，比如我们要记录操作表中这条记录的时间、记录转账的交易时间、记录出发时间、用户下单时间等等。你会发现时间这个东西与我们开发的联系还是非常紧密的，用的好与不好会给我们的业务甚至功能带来很大的影响。所以，我们有必要重新出发，好好认识一下这个东西。

## [不要用字符串存储日期](https://javaguide.cn/database/mysql/some-thoughts-on-database-storage-time.html#%E4%B8%8D%E8%A6%81%E7%94%A8%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%AD%98%E5%82%A8%E6%97%A5%E6%9C%9F)

和绝大部分对数据库不太了解的新手一样，我在大学的时候就这样干过，甚至认为这样是一个不错的表示日期的方法。毕竟简单直白，容易上手。

但是，这是不正确的做法，主要会有下面两个问题：

1. 字符串占用的空间更大！
2. 字符串存储的日期效率比较低（逐个字符进行比对），无法用日期相关的 API 进行计算和比较。

## [Datetime 和 Timestamp 之间的抉择](https://javaguide.cn/database/mysql/some-thoughts-on-database-storage-time.html#datetime-%E5%92%8C-timestamp-%E4%B9%8B%E9%97%B4%E7%9A%84%E6%8A%89%E6%8B%A9)

Datetime 和 Timestamp 是 MySQL 提供的两种比较相似的保存时间的数据类型，可以精确到秒。他们两者究竟该如何选择呢？

下面我们来简单对比一下二者。

### [时区信息](https://javaguide.cn/database/mysql/some-thoughts-on-database-storage-time.html#%E6%97%B6%E5%8C%BA%E4%BF%A1%E6%81%AF)

**DateTime 类型是没有时区信息的（时区无关）** ，DateTime 类型保存的时间都是当前会话所设置的时区对应的时间。这样就会有什么问题呢？当你的时区更换之后，比如你的服务器更换地址或者更换客户端连接时区设置的话，就会导致你从数据库中读出的时间错误。

**Timestamp 和时区有关**。Timestamp 类型字段的值会随着服务器时区的变化而变化，自动换算成相应的时间，说简单点就是在不同时区，查询到同一个条记录此字段的值会不一样。

下面实际演示一下！

建表 SQL 语句：

```
CREATE TABLE `time_zone_test` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `date_time` datetime DEFAULT NULL,
  `time_stamp` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

插入数据：

```
INSERT INTO time_zone_test(date_time,time_stamp) VALUES(NOW(),NOW());
```

查看数据：

```
select date_time,time_stamp from time_zone_test;
```

结果：

```
+---------------------+---------------------+
| date_time           | time_stamp          |
+---------------------+---------------------+
| 2020-01-11 09:53:32 | 2020-01-11 09:53:32 |
+---------------------+---------------------+
```

现在我们运行

修改当前会话的时区:

```
set time_zone='+8:00';
```

再次查看数据：

```
+---------------------+---------------------+
| date_time           | time_stamp          |
+---------------------+---------------------+
| 2020-01-11 09:53:32 | 2020-01-11 17:53:32 |
+---------------------+---------------------+
```

**扩展：一些关于 MySQL 时区设置的一个常用 sql 命令**

```
# 查看当前会话时区
SELECT @@session.time_zone;
# 设置当前会话时区
SET time_zone = 'Europe/Helsinki';
SET time_zone = "+00:00";
# 数据库全局时区设置
SELECT @@global.time_zone;
# 设置全局时区
SET GLOBAL time_zone = '+8:00';
SET GLOBAL time_zone = 'Europe/Helsinki';
```

### [占用空间](https://javaguide.cn/database/mysql/some-thoughts-on-database-storage-time.html#%E5%8D%A0%E7%94%A8%E7%A9%BA%E9%97%B4)

下图是 MySQL 日期类型所占的存储空间（官方文档传送门：[https://dev.mysql.com/doc/refman/8.0/en/storage-requirements.html](https://dev.mysql.com/doc/refman/8.0/en/storage-requirements.html)）：

![[IMG-MySQL 日期类型选择建议-20250214082030427.png]]

在 MySQL 5.6.4 之前，DateTime 和 Timestamp 的存储空间是固定的，分别为 8 字节和 4 字节。但是从 MySQL 5.6.4 开始，它们的存储空间会根据毫秒精度的不同而变化，DateTime 的范围是 5~8 字节，Timestamp 的范围是 4~7 字节。

### [表示范围](https://javaguide.cn/database/mysql/some-thoughts-on-database-storage-time.html#%E8%A1%A8%E7%A4%BA%E8%8C%83%E5%9B%B4)

Timestamp 表示的时间范围更小，只能到 2038 年：

- DateTime：1000-01-01 00:00:00.000000 ~ 9999-12-31 23:59:59.499999
- Timestamp：1970-01-01 00:00:01.000000 ~ 2038-01-19 03:14:07.499999

### [性能](https://javaguide.cn/database/mysql/some-thoughts-on-database-storage-time.html#%E6%80%A7%E8%83%BD)

由于 TIMESTAMP 需要根据时区进行转换，所以从毫秒数转换到 TIMESTAMP 时，不仅要调用一个简单的函数，还要调用操作系统底层的系统函数。这个系统函数为了保证操作系统时区的一致性，需要进行加锁操作，这就降低了效率。

DATETIME 不涉及时区转换，所以不会有这个问题。

为了避免 TIMESTAMP 的时区转换问题，建议使用指定的时区，而不是依赖于操作系统时区。

## [数值时间戳是更好的选择吗？](https://javaguide.cn/database/mysql/some-thoughts-on-database-storage-time.html#%E6%95%B0%E5%80%BC%E6%97%B6%E9%97%B4%E6%88%B3%E6%98%AF%E6%9B%B4%E5%A5%BD%E7%9A%84%E9%80%89%E6%8B%A9%E5%90%97)

很多时候，我们也会使用 int 或者 bigint 类型的数值也就是数值时间戳来表示时间。

这种存储方式的具有 Timestamp 类型的所具有一些优点，并且使用它的进行日期排序以及对比等操作的效率会更高，跨系统也很方便，毕竟只是存放的数值。缺点也很明显，就是数据的可读性太差了，你无法直观的看到具体时间。

时间戳的定义如下：

> 时间戳的定义是从一个基准时间开始算起，这个基准时间是「1970-1-1 00:00:00 +0:00」，从这个时间开始，用整数表示，以秒计时，随着时间的流逝这个时间整数不断增加。这样一来，我只需要一个数值，就可以完美地表示时间了，而且这个数值是一个绝对数值，即无论的身处地球的任何角落，这个表示时间的时间戳，都是一样的，生成的数值都是一样的，并且没有时区的概念，所以在系统的中时间的传输中，都不需要进行额外的转换了，只有在显示给用户的时候，才转换为字符串格式的本地时间。

数据库中实际操作：

```
mysql> select UNIX_TIMESTAMP('2020-01-11 09:53:32');
+---------------------------------------+
| UNIX_TIMESTAMP('2020-01-11 09:53:32') |
+---------------------------------------+
|                            1578707612 |
+---------------------------------------+
1 row in set (0.00 sec)

mysql> select FROM_UNIXTIME(1578707612);
+---------------------------+
| FROM_UNIXTIME(1578707612) |
+---------------------------+
| 2020-01-11 09:53:32       |
+---------------------------+
1 row in set (0.01 sec)
```

## [总结](https://javaguide.cn/database/mysql/some-thoughts-on-database-storage-time.html#%E6%80%BB%E7%BB%93)

MySQL 中时间到底怎么存储才好？Datetime?Timestamp? 还是数值时间戳？

并没有一个银弹，很多程序员会觉得数值型时间戳是真的好，效率又高还各种兼容，但是很多人又觉得它表现的不够直观。

《高性能 MySQL 》这本神书的作者就是推荐 Timestamp，原因是数值表示时间不够直观。下面是原文：

![[IMG-MySQL 日期类型选择建议-20250222193846487.png]]

每种方式都有各自的优势，根据实际场景选择最合适的才是王道。下面再对这三种方式做一个简单的对比，以供大家实际开发中选择正确的存放时间的数据类型：

| 类型        | 存储空间   | 日期格式                           | 日期范围                                                        | 是否带时区信息 |
| --------- | ------ | ------------------------------ | ----------------------------------------------------------- | ------- |
| DATETIME  | 5~8 字节 | YYYY-MM-DD hh:mm:ss[.fraction] | 1000-01-01 00:00:00[.000000] ～ 9999-12-31 23:59:59[.999999] | 否       |
| TIMESTAMP | 4~7 字节 | YYYY-MM-DD hh:mm:ss[.fraction] | 1970-01-01 00:00:01[.000000] ～ 2038-01-19 03:14:07[.999999] | 是       |
| 数值型时间戳    | 4 字节   | 全数字如 1578707612                | 1970-01-01 00:00:01 之后的时间                                   | 否       |