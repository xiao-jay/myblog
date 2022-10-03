---
title: select原理
excerpt: 所在模块：golang
tags: [golang]
categories: 后端面试
banner_img: /img/壁纸.jpg
---



select这个语句底层实现实际上主要由两部分组成：`case语句`和`执行函数`。
源码地址为：/go/src/runtime/select.go

每个case语句，单独抽象出以下结构体：

```
type scase struct {
    c           *hchan         // chan
    elem        unsafe.Pointer // 读或者写的缓冲区地址
    kind        uint16   //case语句的类型，是default、传值写数据(channel <-) 还是  取值读数据(<- channel)
    pc          uintptr // race pc (for race detector / msan)
    releasetime int64
}
```

结构体可以用下图表示：

![img](https://i6448038.github.io/img/select_priciple/select-principle_3.png)
其中比较关键的是：`hchan`，它是channel的指针。
在一个select中，所有的case语句会构成一个`scase`结构体的数组。

![img](https://i6448038.github.io/img/select_priciple/select-principle_4.png)

然后执行select语句实际上就是调用`func selectgo(cas0 *scase, order0 *uint16, ncases int) (int, bool)`函数。

![img](https://i6448038.github.io/img/select_priciple/select-principle_5.png)

`func selectgo(cas0 *scase, order0 *uint16, ncases int) (int, bool)`函数参数：

- cas0 为上文提到的case语句抽象出的结构体`scase`数组的第一个元素地址
- order0为一个两倍cas0数组长度的buffer，保存scase随机序列pollorder和scase中channel地址序列lockorder。
- nncases表示`scase`数组的长度

`selectgo`返回所选scase的索引(该索引与其各自的select {recv，send，default}调用的序号位置相匹配)。此外，如果选择的scase是接收操作(recv)，则返回是否接收到值。

谁负责调用`func selectgo(cas0 *scase, order0 *uint16, ncases int) (int, bool)`函数呢？

在`/reflect/value.go`中有个`func rselect([]runtimeSelect) (chosen int, recvOK bool)`函数，此函数的实现在`/runtime/select.go`文件中的`func reflect_rselect(cases []runtimeSelect) (int, bool)`函数中:

```
func reflect_rselect(cases []runtimeSelect) (int, bool) { 
    //如果cases语句为空，则阻塞当前groutine
    if len(cases) == 0 {
        block()
    }
    //实例化case的结构体
    sel := make([]scase, len(cases))
    order := make([]uint16, 2*len(cases))
    for i := range cases {
        rc := &cases[i]
        switch rc.dir {
        case selectDefault:
            sel[i] = scase{kind: caseDefault}
        case selectSend:
            sel[i] = scase{kind: caseSend, c: rc.ch, elem: rc.val}
        case selectRecv:
            sel[i] = scase{kind: caseRecv, c: rc.ch, elem: rc.val}
        }
        if raceenabled || msanenabled {
            selectsetpc(&sel[i])
        }
    }
    return selectgo(&sel[0], &order[0], len(cases))
}
```

那谁调用的`func rselect([]runtimeSelect) (chosen int, recvOK bool)`呢？
在`/refect/value.go`中，有一个`func Select(cases []SelectCase) (chosen int, recv Value, recvOK bool)`的函数，其调用了`rselect`函数，并将最终Go中select语句的返回值的返回。

以上这三个函数的调用栈按顺序如下：

- `func Select(cases []SelectCase) (chosen int, recv Value, recvOK bool)`
- `func rselect([]runtimeSelect) (chosen int, recvOK bool)`
- `func selectgo(cas0 *scase, order0 *uint16, ncases int) (int, bool)`

这仨函数中无论是返回值还是参数都大同小异，可以简单粗暴的认为：函数参数传入的是case语句，返回值返回被选中的case语句。
那谁调用了`func Select(cases []SelectCase) (chosen int, recv Value, recvOK bool)`呢？
可以简单的认为是系统了。
来个简单的图：
![img](https://i6448038.github.io/img/select_priciple/select-principle_18.png)

前两个函数`Select`和`rselect`都是做了简单的初始化参数，调用下一个函数的操作。select真正的核心功能，是在最后一个函数`func selectgo(cas0 *scase, order0 *uint16, ncases int) (int, bool)`中实现的。

### selectgo函数做了什么

打乱传入的case结构体顺序

![img](https://i6448038.github.io/img/select_priciple/select-principle_6.png)

锁住其中的所有的channel
![img](https://i6448038.github.io/img/select_priciple/select-principle_7.png)

遍历所有的channel，查看其是否可读或者可写

![img](https://i6448038.github.io/img/select_priciple/select-principle_8.png)

如果其中的channel可读或者可写，则解锁所有channel，并返回对应的channel数据

![img](https://i6448038.github.io/img/select_priciple/select-principle_9.png)

![img](https://i6448038.github.io/img/select_priciple/select-principle_10.png)

假如没有channel可读或者可写，但是有default语句，则同上:返回default语句对应的scase并解锁所有的channel。

![img](https://i6448038.github.io/img/select_priciple/select-principle_11.png)

假如既没有channel可读或者可写，也没有default语句，则将当前运行的groutine阻塞，并加入到当前所有channel的等待队列中去。

![img](https://i6448038.github.io/img/select_priciple/select-principle_12.png)

然后解锁所有channel，等待被唤醒。
![img](https://i6448038.github.io/img/select_priciple/select-principle_13.png)

此时如果有个channel可读或者可写ready了，则唤醒，并再次加锁所有channel，
![img](https://i6448038.github.io/img/select_priciple/select-principle_14.png)

遍历所有channel找到那个对应的channel和G，唤醒G，并将没有成功的G从所有channel的等待队列中移除。

![img](https://i6448038.github.io/img/select_priciple/select-principle_15.png)

如果对应的scase值不为空，则返回需要的值，并解锁所有channel

![img](https://i6448038.github.io/img/select_priciple/select-principle_16.png)

如果对应的scase为空，则循环此过程。

### select和channel之间的关系

在想想select和channel做了什么事儿，我觉得和多路复用是一回事儿

![img](https://i6448038.github.io/img/select_priciple/select-principle_17.png)

参考文章：https://i6448038.github.io/2019/03/23/go-select-principle/
