---
title: 剑指 Offer 14- II. 剪绳子 II
excerpt: 所在模块：array
tags: [go,array]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>剑指 Offer 14- II. 剪绳子 II</font>

#### 题目描述：

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m - 1] 。请问 k[0]*k[1]*...*k[m - 1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

 

示例 1：

输入: 2
输出: 1
解释: 2 = 1 + 1, 1 × 1 = 1
示例 2:

输入: 10
输出: 36
解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36

#### 思路:

```
时间复杂度：O(),空间复杂度O()
```



#### 代码:

```golang
func cuttingRope(n int) int {
    var res int
    res = 1
    if  n<=3{
        return n-1
    }
    if n == 4{
        return 4
    }
    for n>4{
        res *= 3
        res %= 1000000007
        n -= 3
    }
    return (res *n)%1000000007
}

```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：1.8 MB, 在所有 Go 提交中击败了58.33%的用户
</p>



