---
title: 为什么string不可变
excerpt: 所在模块：golang
tags: [golang]
categories: 后端面试
banner_img: /img/壁纸.jpg
---



## 为什么string不可变



## 总结

- Go 语言中的字符串和其他高级语言（Java、C#）一样，默认是不可变的（immutable）。
- 字符串不可变有很多好处：
  - 如天生线程安全，大家使用的都是只读对象，无须加锁；
  - 再者，方便内存共享，而不必使用写时复制（Copy On Write）等技术；
  - 字符串 hash 值也只需要制作一份。
- 修改字符串时，可以将字符串转换为 []byte 进行修改。
- []byte 和 string 可以通过强制类型转换互转。

这样做的好处是 string 变得非常轻量，可以很方便的进行传递而不用担心内存拷贝（这也避免了内存带来的诸多问题)

GO 中的 string 类型一般是指向字符串字面量

字符串字面量存储位置是在虚拟内存分区的只读段上面，而不是堆或栈上

因此，GO 的 string 类型不可修改的

