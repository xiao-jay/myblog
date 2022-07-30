---
title: kafka和redis作为消息队列的差别
excerpt: 所在模块：操作系统
tags: [操作系统]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

## kafka和redis作为消息队列的差别

1、需要使用多消费组情况的时候redis做不了，redis的list只能支持一个消费者pop，kafka可以使用多个group重复消费一个数据。

2、当订阅者断开重连会丢失断开期间发布者发布的消息，而kafka中会记录每个消费者消费的topic的offset，因此kafka可以从断开的offset继续消费。

3、高并发情况的下的list无法通过扩容解决问题，kafka可以扩partition。

4、没有确认机制，每当pop了之后消息就永远从list删除，消费者如果消费失败就永远不能找回这条消息了

5、redis断电无法保证数据完全不丢，kakfa不会，kafka可靠
