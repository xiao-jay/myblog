---
title: 数据库模板
excerpt: 所在模块：golang
tags: [golang]
categories: 后端面试
banner_img: /img/壁纸.jpg

---

# 实现无限缓存的channel

Go语言的channel有两种类型，一种是无缓存的channel，一种是有缓存的buffer，这两种类型的channel大家都比较熟悉了，但是对于有缓存的channel,它的缓存长度在创建channel的时候就已经确定了，中间不能扩缩容，这导致在一些场景下使用有问题，或者说不太适合特定的场景。



我为什么突然谈起这个无限缓存的channel呢？主要是我最近在review公司一位同事的代码时，我的某种设计思路出现了一个问题，如果使用无限缓存的channel的话，我的问题就会迎刃而解了。

这位同事的设计大概是这样子的：

1. 一个dispatcher包含一个channel, 里面存放待处理的url
2. 一堆worker从channel中读取任务，下载解析网页，并提取其中链接，再把链接放入到dispatcher.channel中

这位同事为了解决并发的问题，不得不使用了比较复杂的sync.Mutex和sync.Cond，并且定义了一堆并发的方法处理逻辑，这里我想谈谈我的错误想法。

我review这段代码的时候想，如果每个 worker启动一个goroutine,处理url，然后把链接再放入到channel中即可，不用复杂的 Mutex+Cond等，但是我犯了一个错误，那就是如果当前channel已经满了，那么这些worker都不能把解析的结果放入到channel中，都被"阻塞"住了，并且也没有可用的worker从channel中消费url。

当然，你可以说可以创建一个buffer非常大的channel，避免被塞满，但是,第一,buffer非常大的channel占用的内存也非常大，第二，多大合适？关键你不能保证channel不会满。

如果有一个无限缓存长度的buffer就好了。

