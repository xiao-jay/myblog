---
title: HashMap
excerpt: 所在模块：java
tags: [java]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

## HashMap

定义：HashMap是我们常见的一种数据结构，实现Map接口，用来存储键值对,允许null键/值、非同步、不保证有序(比如插入的顺序)。那HashMap中最核心的部分就是哈希函数，又称散列函数。也就是说，哈希函数是通过把key的hash值映射到数组中的一个位置来进行访问。

#### 初始容量

 static final int DEFAULT_INITIAL_CAPACITY = 1<<4;

#### 最大容量

 MAXIMUM_CAPACITY = 1<<30;

#### 扩容条件

DEFAULT_LOAD_FACTOR = 0.75f;  在容量3/4时扩容



### hash算法

```
static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```

![](https://upload-images.jianshu.io/upload_images/16390890-8018e0b694b486d7.png?imageMogr2/auto-orient/strip|imageView2/2/w/586/format/webp)

一个hashCode的前16位和他的后16为进行异或，将算出来的值和（n-1）&一下就是要插入的槽位。
