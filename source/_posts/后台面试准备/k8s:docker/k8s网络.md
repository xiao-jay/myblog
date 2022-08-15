---
title: k8s网络
excerpt: 所在模块：k8s
tags: [k8s]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

#### docker网络

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h50depznptj20s50e13zh.jpg)

docker使用网桥模式，通过虚拟化出网卡来，不同容器之间通信是通过docker0网桥转发。

每个容器里面都有个etho，然后docker0有个对应的veth。

#### k8s不同node是怎么进行通信的

k8s不同node通信也是和
