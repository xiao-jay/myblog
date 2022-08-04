---
title: redis 持久化
excerpt: 所在模块：数据库
tags: [数据库]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

## redis 持久化

### redis持久化的概念：

利用永久性存储介质将数据进行保存，在特定的时间将保存的数据进行恢复的工作机制叫做持久化。
持久化是为了保障数据安全的。

### 持久化保存的是什么？

那么持久化保存的是什么？
Redis持久化有两种形式：
1.快照
2.记录过程

快照方式就是我们所说的RDB,记录过程的方式就是AOF。

RDB(Redis DataBase):
RDB是将数据写入一个临时文件，持久化结束后，用这个临时文件替换上次持久化的文件，达到数据恢复。

AOF（append only file):
AOF是将执行过的指令记录下来，数据恢复时按照从前到后的顺序再将指令执行一遍，实现数据恢复。

## RDB

### RDB的启动方式：

save指令。作用就是执行一次保存操作。

![在这里插入图片描述](https://img.inotgo.com/imagesLocal/202203/05/202203050529489737_0.jpg)
通过使用save命令。我们就可以看到生成了一个rdb文件：
![在这里插入图片描述](https://img.inotgo.com/imagesLocal/202203/05/202203050529489737_5.jpg)
rdb中的文件是二进制文件。我们是无法打开的。

**当然我们也可以进行默认触发，就是将此命令写入到conf中。**

#### save指令相关配置：

我们使用了RDB生成了文件。接下来我们看看save指令的相关配置：

- dbfilename dump.rdb:
  设置本地数据库文件名，默认值为dump.rdb，通常设置为dump-端口号.rdb
- dir：
  设置存储.rdb文件的路径，通常设置成存储空间较大的目录中，目录名称为data
- rdbcompression yes:
  设置存储至本地数据库时是否压缩数据
  默认为yes，采用LZF压缩。通常设置为开启状态，如果设置为no，可以节省cpu运行时间，但是会使存储的文件变大（巨大）
- rdbchecksum yes
  设置是否进行RDB文件格式校验，改校验过程在写文件和读文件过程均进行
  通常默认设置为开启状态，如果设置为no，可以节约读写行过程约10%时间消耗，但是存储一定的数据损坏风险。

**这些命令在conf文件中添加即可生效。**

#### save指令的工作原理

save指令是单线程任务执行序列 ，进行一个一个执行的。当我们执行save指令过长的时候，save指令的执行会阻塞当前的Redis服务器。**线上的环境不建议使用**。

#### bgsave指令：

那么如何处理数据量过大，单线程执行方式造成效率过得问题？
我们可以通过后台指令执行。通过bgsave指令来执行。

当我们执行bgsave指令的时候，它会给redis发送一个消息。之后进行返回消息.他不会立即执行，而是通过fork生成子进程来进行创建rdb文件。做完之后会返回一个消息。

![在这里插入图片描述](https://img.inotgo.com/imagesLocal/202203/05/202203050529489737_1.jpg)
我们在log中也可以看到这句话：
![在这里插入图片描述](https://img.inotgo.com/imagesLocal/202203/05/202203050529489737_3.jpg)

bgsave命令式针对save阻塞问题做的优化。Redis内部所有涉及到RDB操作都**建议采用bgsave的方式**，**save命令放弃使用**。

### save配置方式

RDB的启动方式有两种，一种是通过命令的方式。还有一种就是通过写入到conf配置文件中。
命令如下：

```java
save second changes
```

second:监控时间范围。
changes:监控key的变化量
这个的作用就是限定时间范围内key的变化数量达到指定数量及进行持久化操作。

例如：

```java
save 200 10：表示200 秒内如果至少有 10 个 key 的值变化，则保存
```

注意：默认save配置使用的是bgsave操作。

#### 总结：

启动方式
![在这里插入图片描述](https://img.inotgo.com/imagesLocal/202203/05/202203050529489737_2.jpg)
还有一些其他的特殊形式来进行启动save：

- 全量复制
- debug reload 重启过程中
- shutdown save 关闭服务器

RDB优点：

- 压缩的二进制文件，存储效率高
- 内部存储的是redis在某个时间点的快照，非常适合用于数据备份，全量复制场景。
- RDB恢复数据的速度比AOF块
- 应用：服务器每隔x小时执行bgsave备份，并将RDB文件拷贝到远程机器中，用于灾难恢复。

RDB缺点：

- RDB 无法做到实时持久化
- bgsave指令每次运行执行fork操作，牺牲性能
- Redis多版本RDB文件格式版本未统一，有可能版本无法兼容。

## AOF

AOF对RDB的一些弊端进行了解决。

- 快照思想是读写全部数据，而AOF仅仅记录操作过程
- 对所有操作进行记录，排除丢失数据的风险
- **解决了数据持久化的实时性**

由于上面的许多优点，AOF已经成为了主流的持久化方式，因此，**我们在使用持久化的时候，最好选择AOF.**

我们看一下AOF写入的过程：
![在这里插入图片描述](https://img.inotgo.com/imagesLocal/202203/05/202203050529489737_4.jpg)
写数据的三种策略：

- always:每次写入操作珺同步到AOF文件中，性能差
- everysec：每秒将缓冲区中的指令同步到AOF文件中，数据准确性高。系统宕机丢失一秒的数据。
- no：由操作系统控制每次同步到AOF文件的生命周期，整体过程无法控制。

从上面三种来看，丢失一秒的数据是我们可容忍的，性能还可以。所以**建议选择everysec**。

#### AOF开启

将 redis.conf 的 appendonly 配置改为 yes 即可

```java
appendonly yes|no
appendfsync always|everysec| no
```

#### AOF改名

appendfilename ：aof文件名，默认是"appendonly.aof"

#### AOF 重写

随着命令不断写入AOF,文件会越来越大，为了解决这个问题，Redis引入了AOF重写机制压缩文件体积。AOF文件重写是将Redis进程内的数据转化为写命令同步到新的AOF文件的过程。
简单来说就是将对同一个数据的若干跳命令执行结果转化成最终结果数据对应的指令进行记录。

我们将AOF重写之后主要有三点作用：

- 降低磁盘占用量，提高效率
- 提高IO
- 降低数据恢复效率

AOF重写规则：

- 进程内一超市的数据不再写入文件
- 忽略无效指令，例如：del key1 hdel key2,srem key3 set key4 111 set key4 222.
- 对同一个数据的多条命令合并为一条命令
  如：

```java
lpush list1 a
lpush list1 b
lpush list1 c
```

可以合并为一条

```java
lpush list1 a b c
```

#### AOF重写方式

手动重写：bgrewriteof
![在这里插入图片描述](https://img.inotgo.com/imagesLocal/202203/05/202203050529489737_6.jpg)

自动重写：

auto-aof-rewrite-percentage :默认值为100。aof自动重写配置，当目前aof文件大小超过上一次重写的aof文件大小的百分之多少进行重写，即当aof文件增长到一定大小的时候，Redis能够调用bgrewriteaof对日志文件进行重写。当前AOF文件大小是上次日志重写得到AOF文件大小的二倍（设置为100）时，自动启动新的日志重写过程。
auto-aof-rewrite-min-size：64mb:设置允许重写的最小aof文件大小

自动重写触发对比参数：（运行指令info persistence获取信息）

```java
aof_current_size
aof_base_size
```

自动重写触发条件：

```java
aof_current_size >auto-aof-rewrite-min-size
aof_current_size-aof_base_size/aof_base_size >= auto-aof-rewrite-percentage
```

## RDB与AOF区别

![在这里插入图片描述](https://img.inotgo.com/imagesLocal/202203/05/202203050529489737_8.jpg)

如何选择：

- 对数据非常敏感，建议使用AOF
  -AOF持久化策略使用everysecond,每秒钟fsync一次.该策略redis仍可以保持很好的处理性能。出现问题的时候，最多丢失0-1秒内的数据。
  -aof文件存储体积较大，恢复速度较慢
- 数据呈现阶段有效性，建议使用RDB持久化 ,比如游戏宕机了，数据恢复到昨天。数据可以良好的做到阶段内无丢失，恢复速度较快。但是rdb持久化使redis性能降低

数据敏感：AOF
追求大数据的恢复速度rdb
灾难恢复rdb
双保险RDB AOF同时。

