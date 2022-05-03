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
yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
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
 vim /etc/fstab，注释掉swap那一行
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

vi /etc/sysconfig/kubelet

改为如下参数
KUBELET_EXTRA_ARGS=--cgroup-driver=systemd
```

6、

```
kubeadm init --image-repository registry.aliyuncs.com/google_containers --pod-network-cidr=10.244.0.0/16
```

如果安装失败，可以使用下面命令重置

```
kubeadm reset
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
kubeadm reset
rm -rf /etc/kubernetes/
```





## 安装的时候遇到的错误

#### 1、It seems like the kubelet isn‘t running or healthy

查看官网介绍为 docker 和 kubelet 服务中的 cgroup 驱动不一致，有两种方法
方式一：驱动向 docker 看齐
方式二：驱动为向 kubelet 看齐
如果docker 不方便重启则统一向 kubelet看齐，并重启对应的服务即可

解决方式
docker 配置文件
这里采取的是方式二，docker 默认驱动为 cgroupfs ,只需要添加

 "exec-opts": [
    "native.cgroupdriver=systemd"
  ],

修改后配置文件

root@controlplane:~# cat /etc/docker/daemon.json 
{
  "exec-opts": [
    "native.cgroupdriver=systemd"
  ],
  "bip":"172.12.0.1/24",
  "registry-mirrors": [
    "http://docker-registry-mirror.kodekloud.com"
  ]
}

重启docker
systemctl restart docker

kublete 配置文件
grep 截取一下,可以看得出来kubelet默认 cgoup 驱动为systemd

root@controlplane:~# cat /var/lib/kubelet/config.yaml |grep group
cgroupDriver: systemd
1
2
重启kubelet （optional）
systemctl restart kubelet