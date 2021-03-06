---
Title: 在centos上面安装k8s
---

# 在centos上面安装k8s

1、配置镜像源，在centos上面安装k8s

Kubernetes packages are not available from official CentOS 7 repositories,需要配置yum源

```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=0
EOF

// 安装kubeadm，kubectl
sudo yum install -y kubelet-1.21.0  kubeadm-1.21.0  kubectl-1.21.0
systemctl enable --now kubelet

```

**ubuntu上面安装**

```bash
# 使得 apt 支持 ssl 传输
apt-get update && apt-get install -y apt-transport-https
# 下载 gpg 密钥   这个需要root用户否则会报错
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
# 添加 k8s 镜像源 这个需要root用户否则会报错
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF
# 更新源列表
apt-get update
# 下载 kubectl，kubeadm以及 kubelet
apt-get install -y kubelet kubeadm kubectl
```

2、安装docker

```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

```
curl -sSL https://get.daocloud.io/docker | sh   
```

配置docker

```
sudo vi /etc/docker/daemon.json
```

加入：

```
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com",
    "https://quay-mirror.qiniu.com"
  ],
  "exec-opts": [ "native.cgroupdriver=systemd" ]
}
```

然后重启docker

```
sudo systemctl daemon-reload
sudo systemctl restart docker
```



3、使用swap会影响性能。kubelet禁用swap

```
 sudo vim /etc/fstab，注释掉swap那一行
