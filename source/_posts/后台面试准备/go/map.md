---
title: map
excerpt: 所在模块：golang
tags: [golang]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

### map是怎么实现的

Go 语言采用的是哈希查找表，并且使用链表解决哈希冲突。

```
// A header for a Go map.
type hmap struct {
    // 元素个数，调用 len(map) 时，直接返回此值
    count     int
    flags     uint8
    // buckets 的对数 log_2
    B         uint8
    // overflow 的 bucket 近似数
    noverflow uint16
    // 计算 key 的哈希的时候会传入哈希函数
    hash0     uint32
    // 指向 buckets 数组，大小为 2^B
    // 如果元素个数为0，就为 nil
    buckets    unsafe.Pointer
    // 扩容的时候，buckets 长度会是 oldbuckets 的两倍
    oldbuckets unsafe.Pointer
    // 指示扩容进度，小于此地址的 buckets 迁移完成
    nevacuate  uintptr
    extra *mapextra // optional fields
}
```



map分为hmap和bmap，是一个哈希表，是一个数组，golang用链表法解决哈希冲突，如果溢出会弄一个溢出bucket放进去，之后迁移进去，溢出把oldbucket放进newbucket，map类型是[int64]int8，key是int64，value是int8，所以是放七个key，然后连续7个value放在同一个byte里面节省空间



## key 定位过程

key 经过哈希计算后得到哈希值，共 64 个 bit 位（64位机，32位机就不讨论了，现在主流都是64位机），计算它到底要落在哪个桶时，只会用到最后 B 个 bit 位。还记得前面提到过的 B 吗？如果 B = 5，那么桶的数量，也就是 buckets 数组的长度是 2^5 = 32。

例如，现在有一个 key 经过哈希函数计算后，得到的哈希结果是：

 10010111 | 000011110110110010001111001010100010010110010101010 │ 01010



用最后的 5 个 bit 位，也就是 `01010`，值为 10，也就是 10 号桶。这个操作实际上就是取余操作，但是取余开销太大，所以代码实现上用的位操作代替。

再用哈希值的高 8 位，找到此 key 在 bucket 中的位置，这是在寻找已有的 key。最开始桶内还没有 key，新加入的 key 会找到第一个空位，放入。

buckets 编号就是桶编号，当两个不同的 key 落在同一个桶中，也就是发生了哈希冲突。冲突的解决手段是用链表法：在 bucket 中，从前往后找到第一个空位。这样，在查找某个 key 时，先找到对应的桶，再去遍历 bucket 中的 key。

这里参考曹大 github 博客里的一张图，原图是 ascii 图，geek 味十足，可以从参考资料找到曹大的博客，推荐大家去看看。





## 问题



### 1、为什么map不可以寻址，map是怎么存储的

1. 如果字典的元素不存在，则返回零值，而零值是不可变对象，如果能寻址问题就大了。
2. 而如果字典的元素存在，考虑到 Go 中 map 实现中元素的地址是变化的，这意味着寻址的结果也是无意义的。





参考网站：https://qcrao91.gitbook.io/go/map/map-de-di-ceng-shi-xian-yuan-li-shi-shi-mo
