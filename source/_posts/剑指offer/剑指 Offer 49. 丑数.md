---
title: 剑指 Offer 49. 丑数
excerpt: 所在模块：d p
tags: [c,dp]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### 丑数 II

#### 题目描述：

给你一个整数 n ，请你找出并返回第 n 个 丑数 。

丑数 就是只包含质因数 2、3 和/或 5 的正整数。

 

示例 1：

输入：n = 10 输出：12 解释：[1, 2, 3, 4, 5, 6, 8, 9, 10, 12] 是由前 10 个丑数组成的序列。 示例 2：

输入：n = 1 输出：1 解释：1 通常被视为丑数。

提示：

1 <= n <= 1690

#### 思路:

```
时间复杂度：O(n),空间复杂度O(n)
```

dp求三个值里面最小值

#### 代码:

```
func nthUglyNumber(n int) int {
    dp := make([]int, n+1)
    dp[1] = 1
    p2,p3,p5 := 1,1,1
    for i:=2;i<=n;i++{
        x2,x3,x5 := dp[p2]*2,dp[p3]*3,dp[p5]*5
        dp[i] = min(x2,min(x3,x5))
        if dp[i] == x2{
            p2++
        }
        if dp[i] == x3{
            p3++
        }
        if dp[i] == x5{
            p5++
        }
    }
    return dp[n]
}

func min(a,b int)int{
    if a<b{
        return a
    }
    return b
}
```

#### 代码效率：

执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户
内存消耗：4 MB, 在所有 Go 提交中击败了62.40%的用户
