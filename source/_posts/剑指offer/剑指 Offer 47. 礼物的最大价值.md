---
title: 剑指 Offer 47. 礼物的最大价值
excerpt: 所在模块：dp
tags: [go,dp]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>礼物的最大价值</font>

#### 题目描述：

在一个 m*n 的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于 0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格、直到到达棋盘的右下角。给定一个棋盘及其上面的礼物的价值，请计算你最多能拿到多少价值的礼物？

 

示例 1:

输入: 
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
输出: 12
解释: 路径 1→3→5→2→1 可以拿到最多价值的礼物


提示：

0 < grid.length <= 200
0 < grid[0].length <= 200

#### 思路:

```
时间复杂度：O(n^2),空间复杂度O(n)
```

动态规划

#### 代码:

```golang
func maxValue(grid [][]int) int {
    m,n := len(grid),len(grid[0])
    if m== 1 && n==1{
        return grid[0][0]
    }
    for i:=1;i<n;i++{
        grid[0][i] += grid[0][i-1]
    }
    for i:=1;i<m;i++{
        grid[i][0] += grid[i-1][0]
    }
    for i:=1;i<m;i++{
        for j:=1;j<n;j++{
            grid[i][j] += max(grid[i-1][j], grid[i][j-1])
        }
    }
    return grid[m-1][n-1]
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
   执行用时：4 ms, 在所有 Go 提交中击败了98.05%的用户<br>
   内存消耗：3.7 MB, 在所有 Go 提交中击败了100.00%的用户
</p>