sudo swapoff -a
```

4、下载所需要的镜像

```
for i in `kubeadm config images list`; do 
  imageName=${i#k8s.gcr.io/}
  docker pull registry.aliyuncs.com/google_containers/$imageName
  docker tag registry.aliyuncs.com/google_containers/$imageName k8s.gcr.io/$imageName
  docker rmi registry.aliyuncs.com/google_containers/$imageName
done;
```

5、更改kubelet参数(只有centos需要)

```
sudo vi /etc/sysconfig/kubelet
改为如下参数
KUBELET_EXTRA_ARGS=--cgroup-driver=systemd
```

6、

```
sudo kubeadm init --image-repository=registry.aliyuncs.com/google_containers --pod-network-cidr 10.244.0.0/16
```

如果安装失败，可以使用下面命令重置

```
sudo kubeadm reset
```

然后会得到token 需要保存下来

```
kubeadm join 10.0.2.203:6443 --token et430p.tuxyjzf2jl8l3nel \
        --discovery-token-ca-cert-hash sha256:3b6ced67f2eacb0ad575e1dac245a7d0ec9a00bf25e928b722eade805f1e8d18 
```

安装网络

kubectl apply -f "https://docs.projectcalico.org/manifests/calico.yaml"

## Node 节点运行

```
kubeadm join 10.0.2.203:6443 --token et430p.tuxyjzf2jl8l3nel \
        --discovery-token-ca-cert-hash sha256:3b6ced67f2eacb0ad575e1dac245a7d0ec9a00bf25e928b722eade805f1e8d18 
```

这里面有个问题，如果master和node不在一个内网，就访问不到，需要在node节点执行

```
iptables -t nat -A OUTPUT -d 192.168.0.1 -j DNAT --to-destination 152.132.125.96
```

注：第一个ip是master节点的内网ip，后一个ip是外网Ip



node 节点运行失败怎么卸载

```
rm -rf /etc/kubernetes/
```





## 安装的时候遇到的错误

#### 1、It seems like the kubelet isn‘t running or healthy

查看官网介绍为 docker 和 kubelet 服务中的 cgroup 驱动不一致，有两种方法
方式一：驱动向 docker 看齐
方式二：驱动为向 kubelet 看齐
如果docker 不方便重启则统一向 kubelet看齐，并重启对应的服务即可

**解决方式**

kublete 配置文件
grep 截取一下,可以看得出来kubelet默认 cgoup 驱动为systemd

```
root@controlplane:~# cat /var/lib/kubelet/config.yaml |grep group
cgroupDriver: systemd
```

重启kubelet （optional）

```
sudo systemctl restart kubelet
```

#### 2、msg=”getting status of runtime: rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.RuntimeService”

解决方法:

```
rm /etc/containerd/config.toml
sudo systemctl restart containerd
rm -fr  $HOME/.kube/config
```

#### 3、--apiserver-advertise-address  更改后的地址

```
sudo kubeadm init \
--apiserver-advertise-address=10.0.1.162 \
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version=v1.24.2 \
--pod-network-cidr=10.244.0.0/16 \
--service-cidr=10.96.0.0/12 
```

docker 配置

```
{
    "runtimes": {
        "nvidia": {
            "path": "nvidia-container-runtime",
            "runtimeArgs": []
        }
    },
    "registry-mirrors": ["https://m3719grz.mirror.aliyuncs.com"]
}
```

#### 4、kubelet 出现找不到Error getting node" err="node \"master\" not found

执行 crictl images 发现 pause 的 TAG 是 3.5 没有 3.2，随即执行重新拉起镜像：

```
[root@k8s-master ~]# crictl images
IMAGE                                                             TAG                 IMAGE ID            SIZE
registry.aliyuncs.com/google_containers/coredns                   v1.8.4              8d147537fb7d1       13.7MB
registry.aliyuncs.com/google_containers/coredns                   latest              8d147537fb7d1       13.7MB
registry.aliyuncs.com/google_containers/etcd                      3.5.0-0             0048118155842       99.9MB
registry.aliyuncs.com/google_containers/kube-apiserver            v1.22.1             f30469a2491a5       31.3MB
registry.aliyuncs.com/google_containers/kube-controller-manager   v1.22.1             6e002eb89a881       29.8MB
registry.aliyuncs.com/google_containers/kube-proxy                v1.22.1             36c4ebbc9d979       35.9MB
registry.aliyuncs.com/google_containers/kube-scheduler            v1.22.1             aca5ededae9c8       15MB
registry.aliyuncs.com/google_containers/pause                     3.5                 ed210e3e4a5ba       301kB
[root@k8s-master ~]# crictl pull registry.aliyuncs.com/google_containers/pause:3.2
Image is up to date for sha256:80d28bedfe5dec59da9ebf8e6260224ac9008ab5c11dbbe16ee3ba3e4439ac2c
[root@k8s-master ~]# ctr -n k8s.io i tag --force registry.aliyuncs.com/google_containers/pause:3.2 k8s.gcr.io/pause:3.2
k8s.gcr.io/pause:3.2
[root@k8s-master ~]# crictl images
IMAGE                                                             TAG                 IMAGE ID            SIZE
registry.aliyuncs.com/google_containers/coredns                   v1.8.4              8d147537fb7d1       13.7MB
registry.aliyuncs.com/google_containers/coredns                   latest              8d147537fb7d1       13.7MB
registry.aliyuncs.com/google_containers/etcd                      3.5.0-0             0048118155842       99.9MB
registry.aliyuncs.com/google_containers/kube-apiserver            v1.22.1             f30469a2491a5       31.3MB
registry.aliyuncs.com/google_containers/kube-controller-manager   v1.22.1             6e002eb89a881       29.8MB
registry.aliyuncs.com/google_containers/kube-proxy                v1.22.1             36c4ebbc9d979       35.9MB
registry.aliyuncs.com/google_containers/kube-scheduler            v1.22.1             aca5ededae9c8       15MB
registry.aliyuncs.com/google_containers/pause                     3.2                 80d28bedfe5de       300kB
k8s.gcr.io/pause                                                  3.2                 80d28bedfe5de       300kB
registry.aliyuncs.com/google_containers/pause 
```

执行kubeadm reset和init就好了

#### 5、在部署时忘记了token密码怎么办？

首先，在每个节点上 执行 kubeadm reset命令即可，重新获取token（需要重新安装flanner网络组件）
关于如何重置k8s集群可以参考：重置kubernetes集群

1.重新生成新的token
默认token的有效期为24小时，当过期之后，该token就不可用了，在master节点上执行 kubeadm token create

```
$ kubeadm token create
c9afga.w3fue0yh3gzxczic
```

查看token

```
$ kubeadm token list
TOKEN                     TTL       EXPIRES                     USAGES                   DESCRIPTION   EXTRA GROUPS
c9afga.w3fue0yh3gzxczic   23h       2019-07-26T14:30:54+08:00   authentication,signing   <none>        system:bootstrappers:kubeadm:default-node-token
```

2.获取ca证书sha256编码hash值

```
$ openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'
8b79b6461e58c07333cb2851fe74fd4374af8bbbe0bf7e040b415b86ad4fb89d
```

3.节点加入集群
先清理环境，然后再kubeadm join （Node节点上执行）

```
sudo kubeadm reset
sudo iptables -F && iptables -t nat -F && iptables -t mangle -F && iptables -X
sudo sysctl net.bridge.bridge-nf-call-iptables=1
sudo bash -c 'echo 1 > /proc/sys/net/ipv4/ip_forward'
sudo kubeadm join 10.0.1.162:6443  --token 9kpu6t.e1jf89l5uzminfo2 \
 	--discovery-token-ca-cert-hash sha256:3010d593b0b7d699ae53ae7b8939b2dc4169f5ff065650a8da18b4c959ac242a --node-name node1
```
#### 6、镜像拉不下来怎么办

先在镜像网站下载下来然后把docker镜像tag修改了

#### 7、swap没有关闭，防火墙没有关闭

会导致kubelet会有问题

#### 8、1.25.0版本的k8s拉不下来网络的镜像，导致node是notready

docker拉下来改tag也没用，降低版本1.21就好了

#### 9、pod 状态是evicted

没有gpu资源然后被驱逐了

10、k8s calico 插件错误：Number of node(s) with BGP peering established = 0 calico/node is not ready: BIRD is

https://blog.csdn.net/baobaoxiannv/article/details/113119528

#### 10、describe pod 发现network: error getting ClusterInformation: connection is unauthorized:

除了执行这个命令

```kubectl delete -f  <yaml>```

    还要去/etc/cni/net.d/目录下 删掉与calico相关的所有配置文件, 然后重启机器。 不然pod起不来，会报错 network: error getting ClusterInformation: connection is unauthorized: Unauthorized . 在这里发现的解决办法：source



#### 11、要关闭防火墙，或者开启特定端口，不能防火墙是failed状态

#### 12、node节点有taint kubectl taint ，node.kubernetes.io/disk-pressure:NoSchedule-

```
kubectl taint node node1  node.kubernetes.io/disk-pressure:NoSchedule-
```

node节点因为磁盘不够导致有污点上面的任务都被驱逐


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cuda-vector-add
spec:
  restartPolicy: OnFailure
  containers:
    - name: cuda-vector-add
      image: "k8s.gcr.io/cuda-vector-add:v0.1"
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```

13、安装docker-compose

```
sudo bash -c 'curl -L https://get.daocloud.io/docker/compose/releases/download/v2.4.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose'

sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose --version
```

14、harbor登陆不上去

```
sudo vim /usr/lib/systemd/system/docker.service
```

```
ExecStart=/usr/bin/dockerd  --insecure-registry=10.28.0.55:80
```

```
sudo systemctl daemon-reload
sudo systemctl restart docker
```

