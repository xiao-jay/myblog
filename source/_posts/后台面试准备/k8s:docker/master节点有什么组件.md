---
title: master节点有什么组件
excerpt: 所在模块：k8s
tags: [k8s]
categories: 后端面试
banner_img: /img/壁纸.jpg
---



### master节点有什么组件

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h4s6dbhvbgj20ru0k7myt.jpg)

**api server：**负责对外提供restful的Kubernetes API服务，提供了资源操作的唯一入口，并提供认证、授权、访问控制、API 注册和发现等机制。我们操作kubectl 和其他Master组件都通过调用api server提供的rest接口实现各自的功能，如controller就是通过api server来实时监控各个资源的状态的。

**etcd：**是 Kubernetes 提供的一个高可用的键值数据库，用于保存集群所有的网络配置和资源对象的状态信息，也就是保存了整个集群的状态。数据变更都是通过api server进行的。整个kubernetes系统中一共有两个服务需要用到etcd用来协同和存储配置，分别是：
1）网络插件flannel，其它网络插件也需要用到etcd存储网络的配置信息；
2）kubernetes本身，包括各种资源对象的状态和元信息配置。

**scheduler：**监听新建pod副本信息，并通过调度算法为该pod选择一个最合适的Node节点。会检索到所有符合该pod要求的Node节点，执行pod调度逻辑。调度成功之后，会将pod信息绑定到目标节点上，同时将信息写入到etcd中。一旦绑定，就由Node上的kubelet接手pod的接下来的生命周期管理。Kubernetes目前提供了调度算法，但是同样也保留了接口，用户可以根据自己的需求定义自己的调度算法。

##### taint 和 Toleration

​	schedule来检测各个node的状态然后打上taint，可以使用toleration来容忍污点。

**controller manager：**负责维护集群的状态，比如故障检测、自动扩展、滚动更新等。每个资源一般都对应有一个控制器，这些controller通过api server实时监控各个资源的状态，controller manager就是负责管理这些控制器的。当有资源因为故障导致状态变化，controller就会尝试将系统由“现有状态”恢复到“期待状态”，保证其下每一个controller所对应的资源始终处于期望状态。比如我们通过api server创建一个pod，当这个pod创建成功后，api server的任务就算完成了。其中一个pod出现问题，controller会自动恢复创建新的pod。

### Node节点有什么组件



![](https://tva1.sinaimg.cn/large/e6c9d24ely1h4s6ogvdamj20rp0k276g.jpg)

kubelet: 会监视已分配给节点的pod，负责pod的生命周期管理，同时与Master密切协作，维护和管理该Node上面的所有容器，实现集群管理的基本功能。即Node节点通过kubelet与master组件交互，可以理解为kubelet是Master在每个Node节点上面的agent。本质上，它负责使Pod的运行状态与期望的状态一致。

kube-proxy: 是实现service的通信与负载均衡机制的重要组件，将到service的请求转发到后端的pod上。

docker-engine(docker): 是负责容器的创建和管理工作。

Pod: 最小部署单元，一个Pod 有一个或多个容器组成，容器中共享存储和网络，在同一台docker主机上运行。
