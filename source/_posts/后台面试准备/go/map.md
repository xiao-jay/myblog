---
title: map
---

map分为hmap和bmap，是一个哈希表，是一个数组，golang用链表法解决哈希冲突，如果溢出会弄一个溢出bucket放进去，之后迁移进去，溢出把oldbucket放进newbucket，map类型是[int64]int8，key是int64，value是int8，所以是放七个key，然后连续7个value放在同一个byte里面节省空间

