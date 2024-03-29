---
title: 三次握手，四次挥手
excerpt: 所在模块：计算机网络，讲了tcp的🤝和🙋
tags: [计算机网络]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

## TCP

**报文结构：**

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h0gcwqp4x2j20on0ewwft.jpg)

#### TCP有哪些特点

1、TCP是面向连接的

2、是点对点通信的

3、TCP提供可靠交付服务，无差错，不丢失，不重复按序到达

4、TCP是面向字节流的服务

5、TCP 提供全双工通信。TCP 允许通信双方的应用进程在任何时候都能发送数据

#### 三次握手

大致理解：tcp为了确认传输数据的安全，第一次发送特殊tcp报文段给客户，客户第二次回复特殊的tcp报文段给服务器，第三次服务器发送要发送的数据给客户。

![](https://img2018.cnblogs.com/blog/1344250/201904/1344250-20190402114137828-2119548758.png)

- 第一次客户端发送SYN=1和初始化的序列号seq x给服务器请求连接连接（ SYN：同步标志）
- 第二次服务器回复ACK=1，ack=x+1,seq=y是发送数据的第一个字符的数据编号，SYN为1 代表收到消息并且也请求连接
- 第三次用户回复ACK=1，ack为y+1，sep为x+1的tcp报文，连接完毕可以传输数据

ACK代表确认（acknowledge character）

序列号seq就是这个报文段中的第一个字节的数据编号

#### 2、四次挥手

![](https://img2018.cnblogs.com/blog/1344250/201904/1344250-20190402114059390-716421818.png)

- 用户和服务器其中两个都可以终止连接，假设用户想取消连接了，先给服务器发送一个FIN置为1,seq为u的报文。
- 服务器收到之后，会立马回复一个ACK为1，ack为u+1，seq为v的报文，ack会自动回复上一个报文加一的seq数据，表示收到。
- 隔了一个close_wait时间回复一个FIN置为1，ACK置为1的报文，seq为w，ack为u+1表示同意，为什么要等待一段时间，是因为要询问上层的软件是否同意关闭，服务器不发FIN代表自己可以收到消息但是客户不会发消息了
- 最后用户回复ACK为1，seq为u+1,ack为w+1的报文，结束连接。



### [TCP拥塞控制](http://sjy.xn--6qq986b3xl/2022/03/20/%E5%90%8E%E5%8F%B0%E9%9D%A2%E8%AF%95%E5%87%86%E5%A4%87/%E7%BD%91%E7%BB%9C/TCP%E6%8B%A5%E5%A1%9E/)

### SYN洪范攻击

服务器为了响应一个收到的SYN，分配并初始化连接变量和缓存，如果攻击者发送大量的TCP SYN报文，服务器不断为这些半开连接分配资源，导致服务器的连接资源消耗殆尽。

解决办法：**SYN cookie**

服务器接收到一个SYN包的时候并不生成一个半开连接，而是生成一个源ip+目的ip+秘密数的一个值，发送过去，但是并不保存，如果客户端发过来的值是确认值+1，那就说明是合法用户，生成一个套接字的全开连接。

### 滑动窗口

TCP 利用滑动窗口实现流量控制。流量控制是为了控制发送方发送速率，保证接收方来得及接收。 TCP会话的双方都各自维护一个发送窗口和一个接收窗口。接收窗口大小取决于应用、系统、硬件的限制。发送窗口则取决于对端通告的接收窗口。接收方发送的确认报文中的window字段可以用来控制发送方窗口大小，从而影响发送方的发送速率。将接收方的确认报文window字段设置为 0，则发送方不能发送数据。

![img](https://uploadfiles.nowcoder.com/files/20211017/530285728_1634456987546/image-20210921112213523.png)

TCP头包含window字段，16bit位，它代表的是窗口的字节容量，最大为65535。这个字段是接收端告诉发送端自己还有多少缓冲区可以接收数据。于是发送端就可以根据这个接收端的处理能力来发送数据，而不会导致接收端处理不过来。接收窗口的大小是约等于发送窗口的大小。

#### 问题

**1、 为什么三次握手？而不是两次握手**

三次握手是得把ACK和SYN都发送给对方，三次握手才能保证双方都接收到消息，第一次是喂，用户：我要连接，服务器：好，我收到了，用户：我收到你的收到了，如果是两次握手那服务器不知道客户收到没有。四次握手是没必要，已经有很大概率链接连接成功了，而且四次握手需要更多的网络资源。

**为什么第三次挥手要等一会再发送？**

原因就是可能上层应用还在发送数据，第一个FIN发过来就告诉上层应用连接要关闭啦，没穿完数据就快点传完，然后等一个close wait自动发送第二个fin包，这样防止数据丢失。

**2、为什么客户端最后还要等待2MSL？**

确实服务器收到了ACK=1，如果没收到可以等计时器超时服务器再重发第三次挥手，客户端接受了再发送ACK。

**3、ISN代表什么？意义何在？**

发送方的字节数据编号的原点，isn代表双方连接的钥匙，钥匙要对上孔

4、**ISN是固定不变的吗？**

动态随机

5、**ISN为何要动态随机？**

防止被第三方猜到，从而被第三方仿造的RST报文的reset。

**6、三次握手的第一次可以携带数据吗？为何？**对方难道不可以将数据缓存下来，等握手成功再提交给应用程序？

**不可以，三次握手还没有完成。**，因为如果带大量1k字节数据的话服务器要开辟大量缓存来处理，容易使服务器拒绝访问，这是洪泛攻击

**7、第三次可以携带数据吗？为何？**

可以，能够发送第三次代表第二次报文已经接收到了，伪装的ip接收不到第二次报文，所以第三次发送的时候是安全的。

**8、如果输入的端口不存在会怎么办**

握手不能进行，会出现超时未响应的错误。

**9、为什么断开连接却需要四次呢？**

1、确保数据能够完整传输。
2、当被动方收到主动方的FIN报文通知时，它仅仅表示主动方没有数据再发送给被动方了。但未必被动方所有的数据都完整的发送给了主动方，所以被动方不会马上关闭SOCKET,它可能还需要发送一些数据给主动方后，再发送FIN报文给主动方，告诉主动方同意关闭连接，所以这里的ACK报文和FIN报文多数情况下都是分开发送的。

**10、TCP四次挥手出现大量time_wait是什么原因**

TIME_WAIT产生条件：TCP四次挥手中，第一个发送FIN=1的TCP报文的人称为主动关闭者，主动关闭者在发送四次挥手最后一个ACK后会变成TIME_WAIT状态，持续时间为2MSL（linux一个MSL为30s，不可配置）

出现这么多短连接很可能是因为使用了http 1.0 ，使用非持久连接，因为http 1.0请求一次就建立一次tcp连接，请求完立马关闭连接。

**11、怎么复用TCP链接**

- 使用http1.1中的持久连接，传输完一个数据后不关闭tcp连接，可以复用tcp链接。

- [使用http2.0中tcp多路复用技术](http://sjy.xn--6qq986b3xl/2022/03/22/%E5%90%8E%E5%8F%B0%E9%9D%A2%E8%AF%95%E5%87%86%E5%A4%87/%E7%BD%91%E7%BB%9C/HTTP1.0%E5%92%8C2.0%E7%9A%84%E5%8C%BA%E5%88%AB/)

  

#### TCP和UDP的区别

UDP报文：

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h0ge8y7o54j22ab0t677i.jpg)

TCP的优点：提供可靠稳定安全的连接，无差错，不重复，不丢失，三次握手建立连接，数据传输时有确认、窗口、重传、拥塞控制，数据传完还有四次挥手断开连接。

TCP缺点：慢，效率低，开销较大，占用硬件资源多，传输效率较低，TCP有确认机制容易被人利用，实现DDOS等攻击

UDP的优点：无状态的传输协议，单位时间传输数据多。

UDP的缺点：不可靠，在网络不好的时候容易丢包。

TCP和UPD的区别

1、基于连接与无连接

2、对系统资源的要求不同

3、UDP程序结构比较简单

4、TCP要求数据正确，保证数据顺序；UDP可能丢包，不保证数据顺序。



### tcp在socket各阶段

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h651zzbxb2j20le0m2acf.jpg)

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h651yhv5trj20qu0kn751.jpg)