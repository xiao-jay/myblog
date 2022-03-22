---
title: GMP
excerpt: 所在模块：golang
tags: [golang]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

1、什么是GMP？

G是goroutine,M全称是Machine内核级线程，P是逻辑处理器（Processor），提供执行一个go语言代码片段所需要的资源，内存分配状态，任务队列（G），存储了所有需要它来调度的G，可以用户使用gomaxprocs来设置。就是讲golang怎么从cup到实现goroutine

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h0h97o56kkj20pb0nnq48.jpg)

2、如果某个M陷入阻塞如何处理？

当一个os线程由于io操作而陷入阻塞，P会带其中的G链接到其他M中去，如果M恢复了，会从别的M上拿过来一个P，把原先跑在上面的G0放到p的队列里面，如果找不到P，就把运行在其上面的G0放到全局global runqueue里面。

3、如果有的M较忙，有的M较闲？

链接M的P中gorouitne做完了，会去global runqueue中要G，如果没有会从别的M里面要G，一般是一个进程的一半Goroutine。

4、如果一个G的运行时间过长，导致队列中后续G无法运行？

会专门创建一个线程sysmon，用于监控和管理，在内部是一个循环，记录所有P的G任务计数schedtick，如果一个P的schedtick一直不增加说明一个G一直在运行，如果运行时间太长（>10ms）就给这个goroutine打标记，下一次遇到这个就直接放到总队列的最后

5、一个G由于调用被中断，此后如何恢复？

终端你的时候把在寄存器中的栈信息重新赋值给G，等下一次运行把保存的信息复制到栈里面就可。

### GMP调度流程

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h0ha3u9e5fj20u00gwdha.jpg)

- - 1. 调用 go func()创建一个goroutine；
  - 2. 新创建的G优先保存在P的本地队列中，如果P的本地队列已经满了就会保存在全局的队列中；
  - 3. M需要在P的本地队列弹出一个可执行的G，如果P的本地队列为空，则先会去全局队列中获取G，如果全局队列也为空则去其他P中偷取G放到自己的P中
  - 4. G将相关参数传输给M，为M执行G做准备
  - 5. 当M执行某一个G时候如果发生了系统调用产生导致M会阻塞，如果当前P队列中有一些G，runtime会将线程M和P分离，然后再获取空闲的线程或创建一个新的内核级的线程来服务于这个P，阻塞调用完成后G被销毁将值返回；
  - 6. 销毁G，将执行结果返回
  - 7. 当M系统调用结束时候，这个M会尝试获取一个空闲的P执行，如果获取不到P，那么这个线程M变成休眠状态， 加入到空闲线程中。

## GM与GMP

#### GMP相对于GM做了哪些优化

优化点有三个

1、每个 P 有自己的本地队列，而不是所有的G操作都要经过全局的G队列，这样锁的竞争会少的多的多。而 GM 模型的性能开销大头就是锁竞争。

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h0hal3f11xj20sw0dpmxt.jpg)

2、P的本地队列平衡上，在 GMP 模型中也实现了 Work Stealing 算法，如果 P 的本地队列为空，则会从全局队列或其他 P 的本地队列中窃取可运行的 G 来运行（通常是偷一半），减少空转，提高了资源利用率。

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h0hakw745aj20gp0ja74v.jpg)

3、hand off机制当M0线程因为G1进行系统调用阻塞时，线程释放绑定的P，把P转移给其他空闲的线程M1执行，同样也是提高了资源利用率。



#### 队列和线程的优化可以做在G层和M层，为什么要加一个P层呢？

因为M层是放在内核的，我们无权修改，内核级也是用户级线程发展成熟才加入内核中。所以在M无法修改的情况下，所有的修改只能放在用户层。将队列和M绑定，由于hand off机制M会一直扩增，因此队列也需要一直扩增，那么为了使Work Stealing 能够正常进行，队列管理将会变的复杂。因此设定了P层作为中间层，进行队列管理，控制GMP数量（最大个数为P的数量）。

参考网站：（讲的特别好）http://www.9ong.com/072021/%E4%B8%80%E6%96%87%E8%AE%B2%E6%B8%85go%E8%AF%AD%E8%A8%80gmp%E8%B0%83%E5%BA%A6%E6%A8%A1%E5%9E%8B.html 