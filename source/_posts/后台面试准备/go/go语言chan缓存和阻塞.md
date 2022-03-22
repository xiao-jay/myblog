---
title: go语言chan缓存和阻塞
excerpt: 所在模块：golang
tags: [golang]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

问题：下面迭代会有什么问题

```
package main
 
import (
	"fmt"
	"sync"
)
 
//下面的迭代会有什么问题？
type threadSafeSet struct {
	sync.RWMutex
	s []interface{}
}
 
func (set *threadSafeSet) Iter() <-chan interface{} {
	ch := make(chan interface{}) // 解除注释看看！
	//ch := make(chan interface{},len(set.s))
	go func() {
		set.RLock()
		for elem,value := range set.s {
			ch <- elem
			fmt.Println("Iter:",elem,value)
		}
		close(ch)
		set.RUnlock()
	}()
	return ch
}
 
func main()  {
	th:=threadSafeSet{
		s:[]interface{}{"1","2"},
	}
	vs := th.Iter()
	fmt.Println(<- vs)
	}
```

结果

```
Iter: 0 1
0
```

解释一下这段代码，在iter（）里面有一个goroutin，用来执行输入数据到channel里面，这里创了一个0空间的channel缓存，只要输入一个数据就会阻塞，为什么这里还能输入一个，因为倒数第二行输出了一个channel，就可以再输入一个数据然后阻塞。

如果把vs这个变量取消掉，然后把prinln也去掉，就什么都不能输出了

为了方便你的理解，把代码里面注释的去掉，把原来那行注释

	ch := make(chan interface{}) 
	//ch := make(chan interface{},len(set.s))
现在channel就有两个缓存了，只有能够把数据输入到channel里面不堵塞才能执行这句话

```
fmt.Println("Iter:",elem,value)
```





