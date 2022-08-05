---
title: 剑指 Offer 56 - I. 数组中数字出现的次数
excerpt: 所在模块：array
tags: [go,array]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>数组中数字出现的次数</font>

#### 题目描述：

一个整型数组 nums 里除两个数字之外，其他数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。

 

示例 1：

输入：nums = [4,1,4,6]
输出：[1,6] 或 [6,1]
示例 2：

输入：nums = [1,2,10,4,1,4,3,3]
输出：[2,10] 或 [10,2]


限制：

2 <= nums.length <= 10000

#### 思路:

```
时间复杂度：O(n),空间复杂度O(1)
```

题目还挺有意思，是升级版，要你更加深入使用抑或

#### 代码:

```golang
func singleNumbers(nums []int) []int {
    ret := 0
    for _,n := range nums{
        ret ^= n
    }
    a,b := 0,0
    p := 1
    for (p & ret) == 0{
        p *= 2
    }
    //fmt.Println(p)
    for _,n := range nums{
        if p & n != 0{
            //fmt.Println("a",n)
            a ^= n
        }else{
            //fmt.Println("b",n)
            b ^= n
        }
    }
    return []int{a,b}
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：12 ms, 在所有 Go 提交中击败了96.80%的用户<br>
   内存消耗：6.1 MB, 在所有 Go 提交中击败了66.50%的用户
</p>





