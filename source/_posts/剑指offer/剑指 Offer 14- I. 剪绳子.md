---
title: 剑指 Offer 14- I. 剪绳子
excerpt: 所在模块：array
tags: [go,array]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>剪绳子</font>

#### 题目描述：

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 k[0]*k[1]*...*k[m-1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

示例 1：

输入: 2
输出: 1
解释: 2 = 1 + 1, 1 × 1 = 1
示例 2:

输入: 10
输出: 36
解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36
提示：

2 <= n <= 58
注意：本题与主站 343 题相同：https://leetcode-cn.com/problems/integer-break/

通过次数231,154提交次数402,798

#### 思路:

```
时间复杂度：O(n),空间复杂度O(1)
```

只有均匀分成n份才是最大的

#### 代码:

```golang
func cuttingRope(n int) int {
    res := 1
    for i:=2;i<=n;i++{
        tmp := 1
        tmpNum := n
        for j:=i;j>=1;j--{
            a := tmpNum/j
            tmp *= a
            tmpNum -= a
        }
        res = max(res,tmp)
    }
    return res
}

func max(a,b int)int{
    if a>b{
        return a
    }
    return b
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：1.9 MB, 在所有 Go 提交中击败了23.74%的用户
</p>

