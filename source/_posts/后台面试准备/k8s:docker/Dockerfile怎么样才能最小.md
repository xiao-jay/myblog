---
title: Dockerfile怎么样才能最小
excerpt: 所在模块：k8s
tags: [k8s]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

Dockerfile怎么样才能最小

1、每一层尽可能的多做事情，只有一层layer能够做到尽可能少的打包内容，但是管理成本很大，无法复用和使用本地cache

2、选择基础镜像的时候选的小一点

3、编译和运行分开，使用from来传输编译好的数据，使用rebuild





Dockerfile最下面那层添加代码，倒数第二层删除代码，有什么不好

1、每次打包docker时,都会使用资源打包无意义的东西

2、打包会导致源代码泄漏，造成很严重的安全事故
