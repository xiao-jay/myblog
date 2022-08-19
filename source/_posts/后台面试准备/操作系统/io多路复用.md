---
title: io多路复用
excerpt: 所在模块：操作系统
tags: [操作系统]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

### io多路复用概念

​	IO多路复用是一种同步IO模型，一个线程监听多个IO事件，当有IO事件就绪时，就会通知线程去执行相应的读写操作，没有就绪事件时，就会阻塞交出cpu。多路是指网络链接，复用指的是复用同一线程。



### select

1. 用户线程调用select，将fd_set从用户空间拷贝到内核空间
2. 内核在内核空间对fd_set遍历一遍，检查是否有就绪的socket描述符，如果没有的话，就会进入休眠，直到有就绪的socket描述符
3. 内核返回select的结果给用户线程，即就绪的文件描述符数量
4. 用户拿到就绪文件描述符数量后，再次对fd_set进行遍历，找出就绪的文件描述符
5. 用户线程对就绪的文件描述符进行读写操作



优点

1. 所有平台都支持，良好的跨平台性

缺点

1. 每次调用select，都需要将fd_set从用户空间拷贝到内核空间，当fd很多时，这个开销很大
2. 最大连接数（支持的最大文件描述符数量）有限制，一般为1024
3. 每次有活跃的socket描述符时，都需要遍历一次fd_set，造成大量的时间开销，时间复杂度是O(n)
4. 将fd_set从用户空间拷贝到内核空间，内核空间也需要对fd_set遍历一遍


### poll

数据结构定义如下，**链表**存储

```c
/* Data structure describing a polling request.  */
struct pollfd
  {
    int fd;			/* File descriptor to poll.  */
    short int events;		/* Types of events poller cares about.  */
    short int revents;		/* Types of events that actually occurred.  */
  };
```




与select的异同点

相同点：

（执行过程与select类似）

1. 内核线程都需要遍历文件描述符，并且当内核返回就绪的文件描述符数量后，还需要遍历一次找出就绪的文件描述符
2. 需要将文件描述符数组或链表从用户空间拷贝到内核空间
3. 性能开销会随文件描述符的数量而线性增大

不同点：

1. select存储的数据结构是文件描述符数组，poll采用链表
2. select有最大连接数限制，poll没有最大限制，因为poll采用链表存储



### epoll

1. epoll_create创建eventpoll对象（红黑树，双链表）

2. 一棵红黑树，存储监听的所有文件描述符，并且通过epoll_ctl将文件描述符添加、删除到红黑树

3. 一个双链表，存储就绪的文件描述符列表，epoll_wait调用时，检测此链表中是否有数据，有的话直接返回

4. 所有添加到eventpoll中的事件都与设备驱动程序建立回调关系

   

缺点

1. 只能工作在linux下

优点

1. 时间复杂度为O(1)，当有事件就绪时，epoll_wait只需要检测就绪链表中有没有数据，如果有的话就直接返回
2. 不需要从用户空间到内核空间频繁拷贝文件描述符集合，使用了内存映射(mmap)技术
3. 当有就绪事件发生时采用回调的形式通知用户线程




- 应用场景
  1. 连接数较少并且都很活跃,用select和poll效率更高
  2. 连接数较多并且都不很活跃,使用epoll效率更高



参考文章：https://juejin.cn/post/6931543528971436046
