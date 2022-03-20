---
title: map
---

map分为hmap和bmap，是一个哈希表，是一个数组，golang用链表法解决哈希冲突，如果溢出会弄一个溢出bucket放进去，之后迁移进去，溢出把oldbucket放进newbucket，map类型是[int64]int8，key是int64，value是int8，所以是放七个key，然后连续7个value放在同一个byte里面节省空间

### 1、为什么map不可以寻址，map是怎么存储的

1. 如果字典的元素不存在，则返回零值，而零值是不可变对象，如果能寻址问题就大了。
2. 而如果字典的元素存在，考虑到 Go 中 map 实现中元素的地址是变化的，这意味着寻址的结果也是无意义的。
