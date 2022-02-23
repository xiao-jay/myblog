---
title: GMP
---



1、什么是GMP？

G是goroutine,M是内核线程的抽象，P是逻辑处理器，提供执行一个go语言代码片段所需要的资源，内存分配状态，任务队列（G），存储了所有需要它来调度的G，可以用户使用gomaxprocs来设置。就是讲golang怎么从cup到实现goroutine

![](http://hkctfsys.com/img/GMP.jfif)

2、如果某个M陷入阻塞如何处理？

当一个os线程由于io操作而陷入阻塞，P会带其中的G链接到其他M中去，如果M恢复了，会从别的M上拿过来一个P，把原先跑在上面的G0放到p的队列里面，如果找不到P，就把运行在其上面的G0放到全局global runqueue里面。

3、如果有的M较忙，有的M较闲？

链接M的P中gorouitne做完了，会去global runqueue中要G，如果没有会从别的M里面要G，一般是一个进程的一半Goroutine。

4、如果一个G的运行时间过长，导致队列中后续G无法运行？

会专门创建一个线程sysmon，用于监控和管理，在内部是一个循环，记录所有P的G任务计数schedtick，如果一个P的schedtick一直不增加说明一个G一直在运行，如果运行时间太长（>10ms）就给这个goroutine打标记，下一次遇到这个就直接放到总队列的最后

5、一个G由于调用被中断，此后如何恢复？

终端你的时候把在寄存器中的栈信息重新赋值给G，等下一次运行把保存的信息复制到栈里面就可。