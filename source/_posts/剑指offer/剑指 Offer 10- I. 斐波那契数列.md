---
title: 剑指 Offer 10- I. 斐波那契数列
excerpt: 所在模块：动态规划
tags: [go,动态规划]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>斐波那契数列</font>

#### 题目描述：

写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项（即 F(N)）。斐波那契数列的定义如下：

F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

 

示例 1：

输入：n = 2
输出：1
示例 2：

输入：n = 5
输出：5


提示：

0 <= n <= 100
通过次数393,397提交次数1,087,540

#### 思路:

```
时间复杂度：O(100),空间复杂度O()
```

提前计算好菲波纳且数列的值

#### 代码:

```golang
var mp map[int]int
func fib(n int) int {
    if len(mp) == 0{
        mp = make(map[int]int, 0)
    }else{
        return mp[n]
    }
    mp[0],mp[1] = 0,1
     
    for i:=2;i<=100;i++{
        mp[i] = (mp[i-1]+mp[i-2])%1000000007
    }
    return mp[n]
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：1.8 MB, 在所有 Go 提交中击败了83.53%的用户
</p>

