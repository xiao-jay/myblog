---
title: 剑指 Offer 04. 二维数组中的查找
excerpt: 所在模块：array
tags: [c,array]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>04. 二维数组中的查找</font>

#### 题目描述：

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

 

示例:

现有矩阵 matrix 如下：

[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]

给定 target = 5，返回 true。

给定 target = 20，返回 false。

 

限制：

0 <= n <= 1000

0 <= m <= 1000

#### 思路:

从最右上开始，如果比target大就往左，小就往下

#### 代码:

```golang
bool findNumberIn2DArray(int** matrix, int matrixSize, int* matrixColSize, int target){
    if(matrixSize==0 || *matrixColSize==0){
        return false;
    }
    int i=0;
    int j=*matrixColSize-1;
    while(i>=0 && i<matrixSize &&j>=0 &&j<*matrixColSize){
        if(matrix[i][j] == target){
            return true;
        }else if(matrix[i][j] < target){
            i++;
        }else{
            j--;
        }

    }
    return false;
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：32 ms, 在所有 C 提交中击败了99.19% 的用户<br>
   内存消耗：8 MB, 在所有 C 提交中击败了99.73% 的用户
</p>

