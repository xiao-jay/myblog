---
title: 剑指 Offer II 091. 粉刷房子
excerpt: 所在模块：dp
tags: [golang,dp]
categories: 力扣刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>粉刷房子</font>

#### 题目描述：

假如有一排房子，共 n 个，每个房子可以被粉刷成红色、蓝色或者绿色这三种颜色中的一种，你需要粉刷所有的房子并且使其相邻的两个房子颜色不能相同。

当然，因为市场上不同颜色油漆的价格不同，所以房子粉刷成不同颜色的花费成本也是不同的。每个房子粉刷成不同颜色的花费是以一个 n x 3 的正整数矩阵 costs 来表示的。

例如，costs[0][0] 表示第 0 号房子粉刷成红色的成本花费；costs[1][2] 表示第 1 号房子粉刷成绿色的花费，以此类推。

请计算出粉刷完所有房子最少的花费成本。

 

示例 1：

输入: costs = [[17,2,17],[16,16,5],[14,3,19]]
输出: 10
解释: 将 0 号房子粉刷成蓝色，1 号房子粉刷成绿色，2 号房子粉刷成蓝色。
     最少花费: 2 + 5 + 3 = 10。
示例 2：

输入: costs = [[7,6,2]]
输出: 2


提示:

costs.length == n
costs[i].length == 3
1 <= n <= 100
1 <= costs[i][j] <= 20

#### 思路:

```
时间复杂度：O(n),空间复杂度O(n)
```

用动态规划做

#### 代码:

```golang
func minCost(costs [][]int) int {
    n := len(costs)
    dp1,dp2,dp3 := make([]int, len(costs)),make([]int, len(costs)),make([]int, len(costs))
    dp1[0],dp2[0], dp3[0] = costs[0][0],costs[0][1],costs[0][2]
    for i:=1; i<len(costs); i++{
        dp1[i] = min(dp2[i-1],dp3[i-1]) + costs[i][0]
        dp2[i] = min(dp1[i-1],dp3[i-1]) + costs[i][1]
        dp3[i] = min(dp1[i-1],dp2[i-1]) + costs[i][2]
    }
    return min(min(dp1[n-1],dp2[n-1]),dp3[n-1])
}

func min(a,b int)int{
    if a>b{
        return b
    }
    return a
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：4 ms, 在所有 Go 提交中击败了84.92%的用户<br>
   内存消耗：3 MB, 在所有 Go 提交中击败了56.98%的用户
</p>



