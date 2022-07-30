---
title: kubernetes介绍
excerpt: 所在模块：k8s
tags: [操作系统]
categories: 后端面试
banner_img: /img/壁纸.jpg
hide: true
---

### 1、Docker与Kubernetes

Docker是一个容器运行时的实现，Kubernetes依赖于某种容器运行时的实现。

### 2、Pod

Kubernetes中最基本的调度单位是Pod，Pod从属于Node（物理机或虚拟机），Pod中可以运行多个Docker容器，会共享 PID、IPC、Network 和 UTS namespace。Pod在创建时会被分配一个IP地址，Pod间的容器可以互相通信。

### 3、Yaml

Kubernetes中有着很多概念，它们都算做是一种对象，如Pod、Deployment、Service等，都可以通过一个yaml文件来进行描述，并可以对这些对象进行CRUD操作（对应REST中的各种HTTP方法）。

下面一个Pod的yaml文件示例：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-nginx-app
  labels:
    app: my-nginx-app
spec:
  containers:
  - name: nginx
    image: nginx:1.7.9
    ports:
    - containerPort: 80
```

kind：对象的类别

metadata：元数据，如Pod的名称，以及标签Label【用于识别一系列关联的Pod，可以使用 Label Selector 来选择一组相同 label 的对象】

spec：希望Pod能达到的状态，在此体现了Kubernetes的声明式的思想，我们只需要定义出期望达到的状态，而不需要关心如何达到这个状态，这部分工作由Kubernetes来完成。这里我们定义了Pod中运行的容器列表，包括一个nginx容器，该容器对外暴露了80端口。

### 4、Node

Node 是 Pod 真正运行的主机，可以是物理机，也可以是虚拟机。为了管理 Pod，每个 Node 节点上至少要运行 container runtime、`kubelet` 和 `kube-proxy` 服务。

### 5、Deployment

Deployment用于管理一个无状态应用，对应一个Pod的集群，每个Pod的地位是对等的，对Deployment来说只是用于维护一定数量的Pod，这些Pod有着相同的Pod模板。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-nginx-app
  template:
    metadata:
      labels:
        app: my-nginx-app
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

可以对Deployment进行部署、升级、扩缩容等操作。

### 6、Service

Service用于将一组Pod暴露为一个服务。

在 kubernetes 中，Pod 的 IP 地址会随着 Pod 的重启而变化，并不建议直接拿 Pod 的 IP 来交互。那如何来访问这些 Pod 提供的服务呢？使用 Service。Service 为一组 Pod（通过 labels 来选择）提供一个统一的入口，并为它们提供负载均衡和自动服务发现。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx-app
  labels:
    name: my-nginx-app
spec:
  type: NodePort      #这里代表是NodePort类型的
  ports:
  - port: 80          # 这里的端口和clusterIP(10.97.114.36)对应，即10.97.114.36:80,供内部访问。
    targetPort: 80    # 端口一定要和container暴露出来的端口对应
    protocol: TCP
    nodePort: 32143   # 每个Node会开启，此端口供外部调用。
  selector:
    app: my-nginx-app
```

### 7、Kubernetes组件

- etcd 保存了整个集群的状态；
- apiserver 提供了资源操作的唯一入口，并提供认证、授权、访问控制、API 注册和发现等机制；
- controller manager 负责维护集群的状态，比如故障检测、自动扩展、滚动更新等；
- scheduler 负责资源的调度，按照预定的调度策略将 Pod 调度到相应的机器上；
- kubelet 负责维护容器的生命周期，同时也负责 Volume（CVI）和网络（CNI）的管理；
- Container runtime 负责镜像管理以及 Pod 和容器的真正运行（CRI）；
- kube-proxy 负责为 Service 提供 cluster 内部的服务发现和负载均衡

参考文章：https://www.servicemesher.com/blog/kubernetes-crd-quick-start/
