---
title: docker file怎么打最小
excerpt: 所在模块：k8s
tags: [k8s]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

1、把所有文件，尽可能多的操作打包成一个docker file

2、多次打包过程，第一步拉去依赖build二进制，第二步是通过from copy二进制然后进行第二步操作。

