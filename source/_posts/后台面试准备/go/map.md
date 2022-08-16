---
title: map
excerpt: 所在模块：golang
tags: [golang]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

## map是怎么实现的

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

map分为hmap和bmap，hmap是一个哈希表，bmap是一个数组，golang用链表法解决哈希冲突，如果溢出会弄一个溢出bucket放进去。



bmap 是存放 k-v 的地方，我们把视角拉近，仔细看 bmap 的内部组成。

```
type bmap struct {
    topbits  [8]uint8
    keys     [8]keytype
    values   [8]valuetype
    pad      uintptr
    overflow uintptr
}
```

![img](https://user-images.githubusercontent.com/7698088/57577391-f88f1d80-74a7-11e9-893c-4783dc4fb35e.png)



### 创建map

从语法层面上来说，创建 map 很简单：

```
ageMp := make(map[string]int)

// 指定 map 长度

ageMp := make(map[string]int, 8)
// ageMp 为 nil，不能向其添加元素，会直接panic

var ageMp map[string]int
```

通过汇编语言可以看到，实际上底层调用的是 `makemap` 函数，主要做的工作就是初始化 `hmap` 结构体的各种字段，例如计算 B 的大小，设置哈希种子 hash0 等等。

    func makemap(t *maptype, hint int64, h *hmap, bucket unsafe.Pointer) *hmap {
    
     // 省略各种条件检查...
    // 找到一个 B，使得 map 的装载因子在正常范围内
    
    B := uint8(0)
    
    for ; overLoadFactor(hint, B); B++ {
    
    }
    // 初始化 hash table
    
    // 如果 B 等于 0，那么 buckets 就会在赋值的时候再分配
    
    // 如果长度比较大，分配内存会花费长一点
    
    buckets := bucket
    
    var extra *mapextra
    
    if B != 0 {
    
        var nextOverflow *bmap
    
        buckets, nextOverflow = makeBucketArray(t, B)
    
        if nextOverflow != nil {
    
            extra = new(mapextra)
    
            extra.nextOverflow = nextOverflow
    
        }
    
    }
    // 初始化 hamp
    
    if h == nil {
    
        h = (*hmap)(newobject(t.hmap))
    
    }
    
    h.count = 0
    
    h.B = B
    
    h.extra = extra
    
    h.flags = 0
    
    h.hash0 = fastrand()
    
    h.buckets = buckets
    
    h.oldbuckets = nil
    
    h.nevacuate = 0
    
    h.noverflow = 0
    return h
    }



## key 定位过程

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h58uhkib28j20u01270vw.jpg)

key 经过哈希计算后得到哈希值，共 64 个 bit 位（64位机，32位机就不讨论了，现在主流都是64位机），计算它到底要落在哪个桶时，只会用到最后 B 个 bit 位。还记得前面提到过的 B 吗？如果 B = 5，那么桶的数量，也就是 buckets 数组的长度是 2^5 = 32。

例如，现在有一个 key 经过哈希函数计算后，得到的哈希结果是：

 10010111 | 000011110110110010001111001010100010010110010101010 │ 01010

用最后的 5 个 bit 位，也就是 `01010`，值为 10，也就是 10 号桶。这个操作实际上就是取余操作，但是取余开销太大，所以代码实现上用的位操作代替。

再用哈希值的高 8 位，找到此 key 在 bucket 中的位置，这是在寻找已有的 key。最开始桶内还没有 key，新加入的 key 会找到第一个空位，放入。

buckets 编号就是桶编号，当两个不同的 key 落在同一个桶中，也就是发生了哈希冲突。冲突的解决手段是用链表法：在 bucket 中，从前往后找到第一个空位。这样，在查找某个 key 时，先找到对应的桶，再去遍历 bucket 中的 key。



### map扩容过程是怎么样的



需要一个指标来判断是否要扩容，这就是`装载因子`。Go 源码里这样定义 `装载因子`：

```
loadFactor := count / (2^B)
```

count 就是 map 的元素个数，2^B 表示 bucket 数量。

再来说触发 map 扩容的时机：在向 map 插入新 key 的时候，会进行条件检测，符合下面这 2 个条件，就会触发扩容：

1. 装载因子超过阈值，源码里定义的阈值是 6.5。
2. overflow 的 bucket 数量过多：当 B 小于 15，也就是 bucket 总数 2^B 小于 2^15 时，如果 overflow 的 bucket 数量超过 2^B；当 B >= 15，也就是 bucket 总数 2^B 大于等于 2^15，如果 overflow 的 bucket 数量超过 2^15。

```
// src/runtime/hashmap.go/mapassign

// 触发扩容时机
if !h.growing() && (overLoadFactor(int64(h.count), h.B) || tooManyOverflowBuckets(h.noverflow, h.B)) {
        hashGrow(t, h)
    }

// 装载因子超过 6.5
func overLoadFactor(count int64, B uint8) bool {
    return count >= bucketCnt && float32(count) >= loadFactor*float32((uint64(1)<<B))
}

// overflow buckets 太多
func tooManyOverflowBuckets(noverflow uint16, B uint8) bool {
    if B < 16 {
        return noverflow >= uint16(1)<<B
    }
    return noverflow >= 1<<15
}
```



再来看看真正执行搬迁工作的 growWork() 函数。

```
func growWork(t *maptype, h *hmap, bucket uintptr) {
    // 确认搬迁老的 bucket 对应正在使用的 bucket
    evacuate(t, h, bucket&h.oldbucketmask())

    // 再搬迁一个 bucket，以加快搬迁进程
    if h.growing() {
        evacuate(t, h, h.nevacuate)
    }
}
```

h.growing() 函数非常简单：

```
func (h *hmap) growing() bool {

    return h.oldbuckets != nil

}
```

如果 `oldbuckets` 不为空，说明还没有搬迁完毕，还得继续搬。



TODO:evacuate函数干了什么下次看懂源码再写，一个搬迁的细节是搬到大内存去的时候得重新hash计算放到哪个bucket中，因为比原来多了一位。



## 问题

### 1、为什么map不可以寻址，map是怎么存储的

1. 如果字典的元素不存在，则返回零值，而零值是不可变对象，如果能寻址问题就大了。
2. 而如果字典的元素存在，考虑到 Go 中 map 实现中元素的地址是变化的，这意味着寻址的结果也是无意义的。





参考网站：https://qcrao91.gitbook.io/go/map/map-de-di-ceng-shi-xian-yuan-li-shi-shi-mo
