---
title: golang内存分配
excerpt: 所在模块：golang
tags: [golang]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

go语言启动时会分配一块内存，然后切成小块进行管理，分成span，bitmap，arena，

arena：将内存分成8kb的页（page），一起组成起来称mspan，成为go内存管理的基本单元。

bitmap：标记堆区使用的映射表，记录哪些区保存对象，哪些区包含指针

spans:存放mspan指针，根据spans信息很容易找到mspan，可以在GC时更快速找到大块内存mspan。





### goroutine的栈内存分配

在`Go`应用程序运行时，每个`goroutine`都维护着一个自己的栈区，这个栈区只能自己使用不能被其他`goroutine`使用。**栈区的初始大小是2KB**（比x86_64架构下线程的默认栈2M要小很多），在`goroutine`运行的时候栈区会按照需要增长和收缩，占用的内存最大限制的默认值在64位系统上是1GB。栈大小的初始值和上限这部分的设置都可以在`Go`的源码`runtime/stack.go`里找到：

```go
// rumtime.stack.go
// The minimum size of stack used by Go code
_StackMin = 2048

var maxstacksize uintptr = 1 << 20 // enough until runtime.main sets it for real
```

其实栈内存空间、结构和初始大小在最开始并不是2KB，也是经过了几个版本的更迭

- v1.0 ~ v1.1 — 最小栈内存空间为 4KB；
- v1.2 — 将最小栈内存提升到了 8KB；
- v1.3 — 使用**连续栈**替换之前版本的分段栈；
- v1.4 — 将最小栈内存降低到了 2KB；

### 分段栈和连续栈

#### 分段栈

Go 1.3 版本前使用的栈结构是分段栈，随着`goroutine` 调用的函数层级的深入或者局部变量需要的越来越多时，运行时会调用 `runtime.morestack` 和 `runtime.newstack`创建一个新的栈空间，这些栈空间是不连续的，但是当前 `goroutine` 的多个栈空间会以双向链表的形式串联起来，运行时会通过指针找到连续的栈片段：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/98871ce62ad74f578ddcc1f879c49f07~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

分段栈虽然能够按需为当前 `goroutine` 分配内存并且及时减少内存的占用，但是它也存在一个比较大的问题：

- 如果当前 `goroutine` 的栈几乎充满，那么任意的函数调用都会触发栈的扩容，当函数返回后又会触发栈的收缩，如果在一个循环中调用函数，栈的分配和释放就会造成巨大的额外开销，这被称为热分裂问题（Hot split）。

为了解决这个问题，Go在1.2版本的时候不得不将栈的初始化内存从4KB增大到了8KB。后来把采用连续栈结构后，又把初始栈大小减小到了2KB。

### 连续栈

连续栈可以解决分段栈中存在的两个问题，其核心原理就是每当程序的栈空间不足时，初始化一片比旧栈大两倍的新栈并将原栈中的所有值都迁移到新的栈中，新的局部变量或者函数调用就有了充足的内存空间。使用连续栈机制时，栈空间不足导致的扩容会经历以下几个步骤：

1. 调用用`runtime.newstack`在内存空间中分配更大的栈内存空间；
2. 使用`runtime.copystack`将旧栈中的所有内容复制到新的栈中；
3. **将指向旧栈对应变量的指针重新指向新栈**；
4. 调用`runtime.stackfree`销毁并回收旧栈的内存空间；

![img](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/498c581f48894526850a3f05708b38c1~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)


#### 栈区的内存管理

前面说了每个`goroutine`都维护着自己的栈区，栈结构是连续栈，是一块连续的内存，在`goroutine`的类型定义的源码里我们可以找到标记着栈区边界的`stack`信息，`stack`里记录着栈区边界的高位内存地址和低位内存地址：

```go
type g struct {
	stack       stack
  ...
}

type stack struct {
	lo uintptr
	hi uintptr
}
```

### 栈扩容

编译器会为函数调用插入运行时检查`runtime.morestack`，它会在几乎所有的函数调用之前检查当前`goroutine` 的栈内存是否充足，如果当前栈需要扩容，会调用`runtime.newstack` 创建新的栈：

```go
func newstack() {
   ......
   // Allocate a bigger segment and move the stack.
   oldsize := gp.stack.hi - gp.stack.lo
   newsize := oldsize * 2
   if newsize > maxstacksize {
       print("runtime: goroutine stack exceeds ", maxstacksize, "-byte limit\n")
      throw("stack overflow")
   }

   // The goroutine must be executing in order to call newstack,
   // so it must be Grunning (or Gscanrunning).
   casgstatus(gp, _Grunning, _Gcopystack)

   // The concurrent GC will not scan the stack while we are doing the copy since
   // the gp is in a Gcopystack status.
   copystack(gp, newsize, true)
   if stackDebug >= 1 {
      print("stack grow done\n")
   }
   casgstatus(gp, _Gcopystack, _Grunning)
}
```

