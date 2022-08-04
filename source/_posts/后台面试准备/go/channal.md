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

