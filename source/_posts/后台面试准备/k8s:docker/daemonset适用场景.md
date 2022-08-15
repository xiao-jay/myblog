---
title: daemonset适用场景
excerpt: 所在模块：k8s
tags: [k8s]
categories: 后端面试
banner_img: /img/壁纸.jpg
---



1. 在集群的每个节点上运行存储 Daemon，比如 glusterd 或 ceph。
2. 在每个节点上运行日志收集 Daemon，比如 flunentd 或 logstash。
3. 在每个节点上运行监控 Daemon，比如 Prometheus Node Exporter 或 collectd。
