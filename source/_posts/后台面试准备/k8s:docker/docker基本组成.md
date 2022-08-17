---
title: docker组成
excerpt: 所在模块：k8s
tags: [k8s]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

## docker基础组成

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h59qurl3dsj21ao0p3gpe.jpg)

命名空间（Namespaces） 、控制组（Control Groups）和联合文件系统（Union File System）为底层提供的实现，Docker将其封装，开发者并不直接操作。在Docker中，另外提供出了一些软件层面的概念，是操作Docker所针对的对象。

它们分别是：**镜像（Image**）、**容器**（**Container**）、**网络**（**Network**）、**数据卷**（**Volume**）。

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h59qwhlezwj20zk0dm74h.jpg)

### docker和vm的区别

VM是一个运行在宿主机之上的完整的操作系统，VM运行自身操作系统会占用较多的CPU、内存、硬盘资源。Docker不同于VM，只包含应用程序以及依赖库，基于libcontainer运行在宿主机上，并处于一个隔离的环境中，这使得Docker更加轻量高效，启动容器只需几秒钟之内完成。由于Docker轻量、资源占用少，使得Docker可以轻易的应用到构建标准化的应用中。但Docker目前还不够完善，比如隔离效果不如VM，共享宿主机操作系统的一些基础库等；网络配置功能相对简单，主要以桥接方式为主；查看日志也不够方便灵活。

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h59quy8wlvj21c80u0afi.jpg)
