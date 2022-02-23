---
title；golang内存分配
---

go语言启动时会分配一块内存，然后切成小块进行管理，分成span，bitmap，arena，

arena：将内存分成8kb的页（page），一起组成起来称mspan，成为go内存管理的基本单元。

bitmap：标记堆区使用的映射表，记录哪些区保存对象，哪些区包含指针

spans:存放mspan指针，根据spans信息很容易找到mspan，可以在GC时更快速找到大块内存mspan。