2017年，有同学向Go官方提出这么一个需求([#20352](https://github.com/golang/go/issues/20352)),希望能够提供一个无限容量的buffer，经过冗长的讨论，Go不会为这个"稀有"的场景提供一种实现，并且建议大家实现这样的一个库，通过第三库的方式处理这种场景，而且Griesemer提供了一个思路，通过ringbuffer实现缓存来实现这样的channel。

网上有两种实现[Why Go channels limit the buffer size](https://stackoverflow.com/questions/41906146/why-go-channels-limit-the-buffer-size)和[Building an Unbounded Channel in Go](https://medium.com/capital-one-tech/building-an-unbounded-channel-in-go-789e175cd2cd),这两种实现也比较类似，我在第一种实现的基础上，封装了一个库: [chanx](https://github.com/smallnest/chanx),来提供通用的无限缓存的channel。

[chanx](https://github.com/smallnest/chanx), 你可以star这个库，放入到你的代码库中，说不定哪一天它就可能帮你解燃眉之急。并且我已经准备好了泛型的设计，一旦Go泛型可用，我就会把它改成泛型的实现。

缓存无限的channel拥有下面的特性：

1. **不会阻塞write**。 它总是能处理write的数据，或者放入到待读取的channel中，或者放入到缓存中
2. **无数据时read会被阻塞**。当没有可读的数据时，从channel中读取的goroutine会被阻塞
3. **读写都是通过channel操作**。 内部的缓存不会暴露出来
4. **能够查询当前待读取的数据数量**。因为缓存中可能也有待处理的数据，所以需要返回len(buffer)+len(chan)
5. **关闭channel后，还未读取的channel还是能够被读取，读取完之后才能发现channel已经完毕**。这和正常的channel的逻辑是一样的，这种情况叫"drain"未读的数据

因为我们不能修改内部的channel结构，也不能重载 chan <- 和 <- chan 操作符,所以我们只能通过两个channel的方式封装一个数据结构,来提供读写。

这个数据结构为:

```
type UnboundedChan struct {
	In     chan<- T // channel for write
	Out    <-chan T // channel for read
	buffer []T      // buffer
}
```

其中`In`这个channel用来写入数据，而`Out`这个channel用来读取数据。你可以close In这个channel,等所有的数据都读取完后，Out channel也会被自动关闭。 用户是不能自己关闭`Out`这个channel的，你也关闭不了，因为它是`<-chan`类型的。

你可以通过`Len`方法得到所有待读取的数据的长度，也可以通过`BufLen`只获取缓存中的数据的长度，不包含外发`Out` channel中数据的长度。

```
// Len returns len of Out plus len of buffer.
func (c UnboundedChan) Len() int {
	return len(c.buffer) + len(c.Out)
}

// BufLen returns len of the buffer.
func (c UnboundedChan) BufLen() int {
	return len(c.buffer)
}
```

那么重点来了，主要的逻辑的实现如下，我在代码中加了注释，通过注释和代码你就可以很好的理解整个的实现逻辑:

```
func NewUnboundedChan(initCapacity int) UnboundedChan {

    // 创建三个字段和无限缓存的chan类型
	in := make(chan T, initCapacity)
	out := make(chan T, initCapacity)
	ch := UnboundedChan{In: in, Out: out, buffer: make([]T, 0, initCapacity)}

    // 通过一个goroutine,不断地从in中读取出来数据，放入到out或者buffer中
	go func() {
		defer close(out) // in关闭，数据读取完后也把out关闭
	loop:
		for {
			val, ok := <-in 
			if !ok { // 如果in已经被closed, 退出loop
				break loop
			}

			// 否则尝试把从in中读取出来的数据放入到out中
			select {
			case out <- val: //放入成功，说明out刚才还没有满，buffer中也没有额外的数据待处理，所以回到loop开始
				continue
			default:
			}

			// 如果out已经满了，需要把数据放入到缓存中
			ch.buffer = append(ch.buffer, val)

            // 处理缓存，一直尝试把缓存中的数据放入到out,直到缓存中没有数据了,
            // 为了避免阻塞住in channel,还要尝试从in中读取数据，因为这个时候out是满的，所以就直接把数据放入到缓存中
			for len(ch.buffer) > 0 {
				select {
				case val, ok := <-in: // 从in读取数据，放入到缓存中，如果in被closed, 退出loop
					if !ok { 
						break loop
					}
					ch.buffer = append(ch.buffer, val)

				case out <- ch.buffer[0]: // 把缓存中最老的数据放入到out中，并移出第一个元素
					ch.buffer = ch.buffer[1:]
					if len(ch.buffer) == 0 { // 避免内存泄露. 如果缓存处理完了，恢复成原始的状态
						ch.buffer = make([]T, 0, initCapacity)
					}
				}
			}
		}

		// in被关闭，退出loop后，buffer中可能还有未处理的数据，需要把它们塞入到out中
        // 这个逻辑叫做"drain"。
        // 这一段逻辑处理完后，就可以把out关闭掉了
		for len(ch.buffer) > 0 {
			out <- ch.buffer[0]
			ch.buffer = ch.buffer[1:]
		}
	}()

	return ch
}
```

这一段的逻辑还是很清晰的，就是细节需要注意，它也是学习channel使用的一个很好的素材。

## 5月13日更新

今天正好出差，在高铁上漫长的5个多小时没有事情做，所以我拿起笔记本干了两件事情，其中之一就是对这个无限缓存的channel做了优化。

本身这个无限缓存的channel的设计非常简洁，唯一有一点我不太满意的是它的buffer不能重用, 这和`ch.buffer = ch.buffer[1:]`的处理有关系,有时候明明底层的数组很大，但是还不得不重新生成新的数据，导致堆分配频次比较多。

依照Go三巨头之一的设计，底层buffer最好采用ringbuffer的实现方式，如果buffer满了应该能[自动扩容](https://github.com/golang/go/issues/20352#issuecomment-365494616):

> Such a library should do well in cases of very fast, "bursty" messages. A large enough buffered channel should be able to absorb bursts while a fast dedicated goroutine drains the channel into a ring buffer from which the messages are delivered at a slower pace to the final consumer of the messages. That ring buffer will need to be efficiently implemented, and will need to be able to grow efficiently (irrespective of size) and that will require some careful engineering. Better to leave that code to a library that can be tuned as needed than baking it into the runtime (and then possibly being at the mercy of release cycles).

所以我又实现了一个[ringbuffer](https://github.com/smallnest/chanx/blob/main/ringbuffer.go),这个ringbuffer比较简单，原因在这里我们不需要考虑并发的问题，这个ringbuffer只会在一个goroutine使用，所以它的实现就非常的简单了，需要注意"读追上写"，以及"写满"这两个边界问题就好了。通过使用ringbuffer，上面的实现就可以更改为下面的代码，可以进一步减少写爆发(burst)的时候分配过多的问题：

```
func NewUnboundedChan(initCapacity int) UnboundedChan {
	in := make(chan T, initCapacity)
	out := make(chan T, initCapacity)
	ch := UnboundedChan{In: in, Out: out, buffer: NewRingBuffer(initCapacity)}

	go func() {
		defer close(out)
	loop:
		for {
			val, ok := <-in
			if !ok { // in is closed
				break loop
			}

			// out is not full
			select {
			case out <- val:
				continue
			default:
			}

			// out is full
			ch.buffer.Write(val)
			for !ch.buffer.IsEmpty() {
				select {
				case val, ok := <-in:
					if !ok { // in is closed
						break loop
					}
					ch.buffer.Write(val)

				case out <- ch.buffer.Peek():
					ch.buffer.Pop()
					if ch.buffer.IsEmpty() && ch.buffer.size > ch.buffer.initialSize { // after burst
						ch.buffer.Reset()
					}
				}
			}
		}

		// drain
		for !ch.buffer.IsEmpty() {
			out <- ch.buffer.Pop()
		}

		ch.buffer.Reset()
	}()

	return ch
}
```

参考文章：https://colobu.com/2021/05/11/unbounded-channel-in-go/
