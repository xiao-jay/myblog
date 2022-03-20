---
title: HTTP 和 HTTPS 
excerpt: 所在模块：计算机网络
tags: [计算机网络]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

#### HTTP

http是超文本传输协议，最开始是为了方便传输html而创建的，传输的信息都是没经过加密的，并且是无状态的，连接通过TCP连接，端口为80，安全性很差，但是连接很简单，速度快减少界面加载延时

#### http缺点

1、无法验证报文的完整性

2、无法验证发送人，所以可能被伪装

3、通信使用明文，可能信息被窃听

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h0gi9djnslj20g20btdg9.jpg)

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h0gi9j8u5uj20re0iejsd.jpg)

#### http常见状态码

| http状态码                | 解释                                     |
| ------------------------- | ---------------------------------------- |
| 200 OK                    | 请求成功完成                             |
| 301 Moved Permanently     | 永久移动                                 |
| 302 Found                 | 临时移动                                 |
| 400 Bad Request           | 客户端请求错误,服务器理解不了            |
| 403 Forbidden             | 服务器收到并且理解请求，但是拒绝提供服务 |
| 404 Not Found             | 客户端所要找的资源找不到                 |
| 500 Internal Server Error | 服务器错误                               |

### HTTP2.0



### HTTPS

https是经过ssl证书认证并且在传输过程加密的一种传输方式，端口为443安全性大大增加，https = http+ssl加密

### HTTP和HTTPS的区别

安全的基础是ssl协议，连接方式不同（待补充），https是http+ssl协议进行构建的加密传输、身份认证的网络协议，比http协议安全很多，连接方式通常也是tcp，也有可能是SCTP。

ssl由权威机构颁发，ssl是一个非对称加密，有一个公钥，和一个私钥，用公钥加密后的信息只能由私钥解密，用私钥加密的数据只能公钥解密,公钥放在服务器，私钥放在个人主机。

#### Get和Post的区别

1、get请求表示的意思是要得到信息，并且get请求参数都放header或者url上面，安全性较差放url上面有长度限制，最多1024个字符，不适合传大长度信息。

2、post请求表示添加信息 ，请求参数作为键值对放在http请求中，不会出现在url中，安全性较get较好

3、get请求在url不可加入中文字符，会乱码，post可以

#### Restful风格的接口

1、请求头

get  表示请求数据 ，一般数据放header里面

post 表示增加数据 ，一般数据放body里面

put 表示修改数据，一般数据放body里面

delete  表示删除数据，一般数据放body里面

2、url的复用，比如work这块，写一个group里面，然后get “/”表示获取工作信息，post“/”增加工作信息

3、状态码，详情看上面

4、制作一个统一的返回信息的格式，写一个可以多处用的success return和error return，返回结果统一json