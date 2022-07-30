---
title: 剑指 Offer 46. 把数字翻译成字符串
excerpt: 所在模块：dp
tags: [go,dp]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>把数字翻译成字符串</font>

#### 题目描述：

给定一个数字，我们按照如下规则把它翻译为字符串：0 翻译成 “a” ，1 翻译成 “b”，……，11 翻译成 “l”，……，25 翻译成 “z”。一个数字可能有多个翻译。请编程实现一个函数，用来计算一个数字有多少种不同的翻译方法。

 

示例 1:

输入: 12258
输出: 5
解释: 12258有5种不同的翻译，分别是"bccfi", "bwfi", "bczi", "mcfi"和"mzi"


提示：

0 <= num < 231

#### 思路:

```
时间复杂度：O(len(n)),空间复杂度O(n)
```

有点像斐波拉且数列

#### 代码:

```golang
func translateNum(num int) int {
    snum := strconv.Itoa(num)
    n :=  len(snum)
    dp := make([]int, n+1)
    dp[0] = 1
    for i:=1;i<n;i++{
        if (snum[i]-'0')+(snum[i-1]-'0')*10 <=25 && snum[i-1]!= '0'{
            if i==1{
                dp[i] = 2*dp[i-1]
            }else{
                dp[i] = dp[i-1] + dp[i-2]
            }
        }else{
            dp[i] = dp[i-1]
        }
    }
    return dp[n-1]
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：1.8 MB, 在所有 Go 提交中击败了35.22%的用户
</p>



