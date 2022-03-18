---
title: MT4 直方图内最大矩形
excerpt: 所在模块：array
tags: [golang,array,单调栈]
categories: 牛客刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>直方图内最大矩形</font>

#### 题目描述：

给定一个数组heights，长度为n，height[i]是在第i点的高度，那么height[i]表示的直方图，能够形成的最大矩形是多少?

1.每个直方图宽度都为1

2.直方图都是相邻的

3.如果不能形成矩形，返回0即可

4.保证返回的结果不会超过231-1

数据范围:

0 <= heights[i] <= 10^40<=*h**e**i**g**h**t**s*[*i*]<=104

0 <= heights.length <=10^50<=*h**e**i**g**h**t**s*.*l**e**n**g**t**h*<=105

如输入[3,4,7,8,1,2]，那么如下:

![img](https://uploadfiles.nowcoder.com/images/20211106/301499_1636186669482/D4212B8149018E3A3A42996A461EA3FC)

## 示例1

输入：

```
[3,4,7,8,1,2]
```

复制

返回值：

```
14
```

复制

## 示例2

输入：

```
[1,7,3,2,4,5,8,2,7]
```

返回值：

```
16
```

#### 思路:

```
时间复杂度：O(n),空间复杂度：O(n)
```

单调栈思路做，如果一个数比之前的大就入栈，确保栈里面的数据是有序的，然后遇到比自己小的就出栈直到栈有序，这里有个技巧是加入哨兵就可以简化判断边界情况的复杂度。

#### 代码:

```golang
package main

func largestRectangleArea( heights []int ) int {
    
    var  stack []int
    if heights == nil{
        return 0
    }
    // 插入一个数防止栈空
    stack = append(stack, 0)
    heights = append([]int{-1}, heights...)
    maxNum := heights[1]
    heights = append(heights, -1)
    n := len(heights)
    secondTop := 0
    for i:=1;i<n;i++{
        top := stack[len(stack)-1]
        if len(stack )>1{
            secondTop = stack[len(stack)-2]
        }else{
            secondTop = top
        }
            for heights[top]>heights[i]{
                maxNum = max((i - secondTop-1) * heights[top], maxNum)
                stack = stack[:len(stack)-1]
                top = stack[len(stack)-1]
                if len(stack)>1{
                    secondTop = stack[len(stack)-2]
                }else{
                    secondTop = top
                }
            }
        stack = append(stack, i)
    }
    return maxNum
}

func max(a ,b int)int{
    if a>b{
        return a
    }
    return b
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   运行时间：35ms超过56.52% 用Go提交的代码<br>
   占用内存：10292KB超过39.13%用Go提交的代码
</p>








