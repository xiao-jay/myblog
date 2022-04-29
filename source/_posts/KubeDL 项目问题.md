# KubeDL 项目问题

项目负责人你好，我叫应宇杰，来自杭州电子科技大学，是计科大三，无k8s基础，有一定docker使用经验。想参与2022年开源之夏的KubeDL项目，原因有二：

- 我想参加开源之夏kubeDL项目，深入参与一次开源社区的开发，提高自己的能力

- 我在一个人工智能小公司实习，公司有一定的机器数量我想使用这个项目来提高公司的机器资源利用率（这个只是我想做的，公司还没和他们商量）。

在尝试看文档使用本机**mac m1**复现quick start，但是遇到了一些困难，提了issue没人理，又不知道该找谁

##### 1、使用这个项目需要什么前置知识吗，举个例子：需要了解k8s内部service的调度



##### 2、在quict start的时候，发现运行

（1）、```helm install kubedl ./helm/kubedl --create-namespace -n kubedl-system```

会报错   ```Error: INSTALLATION FAILED: path "./helm/kubedl" not found```

（2）、运行 ```kubectl apply -f https://raw.githubusercontent.com/kubedl-io/kubedl/master/example/tf/tf_job_mnist_distributed_simple.yaml```

先是开了梯子也拉不下来，我就下载到本地，然后运行会报错

```
error: unable to recognize "tf_job_mnist_distributed_simple.yaml": no matches for kind "TFJob" in version "training.kubedl.io/v1alpha1"
```

发现这个项目无从下手，很难入门写个demo，请问能在快速开始这方面指导一下吗



##### 3、想请问一下今年开源之夏会有什么题目，想快点入门（有点心急），然后可以参与进来



