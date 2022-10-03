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

如容器网络模型一样，对于同一主机上的 pod 之间，通过 docker0 网桥设备直接二层（数据链路层）网络上通过 MAC 地址直接通信：
![image](https://img2020.cnblogs.com/blog/1279547/202111/1279547-20211117161812245-887946032.gif)

而跨主机的 pod 之间的相互通信也主要有以下两个思路：

1. 修改底层网络设备配置，加入容器网络 IP 地址的管理，修改路由器网关等，该方式主要和 SDN(Software define networking) 结合。
2. 完全不修改底层网络设备配置，复用原有的 underlay 网络平面，解决容器跨主机通信，主要有如下两种方式：

- **隧道传输(overlay)**：将容器的数据包封装到原主机网络的三层或者四层数据包中，然后使用主机网络的 IP 或者 TCP/UDP 传输到目标主机，目标主机拆包后再转发给目标容器。overlay 隧道传输常见方案包括 Vxlan、ipip 等，目前使用 overlay 隧道传输技术的主流容器网络有 Flannel 等；
  ![image](https://img2020.cnblogs.com/blog/1279547/202111/1279547-20211117162101660-1777563905.gif)
- **修改主机路由**：把容器网络加到主机路由表中，把主机网络设备当作容器网关，通过路由规则转发到指定的主机，实现容器的三层互通。目前通过路由技术实现容器跨主机通信的网络如 Flannel host-gw、Calico 等；
  ![image](https://img2020.cnblogs.com/blog/1279547/202111/1279547-20211117162134919-1612114891.gif)

下面简单介绍几种主流的方案：

- **Flannel **是目前使用最为普遍的方案，提供了多种网络后端，它支持多种数据路径，也适合于 overlay/underlay 等多种场景。对于 overlay 的数据包封装，可以使用用户态的 UDP，内核态的 Vxlan(性能相对较好)，甚至在集群规模不大，且处于同一个二层域时可以采用 host-gw 的方式修改主机路由表；
- **Weave **工作模式与 Flannel 很相似的，它最早只提供了 UDP（称为 sleeve 模式）的网络方式，后来又加上了 fastpass 方式（基于 VxLAN），不过 Weave 消除了 Flannel 中用来存储网络地址的额外组件，自己集成了高可用的数据存储功能；
- **Calico ** 主要是采用了修改主机路由，节点之间采用 BGP 的协议去进行路由的同步，但是现实中的网络并不总是支持 BGP 路由的，因此 Calico 也支持内核中的 IPIP 模式，使用 overlay 的方式来传输数据；

可以看到，同一个 pod 里面的其他容器共享 pause 容器创建的网络命名空间，也就是说，所有的容器共享相同的网络设备、路由表设置、服务端口等信息，仿佛是在同一台机器上运行的不同进程，所以这些容器之间可以直接通过 localhost + port 通信；对于集群外部的请求，则通过 docker0 网桥设备充当的网关，同时通过 iptables 做地址转换。到这里我们就会发现，这其实就是对单个容器的 bridge 网络模型的扩展。
