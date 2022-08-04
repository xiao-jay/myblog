---
title: redis命令
excerpt: 所在模块：数据库
tags: [数据库]
categories: 后端面试
banner_img: /img/壁纸.jpg
---



### Hash类型的操作命令

1、hget

2、hset

```
hset myhash id 1
```



3、hmeset 

同时设置多对值

4、hgetall

获取key下所有值

```
hgetall myhash
```

5、hlen

```
> hlen mash
1
```

6、hkeys，hvals

```
hkeys myhash
```



获取所有key或者val

