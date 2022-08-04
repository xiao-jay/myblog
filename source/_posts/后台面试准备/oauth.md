---
title: oauth2
excerpt: oauth2的介绍
categories: 后端面试
banner_img: /img/壁纸.jpg
---

## Oauth2是什么？

概念：oauth2是一个开放标准的授权协议

### OAuth中的角色

OAuth 2 标准中定义了以下几种角色：

- 资源所有者（Resource Owner）
- 资源服务器（Resource Server）
- 授权服务器（Authorization Server）
- 客户端（Client）

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h0gn2t38nqj219s0u0diw.jpg)

1. Authrization Request
   客户端向用户请求对资源服务器的`authorization grant`。
2. Authorization Grant（Get）
   如果用户授权该次请求，客户端将收到一个`authorization grant`。
3. Authorization Grant（Post）
   客户端向授权服务器发送它自己的客户端身份标识和上一步中的`authorization grant`，请求访问令牌。
4. Access Token（Get）
   如果客户端身份被认证，并且`authorization grant`也被验证通过，授权服务器将为客户端派发`access token`。授权阶段至此全部结束。
5. Access Token（Post && Validate）
   客户端向资源服务器发送`access token`用于验证并请求资源信息。
6. Protected Resource（Get）
   如果`access token`验证通过，资源服务器将向客户端返回资源信息。
