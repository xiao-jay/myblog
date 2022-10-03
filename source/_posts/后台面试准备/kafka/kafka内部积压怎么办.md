---
title: kafka内部积压
excerpt: 所在模块：kafka
tags: [kafka]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

1、kafka内部速度比较慢，rebalance（比较少）

排查分区数据发生倾斜

2、业务数据量增多

增大分区，增大并行度，排查消费者速率是否出现问题



解决消息积压可以采用下面方法。

任务重新启动后直接消费最新的消息，对于"滞后"的历史数据采用离线程序进行"补漏"。

如下面图所示。创建新的topic并配置更多数量的分区，将积压消息的topic消费者逻辑改为直接把消息打入新的topic，将消费逻辑写在新的topic的消费者中。
