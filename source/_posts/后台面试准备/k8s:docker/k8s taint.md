---
title: taint 和 Toleration
excerpt: 所在模块：k8s
tags: [k8s]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

### taint类型

- NoSchedule：不能容忍此污点的 Pod 不会被调度到节点上；现有 Pod 不会从节点中逐出。
- PreferNoSchedule：Kubernetes 会避免将不能容忍此污点的 Pod 安排到节点上。
- NoExecute：如果 Pod 已在节点上运行，则会将该 Pod 从节点中逐出；如果尚未在节点上运行，则不会将其安排到节点上。



#### 容忍度（Toleration）

```
  tolerations:
  - key: "key1"
    operator: "Equal"
    value: "value1"
    effect: "NoSchedule" 
```

