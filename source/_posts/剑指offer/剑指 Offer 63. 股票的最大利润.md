---
title: 剑指 Offer 63. 股票的最大利润
excerpt: 所在模块：dp
tags: [go,dp]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>股票的最大利润</font>

#### 题目描述：

假设把某股票的价格按照时间先后顺序存储在数组中，请问买卖该股票一次可能获得的最大利润是多少？

 

示例 1:

输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。
示例 2:

输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。


限制：

0 <= 数组长度 <= 10^5

#### 思路:

```
时间复杂度：O(n),空间复杂度O(1)
```



#### 代码:

```golang
func maxProfit(prices []int) int {
    if len(prices) == 0{
        return 0
    }
    maxNum,minNum := prices[0],prices[0]
    n := len(prices)
    res := 0
    for i:=1;i<n;i++{
        if prices[i]>maxNum{
            maxNum = prices[i]
        }
        if prices[i] < minNum{
            maxNum = prices[i]
            minNum = prices[i]
        }
        res = max(res,maxNum-minNum)
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
   执行用时：4 ms, 在所有 Go 提交中击败了90.64%的用户<br>
   内存消耗：2.9 MB, 在所有 Go 提交中击败了100.00%的用户
</p>



