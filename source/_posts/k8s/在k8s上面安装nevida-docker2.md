# 在k8s上面安装nevida-docker2

```
sudo yum-config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
```

```
curl -s -L https://nvidia.github.io/nvidia-docker/centos7/x86_64/nvidia-docker.repo | sudo tee /etc/yum.repos.d/nvidia-docker.repo 
```



### 0、daemon.json 配置



```
(base) [xinchenTest@master docker]$ cat /etc/docker/daemon.json 
{
    "default-runtime": "nvidia",
    "runtimes": {
        "nvidia": {
            "path": "nvidia-container-runtime",
            "runtimeArgs": []
        }
    },
"exec-opts": ["native.cgroupdriver=systemd"],
"registry-mirrors":
        ["http://7e61f7f9.m.daocloud.io"],
"live-restore": true,
"graph": "/data/docker"
}
```

配置完别忘记

```
sudo systemctl daemon-reload
sudo systemctl restart docker
```



### 1、移除nvidia-docker 1.0

```
su do docker volume ls -q -f driver=nvidia-docker | xargs -r -I{} -n1 docker ps -q -a -f volume={} | xargs -r docker rm -f
sudo yum remove nvidia-docker
```

###  2、安裝nvidia-docker 2.0

```
sudo yum install nvidia-docker2
```

### 3、重新載入Docker daemon的設定

```
sudo pkill -SIGHUP dockerd
```

### 4、测试是否成功

```
sudo docker run --runtime=nvidia --rm nvidia/cuda:11.1.1-base-centos7 d  nvidia-smi
```

5、

```
kubectl create -f https://raw.githubusercontent.com/NVIDIA/k8s-device-plugin/1.0.0-beta4/nvidia-device-plugin.yml
```



##### Configure `containerd`

When running `kubernetes` with `containerd`, edit the config file which is usually present at `/etc/containerd/config.toml` to set up `nvidia-container-runtime` as the default low-level runtime:

```
version = 2
[plugins]
  [plugins."io.containerd.grpc.v1.cri"]
    [plugins."io.containerd.grpc.v1.cri".containerd]
      default_runtime_name = "nvidia"

      [plugins."io.containerd.grpc.v1.cri".containerd.runtimes]
        [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.nvidia]
          privileged_without_host_devices = false
          runtime_engine = ""
          runtime_root = ""
          runtime_type = "io.containerd.runc.v2"
          [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.nvidia.options]
            BinaryName = "/usr/bin/nvidia-container-runtime
```

journalctl -xe

```go
# 正常来说会输出已经安装过的程序
$ rpm -qa | grep nvidia

# 正常来说会输出已经安装过的程序
$ rpm -qa | grep cuda
```
