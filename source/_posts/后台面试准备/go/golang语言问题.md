---
title: golang后台面试题目
---

1、go struct 能不能比较

struct是强类型语言，不同类型不能比较，同类型才能比较，实例也不能比较，因为实例是指针类型

2、写出下面代码输出内容

```
package main

import (
	"fmt"
)

func main() {
	defer_call()
}

func defer_call() {
	defer func() { fmt.Println("打印前") }()
	defer func() { fmt.Println("打印中") }()
	defer func() { fmt.Println("打印后") }()

	panic("触发异常")
}
```

打印后
打印中
打印前
panic: 触发异常

#### 3、range陷阱

```
package main

import "fmt"

type AStruct struct {
    bar string
}

func main() {
    list := []AStruct{
        {"1"},
        {"2"},
        {"3"},
    }

    copyedList := make([]*AStruct, len(list))
    for i,v:= range list {
        copyedList[i] = &v
    }

    fmt.Println(list[0], list[1], list[2])
    fmt.Println(copyedList[0], copyedList[1], copyedList[2])
}
```

运行这段代码，你就会发现

````
{1} {2} {3}
&{3} &{3} &{3}
````

怎么都是同一个地址，因为range赋值用的是用的是一个循环变量的地址，Go每次循环都会复用这一"循环变量"，所以一直会输出最后一个数的地址

##### 解决方案

```go
for i := range list {
  copyedList[i] = &list[i]
}
```

#### 4、变量的作用域陷阱

下面这段程序会有什么输出结果？

```
package main

import (
    "fmt"
    "runtime"
    "sync"
)

func main() {
    runtime.GOMAXPROCS(1)
    wg := sync.WaitGroup{}
    wg.Add(20)
    for i := 0; i < 10; i++ {
        go func() {
            fmt.Println("A: ", i)
            wg.Done()
        }()
    }
    for i := 0; i < 10; i++ {
        go func(i int) {
            fmt.Println("B: ", i)
            wg.Done()
        }(i)
    }
    wg.Wait()
}

```

A都输出10，B先输出9，然后按顺序输出

```
B:  9
A:  10
A:  10
A:  10
A:  10
A:  10
A:  10
A:  10
A:  10
A:  10
A:  10
B:  0
B:  1
B:  2
B:  3
B:  4
B:  5
B:  6
B:  7
B:  8
```

如果不用runtime.GOMAXPROCS(1)约束，就会随机输出A和B，A和B中数字也随机输出,但是A是10

```
A:  3
B:  5
A:  10
B:  9
A:  10
A:  10
A:  10
A:  10
B:  1
A:  10
B:  0
B:  2
B:  3
B:  4
A:  3
B:  6
B:  7
B:  8
A:  10
A:  10
```

为什么A一直会是10，这是一个变量作用域不同引起的

#### 5、继承和组合的区别和优势劣势（还不懂）

**继承**是一个白盒模型，父类的内部细节对子类是透明的，

**组合**是黑盒模型，父类的内部细节对子类不透明，对现有的对象进行拼装组合产生更复杂的功能

##### 继承的优点：

易于操作，易于修改和拓展父类

##### 继承的缺点：

1、子类和父类缺乏独立性，父类改变子类不得不改变

2、破坏了封装性

##### 组合的优点

1、降低各个模块之间的耦合性

2、不会破坏封装性，只用一个接口用来连接对象内部的函数

3、减少依存关系

##### 组合的缺点

1、操作困难，对不熟悉的人难以操作

2、比继承要写更多的代码

#### 6、go的组合和继承

```
type People struct{}

func (p *People) ShowA() {
    fmt.Println("showA")
    p.ShowB()
}
func (p *People) ShowB() {
    fmt.Println("showB")
}

type Teacher struct {
    People
}

func (t *Teacher) ShowB() {
    fmt.Println("teacher showB")
}

func main() {
    t := Teacher{}
    t.ShowA()
}
```

输出

```
showA
showB
```

解答：被组合的类型People虽然被包装成Teacher，执行t.ShowA()但是people并不自己会被什么类型组合，所以t.showB()不会执行

#### 7、select的随机性

```
func main() {
    runtime.GOMAXPROCS(1)
    int_chan := make(chan int, 1)
    string_chan := make(chan string, 1)
    int_chan <- 1
    string_chan <- "hello"
    select {
    case value := <-int_chan:
        fmt.Println(value)
    case value := <-string_chan:
        panic(value)
    }
}
```

答案：select随机执行一个case，有一定概率出现panic（“hello”），一定概率输出1

#### 8、defer执行顺序

```
func calc(index string, a, b int) int {
	ret := a + b
	fmt.Println(index, a, b, ret)
	return ret
}

func main() {
	a := 1
	b := 2
	defer calc("1", a, calc("10", a, b))
	a = 0
	defer calc("2", a, calc("20", a, b))
	b = 1
}
```

结果

```
10 1 2 3
20 0 2 2
2 0 2 2
1 1 3 4
```

解析;calc("1", a, calc("10", a, b))会先对这句话里面的a，b赋值，赋值过程中先执行10，同理下一句话先执行20，再是2，最后1，因为defer是像栈一样，最后的先执行

#### 9、看看下面会有什么问题

```
type UserAges struct {
	ages map[string]int
	sync.Mutex
}

func (ua *UserAges) Add(name string, age int) {
	ua.Lock()
	defer ua.Unlock()
	ua.ages[name] = age
}

func (ua *UserAges) Get(name string) int {
	if age, ok := ua.ages[name]; ok {
		return age
	}
	return -1
}

```

解答：在并发处理的时候可能会出现`fatal error: concurrent map read and map write`.，因为get没上锁，在同一时间读写一个东西会出现竞争

#### 10、printf和Sprintf的区别

printf

1. 用传入的格式化规则符将传入的变量写入到标准输出里面(即在终端中有显示)，
2. 返回值为 写入标准输出的字节数和写入过程中遇到的问题。

sprintf

1. 用传入的格式化规则符将传入的变量格式化，(终端中不会有显示)
2. 返回为 格式化后的字符串。

```
package main

import (
    "fmt"
)

func main()  {

    testStr := []byte("test str")
    fmt.Println(testStr, "hello world")    //[116 101 115 116 32 115 116 114] hello world
    fmt.Printf("%s", testStr) //test str
    fmt.Println()
    fmt.Sprintf("%s", testStr) //空，无IO输出
    fmt.Println()
    printStr := fmt.Sprintf("%s", testStr)
    fmt.Println(printStr)    //test str
}
```

sprintf就是把一个[]byte类型的东西格式化成string