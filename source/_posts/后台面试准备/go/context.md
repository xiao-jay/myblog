---
title: Context
excerpt: 所在模块：golang
tags: [golang]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

### 使用Context来控制协程

1、控制单个协程，context.WithCancel

```
func reqTask(ctx context.Context, name string) {
	for {
		select {
		case <-ctx.Done():
			fmt.Println("stop", name)
			return
		default:
			fmt.Println(name, "send request")
			time.Sleep(1 * time.Second)
		}
	}
}

func main() {
	ctx, cancel := context.WithCancel(context.Background())
	go reqTask(ctx, "worker1")
	time.Sleep(3 * time.Second)
	cancel()
	time.Sleep(3 * time.Second)
}
```

2、控制多个协程（多个协程间的同步）

```
func main() {
	ctx, cancel := context.WithCancel(context.Background())
	
	go reqTask(ctx, "worker1")
	go reqTask(ctx, "worker2")
	
	time.Sleep(3 * time.Second)
	cancel()
	time.Sleep(3 * time.Second)
}
```

3、ctx 传递值，context.WithValue

```
type Options struct{ Interval time.Duration }

func reqTask(ctx context.Context, name string) {
	for {
		select {
		case <-ctx.Done():
			fmt.Println("stop", name)
			return
		default:
			fmt.Println(name, "send request")
			op := ctx.Value("options").(*Options)
			time.Sleep(op.Interval * time.Second)
		}
	}
}

func main() {
	ctx, cancel := context.WithCancel(context.Background())
	vCtx := context.WithValue(ctx, "options", &Options{1})

	go reqTask(vCtx, "worker1")
	go reqTask(vCtx, "worker2")

	time.Sleep(3 * time.Second)
	cancel()
	time.Sleep(3 * time.Second)
}
```

4、控制执行时间，context.WithTimeout

```
func main() {
	ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
	go reqTask(ctx, "worker1")
	go reqTask(ctx, "worker2")

	time.Sleep(3 * time.Second)
	fmt.Println("before cancel")
	cancel()
	time.Sleep(3 * time.Second)
}
```

5、根据时间点来决定退出，context.WithDeadline

```
func reqTask(ctx context.Context, name string) {
	for {
		select {
		case <-ctx.Done():
			fmt.Println("stop", name, ctx.Err())
			return
		default:
			fmt.Println(name, "send request")
			time.Sleep(1 * time.Second)
		}
	}
}

func main() {
	ctx, cancel := context.WithDeadline(context.Background(), time.Now().Add(1*time.Second))
	go reqTask(ctx, "worker1")
	go reqTask(ctx, "worker2")

	time.Sleep(3 * time.Second)
	fmt.Println("before cancel")
	cancel()
	time.Sleep(3 * time.Second)
}
```

`WithTimeout`，带有超时时间的cancelCtx的Context，它是WithDeadline的封装，只不过WithTimeout为时间间隔，Deadline为时间点。

参考文章：https://geektutu.com/post/quick-go-context.html
