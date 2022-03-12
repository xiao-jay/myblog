---
title: 剑指 Offer 39. 数组中出现次数超过一半的数字
excerpt: 所在模块：array
tags: [go,array]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>数组中出现次数超过一半的数字</font>

#### 题目描述：

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。

 

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

 

示例 1:

输入: [1, 2, 3, 2, 2, 2, 5, 4, 2]
输出: 2


限制：

1 <= 数组长度 <= 50000

#### 思路:

#### 代码:

```golang
func majorityElement(nums []int) int {
    var res int
    num := 0
    for _,v := range nums{
        if res ==0{
            res = v
        }else{
            if res == v{
                num ++
            }else{
                if(num >0){
                    num--
                }else{
                    res = v
                }
            }
        }

    }
    return res
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：12 ms, 在所有 Go 提交中击败了94.86%的用户<br>
   内存消耗：5.9 MB, 在所有 Go 提交中击败了99.43%的用户
</p>



