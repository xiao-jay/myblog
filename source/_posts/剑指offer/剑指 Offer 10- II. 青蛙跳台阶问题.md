---
title: 剑指 Offer 10- II. 青蛙跳台阶问题
excerpt: 所在模块：dp
tags: [go,dp]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>青蛙跳台阶问题</font>

#### 题目描述：

一只青蛙一次可以跳上1级台阶，也可以跳上2级台阶。求该青蛙跳上一个 n 级的台阶总共有多少种跳法。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

示例 1：

输入：n = 2
输出：2
示例 2：

输入：n = 7
输出：21
示例 3：

输入：n = 0
输出：1
提示：

0 <= n <= 100
注意：本题与主站 70 题相同：https://leetcode-cn.com/problems/climbing-stairs/

#### 思路:

```
时间复杂度：O(n),空间复杂度O(1)
```

dp

#### 代码:

```golang
func numWays(n int) int {

    var f []int
    if(n==1 || n == 0){
        return 1
    }else if(n == 2){
        return 2
    }
    f = append(f,1,2)
    for i:=2; i<=n;i++{
        f = append(f,(f[i-1]+f[i-2])% 1000000007)
    }
    return f[n-1]
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：1.9 MB, 在所有 Go 提交中击败了20.91%
</p>

