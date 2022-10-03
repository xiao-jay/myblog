---
title: channel
excerpt: 所在模块：golang
tags: [golang]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

### CSP通信模型

Communicating Sequential Processes

关于[CSP的理论](https://en.wikipedia.org/wiki/Communicating_sequential_processes)，它的精确定义其实比较复杂，不过它的核心理念用一句话就可以概括：**不要共享内存来通信；而是要用通信来共享内存**（Don’t communicate by sharing memory; share memory by communicating）。



#### 往一个关闭的channal里面接收数据能够接收到吗

```
package main

import "fmt"

func main() {
	ch := make(chan int, 1)
	ch <- 1

	close(ch)
	fmt.Println(<-ch)
	fmt.Println(<-ch)
	ch <- 1
}
```

可以，但是从channal里面写就会panic





```
type hchan struct {
  qcount   uint            // 当前队列中剩余元素个数，即len
  dataqsiz uint            // 环形队列长度，即可以存放的元素个数，cap
  buf      unsafe.Pointer  // 环形队列指针：队列缓存，头指针，环形数组实现
  elemsize uint16          // 每个元素的大小
  closed   uint32          // 关闭标志位
  elemtype *_type          // 元素类型
  sendx    uint            // 队列下标，指示元素写入时存放到队列中的位置
  recvx    uint            // 队列下标，指示元素从队列的该位置读出
  recvq    waitq           // 等待读消息的goroutine队列
  sendq    waitq           // 等待写消息的goroutine队列

  // lock protects all fields in hchan, as well as several
  // fields in sudogs blocked on this channel.
  //
  // Do not change another G's status while holding this lock
  // (in particular, do not ready a G), as this can deadlock
  // with stack shrinking.
  lock mutex              // 该锁保护hchan所有字段
}
// sending/receiving等待队列的链表实现
type waitq struct {
  first *sudog
  last  *sudog
}
```

**hchan环形队列**
hchan内部实现了一个环形队列作为缓冲区，队列的长度是创建channel时指定的。下图展示了一个可缓存6个元素的channel的示意图：

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h64pmlbaf5j20dz07fglp.jpg)

- dataqsiz指示队列长度为6，即可缓存6个元素
- buf指向队列的内存，队列中还剩余两个元素
- qcount表示队列中还有两个元素
- sendx指示后续写入的数据存储的位置，取值[0,6)
- recvx指示从该位置读取数据，取值[0,6)



## 问题

### 1、能使用无缓存的channal做mutex吗

虽然没有缓存，但是可以有一个放入，多个放出，放出可以抽象成争抢锁

1、设置一个goroutine，专门来放入，因为没有拿就阻塞了，如果有人拿就退出

2、有人拿了，没有被阻塞，说明拿到锁了，开始执行任务，然后执行完的时候重新启动一个goroutine来放入锁，等待下一个拿到锁的人。