旧栈的大小是通过我们上面说的保存在`goroutine`中的`stack`信息里记录的栈区内存边界计算出来的，然后用旧栈两倍的大小创建新栈，创建前会检查是新栈的大小是否超过了单个栈的内存上限。

```go
   oldsize := gp.stack.hi - gp.stack.lo
   newsize := oldsize * 2
   if newsize > maxstacksize {
       print("runtime: goroutine stack exceeds ", maxstacksize, "-byte limit\n")
      throw("stack overflow")
   }
```

如果目标栈的大小没有超出程序的限制，会将 `goroutine` 切换至 `_Gcopystack` 状态并调用 `runtime.copystack` 开始栈的拷贝，在拷贝栈的内存之前，运行时会先通过`runtime.stackalloc` 函数分配新的栈空间：

```go
func copystack(gp *g, newsize uintptr) {
	old := gp.stack
	used := old.hi - gp.sched.sp
  // 创建新栈
	new := stackalloc(uint32(newsize))
	...
  // 把旧栈的内容拷贝至新栈
	memmove(unsafe.Pointer(new.hi-ncopy), unsafe.Pointer(old.hi-ncopy), ncopy)
  ...
  // 调整指针
  adjustctxt(gp, &adjinfo)
  // groutine里记录新栈的边界
  gp.stack = new
  ...
  // 释放旧栈
  stackfree(old)
}
```

新栈的初始化和数据的复制是一个比较简单的过程，整个过程中最复杂的地方是将指向源栈中内存的指针调整为指向新的栈，这一步完成后就会释放掉旧栈的内存空间了。

我们可以通过修改一下源码文件`runtime.stack.go`，把常量`stackDebug`的值修改为1，使用命令**go build -gcflags -S main.go** 运行文章最开始的那个例子，观察栈的初始化和扩容过程：

```shell
stackalloc 2048
stackcacherefill order=0
  allocated 0xc000030000
...
copystack gp=0xc000000180 [0xc000030000 0xc0000306e0 0xc000030800] -> [0xc00005c000 0xc00005cee0 0xc00005d000]/4096
stackfree 0xc000030000 2048
stack grow done
...
copystack gp=0xc000000180 [0xc00005c000 0xc00005c890 0xc00005d000] -> [0xc000064000 0xc000065890 0xc000066000]/8192
stackfree 0xc00005c000 4096
stack grow done
...
copystack gp=0xc000000180 [0xc000064000 0xc000065890 0xc000066000] -> [0xc00006c000 0xc00006f890 0xc000070000]/16384
stackfree 0xc000064000 8192
stack grow done
...
copystack gp=0xc000000180 [0xc00006c000 0xc00006f890 0xc000070000] -> [0xc000070000 0xc000077890 0xc000078000]/32768
stackfree 0xc00006c000 16384
stack grow done

```

### 栈缩容

在`goroutine`运行的过程中，如果栈区的空间使用率不超过1/4，那么在**垃圾回收**的时候使用`runtime.shrinkstack`进行栈缩容，当然进行缩容前会执行一堆前置检查，都通过了才会进行缩容

```go
func shrinkstack(gp *g) {
	...
	oldsize := gp.stack.hi - gp.stack.lo
	newsize := oldsize / 2
	if newsize < _FixedStack {
		return
	}
	avail := gp.stack.hi - gp.stack.lo
	if used := gp.stack.hi - gp.sched.sp + _StackLimit; used >= avail/4 {
		return
	}

	copystack(gp, newsize)
}
```

如果要触发栈的缩容，新栈的大小会是原始栈的一半，不过如果新栈的大小低于程序的最低限制 2KB，那么缩容的过程就会停止。缩容也会调用扩容时使用的 `runtime.copystack` 函数开辟新的栈空间，将旧栈的数据拷贝到新栈以及调整原来指针的指向。

在我们上面的那个例子里，当`main`函数里的其他函数执行完后，只有`main`函数还在栈区的空间里，如果这个时候系统进行垃圾回收就会对这个`goroutine`的栈区进行缩容。在这里我们可以在程序里通过调用`runtime.GC`，强制系统进行垃圾回收，来试验看一下栈缩容的过程和效果：

```go
func main() {
   var x [10]int
   println(&x)
   a(x)
   runtime.GC()
   println(&x)
}
```

执行命令**go build -gcflags -S main.go**后会看到类似下面的输出。

```
...
shrinking stack 32768->16384
stackalloc 16384
  allocated 0xc000076000
copystack gp=0xc000000180 [0xc00007a000 0xc000081e60 0xc000082000] -> [0xc000076000 0xc000079e60 0xc00007a000]/16384
...

```







**参考文章：**https://juejin.cn/post/6871550379432574990