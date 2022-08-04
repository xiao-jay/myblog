---
title: Mysql redo undo bin log
excerpt: 所在模块：数据库
tags: [数据库]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

### redo log

重做日志

　　作用：确保事务的持久性。防止在发生故障的时间点，尚有脏页未写入磁盘，（刷脏页是个随机IO），在重启 mysql 服务的时候，根据 redo log 进行重做，从而达到事务的持久性这一特性。

### bin log

归档日志（二进制日志）

　　作用：用于复制，在主从复制中，从库利用主库上的 binlog 进行重播，实现主从同步。 用于数据库的基于时间点的还原。

　　内容：逻辑格式的日志，可以简单认为就是执行过的事务中的 sql 语句。但又不完全是 sql 语句这么简单，而是包括了执行的 sql 语句（增删改）反向的信息，也就意味着 delete 对应着 delete 本身和其反向的 insert；update 对应着 update 执行前后的版本的信息；insert 对应着 delete 和 insert 本身的信息。

　　binlog 有三种模式：Statement（基于 SQL 语句的复制）、Row（基于行的复制） 以及 Mixed（混合模式）

### undo log

回滚日志

　　作用：保存了事务发生之前的数据的一个版本，可以用于回滚，同时可以提供多版本并发控制下的读（MVCC），也即非锁定读

　　内容：逻辑格式的日志，在执行 undo 的时候，仅仅是将数据从逻辑上恢复至事务之前的状态，而不是从物理页面上操作实现的，这一点是不同于 redo log 的。



### redo log 和 undo log 的区别

undo 日志用于记录事务开始前的状态，用于事务失败时的回滚操作；redo 日志记录事务执行后的状态，用来恢复未写入 data file 的已成功事务更新的数据。例如某一事务的事务序号为 T1，其对数据 X 进行修改，设 X 的原值是 0，修改后的值为 1，那么 Undo 日志为 <T1, X, 0>，Redo 日志为 < T1, X, 1>。



### **redo log与binlog的区别**

我们知道，在MySQL中还存在binlog(二进制日志)也可以记录写操作并用于数据的恢复，但二者是有着根本的不同的：

（1）作用不同：redo log是用于crash recovery的，保证MySQL宕机也不会影响持久性；binlog是用于point-in-time recovery的，保证服务器可以基于时间点恢复数据，此外binlog还用于主从复制。

（2）层次不同：redo log是InnoDB存储引擎实现的，而binlog是MySQL的服务器层(可以参考文章前面对MySQL逻辑架构的介绍)实现的，同时支持InnoDB和其他存储引擎。

（3）内容不同：redo log是物理日志，内容基于磁盘的Page；binlog的内容是二进制的，根据binlog_format参数的不同，可能基于sql语句、基于数据本身或者二者的混合。

（4）写入时机不同：binlog在事务提交时写入；redo log的写入时机相对多元：

![](https://img-blog.csdnimg.cn/20200509170444156.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3h4MTIzNjk4,size_16,color_FFFFFF,t_70)

前面曾提到：当事务提交时会调用fsync对redo log进行刷盘；这是默认情况下的策略，修改innodb_flush_log_at_trx_commit参数可以改变该策略，但事务的持久性将无法保证。
除了事务提交时，还有其他刷盘时机：如master thread每秒刷盘一次redo log等，这样的好处是不一定要等到commit时刷盘，commit速度大大加快。
