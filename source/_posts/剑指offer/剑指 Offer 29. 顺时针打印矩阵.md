---
title: 剑指 Offer 04. 二维数组中的查找
excerpt: 所在模块：array
tags: [go,array]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>螺旋矩阵</font>

#### 题目描述：

给你一个 m 行 n 列的矩阵 matrix ，请按照 顺时针螺旋顺序 ，返回矩阵中的所有元素。

 

示例 1：


输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
示例 2：


输入：matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
输出：[1,2,3,4,8,12,11,10,9,5,6,7]


提示：

m == matrix.length
n == matrix[i].length
1 <= m, n <= 10
-100 <= matrix[i][j] <= 100

#### 思路:

```
时间复杂度：O(n^2),空间复杂度O(n^2)
```



#### 代码:

```golang
func spiralOrder(matrix [][]int) []int {
    var res []int
    for len(matrix) != 0{
        res = append(res,matrix[0]...)
        matrix = matrix[1:]
        if len(matrix) == 0{
            break
        }
        m,n := len(matrix),len(matrix[0])
        tmp := make([][]int, n)
            for i:=0;i<m;i++{
                for j:=0;j<n;j++{
                    tmp[j] = append(tmp[j],matrix[i][n-j-1])
                }
            }
            matrix = tmp
        
        //fmt.Println(matrix)
    }
    return res
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：32 ms, 在所有 Go 提交中击败了5.26%的用户<br>
   内存消耗：7.1 MB, 在所有 Go 提交中击败了5.54%的用户
</p>



