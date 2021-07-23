---
title: defer函数
---



#### 1、**defer函数参数即时求值**

```
func g(i int) {
   fmt.Println("g i:", i)
}
func f() {
   i := 100
   defer g(i)  //1
   fmt.Println("begin i:", i)
   i = 200
   fmt.Println("end i:", i)
   return
}
```

会输出begin i :100,end i: 200,最后输出g i:100

**g（）函数延迟执行，参数不会**

#### 2、反序调用

```
func f() {
   defer fmt.Println("defer01")
   fmt.Println("begin")
   defer fmt.Println("defer02")
   fmt.Println("----")
   defer fmt.Println("defer03")
   fmt.Println("end")
   return
}
```

```
begin
----
end
defer03
defer02
defer01
```

#### 3、defer与return

**defer 函数的执行既不是在 return 之后也不是在 return 之前，而是一条go语言的 return 语句包含了对 defer 函数的调用**,给你举个例子：

```
package main
 
import "fmt"
 
var g = 100
 
func f() (r int) {
    defer func() {
        g = 200
    }()
 
    fmt.Printf("f: g = %d\n", g)
 
    return g
}
 
func main() {
    i := f()
    fmt.Printf("main: i = %d, g = %d\n", i, g)
}
```

输出g =100，i =100, g =200

这个简单理解，看看下一个例子

```
package main
 
import "fmt"
 
var g = 100
 
func f() (r int) {
    r = g
    defer func() {
        r = 200
    }()
 
    fmt.Printf("f: r = %d\n", r)
 
    r = 0
    return r
}
 
func main() {
    i := f()
    fmt.Printf("main: i = %d, g = %d\n", i, g)
}
```

输出：

r =100

i =200, g =100

看起来很冲突的结果，到汇编层面来看，用gdb层面来看，我看不懂，直接说结论

defer 函数的执行既不是在 return 之后也不是在 return 之前，而是一条go语言的 return 语句包含了对 defer 函数的调用，即 return 会被翻译成如下几条伪指令

```
保存返回值到栈上
调用defer函数
调整函数栈
retq指令返回
```

#### 自己的理解：return有两个步骤，把值放到栈里面，然后在return退出函数栈第一个例子return g是先g把值赋值给r,然后r的值放栈里面，g值的改变无关了，第二个例子是因为r的值放到栈里面，但是后来defer的时候r 的值改变了，所以最后return的时候r=200。

