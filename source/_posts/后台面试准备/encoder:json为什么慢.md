---
title: encoder/json为什么慢
excerpt: 所在模块：json
tags: [json]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

### encoder/json为什么慢

因为使用了较多的反射，会导致时间变慢

### 为什么jsoniter快

- 减少不必要的内存复制。
- 减少 反射(`reflect`） 的使用，对同一类型的对象，`jsoniter `只反射一次之后即缓存下来。
- 获取部分内容的时候尽可能解析少的东西，按需解析

和 encoding/json 的区别是，标准库使用的是 reflect.ValueOf ，然后根据 json 的输入情况去找对应的 field 和 element 。而 jsoniter 的实现是反过来的，用 reflect.TypeOf 确定一个 json 的 schema ，然后根据 schema 产生对应的 decoder 。如果 json 输入不符合这个 decoder 则报错。

