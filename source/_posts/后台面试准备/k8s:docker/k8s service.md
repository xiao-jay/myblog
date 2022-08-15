---
title: k8s service
excerpt: 所在模块：k8s
tags: [k8s]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

### service的负载均衡机制

从服务ip到后端pod的负载均衡机制，是由每个node上面的kube-proxy实现的



#### kube-proxy 代理模式

##### iptables

这种模式，`kube-proxy` 会监视 Kubernetes 控制节点对 Service 对象和 Endpoints 对象的添加和移除。 对每个 Service，它会配置 iptables 规则，从而捕获到达该 Service 的 `clusterIP` 和端口的请求，进而将请求重定向到 Service 的一组后端中的某个 Pod 上面。 对于每个 Endpoints 对象，它也会配置 iptables 规则，这个规则会选择一个后端组合。

默认的策略是，kube-proxy 在 iptables 模式下随机选择一个后端。

使用 iptables 处理流量具有较低的系统开销，因为流量由 Linux netfilter 处理， 而无需在用户空间和内核空间之间切换。 这种方法也可能更可靠。

如果 kube-proxy 在 iptables 模式下运行，并且所选的第一个 Pod 没有响应，则连接失败。 这与用户空间模式不同：在这种情况下，kube-proxy 将检测到与第一个 Pod 的连接已失败， 并会自动使用其他后端 Pod 重试。

你可以使用 Pod [就绪探测器](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/pod-lifecycle/#container-probes) 验证后端 Pod 可以正常工作，以便 iptables 模式下的 kube-proxy 仅看到测试正常的后端。 这样做意味着你避免将流量通过 kube-proxy 发送到已知已失败的 Pod。

![iptables 代理模式下 Service 概览图](https://d33wubrfki0l68.cloudfront.net/27b2978647a8d7bdc2a96b213f0c0d3242ef9ce0/e8c9b/images/docs/services-iptables-overview.svg)

### IPVS 代理模式

**特性状态：** `Kubernetes v1.11 [stable]`

在 `ipvs` 模式下，kube-proxy 监视 Kubernetes 服务和端点，调用 `netlink` 接口相应地创建 IPVS 规则， 并定期将 IPVS 规则与 Kubernetes 服务和端点同步。该控制循环可确保 IPVS 状态与所需状态匹配。访问服务时，IPVS 将流量定向到后端 Pod 之一。

IPVS 代理模式基于类似于 iptables 模式的 netfilter 挂钩函数， 但是使用哈希表作为基础数据结构，并且在内核空间中工作。 这意味着，与 iptables 模式下的 kube-proxy 相比，IPVS 模式下的 kube-proxy 重定向通信的延迟要短，并且在同步代理规则时具有更好的性能。 与其他代理模式相比，IPVS 模式还支持更高的网络流量吞吐量。

IPVS 提供了更多选项来平衡后端 Pod 的流量。这些是：

- `rr`：轮替（Round-Robin）
- `lc`：最少链接（Least Connection），即打开链接数量最少者优先
- `dh`：目标地址哈希（Destination Hashing）
- `sh`：源地址哈希（Source Hashing）
- `sed`：最短预期延迟（Shortest Expected Delay）
- `nq`：从不排队（Never Queue）

##### ipvs





服务发现：

通过DNS来做
