---
title: channel
excerpt: 所在模块：golang
tags: [golang]
categories: 后端面试
banner_img: /img/壁纸.jpg
---



#### 往一个关闭的channal里面接收数据能够接收到吗

```
package main

import "fmt"

func main() {
	ch := make(chan int, 1)
	ch <- 1

	close(ch)
	fmt.Println(<-ch)
	fmt.Println(<-ch)
	ch <- 1
}
```



可以，但是从channal里面拿就会panic





## 问题

### 1、能使用无缓存的channal做mutex吗

虽然没有缓存，但是可以有一个放入，多个放出，放出可以抽象成争抢锁

1、设置一个goroutine，专门来放入，因为没有拿就阻塞了，如果有人拿就退出

2、有人拿了，没有被阻塞，说明拿到锁了，开始执行任务，然后执行完的时候重新启动一个goroutine来放入锁，等待下一个拿到锁的人。
