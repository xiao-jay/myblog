---
title: 剑指 Offer 43. 1～n 整数中 1 出现的次数
excerpt: 所在模块：dp
tags: [go,dp]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>1～n 整数中 1 出现的次数</font>

#### 题目描述：

输入一个整数 n ，求1～n这n个整数的十进制表示中1出现的次数。

例如，输入12，1～12这些整数中包含1 的数字有1、10、11和12，1一共出现了5次。

 

示例 1：

输入：n = 12
输出：5
示例 2：

输入：n = 13
输出：6


限制：

1 <= n < 2^31

#### 思路:

```
时间复杂度：O(n),空间复杂度O()
```



#### 代码:

```golang
func countDigitOne(n int) int {
    var res int
    d := dgt(n)
    for j:=1; j<=d; j++{

        p := int(math.Pow(10,float64(j-1)))
        l := n/p/10
        r := n % p
        dj := n /p %10
        res +=   l*p
        if dj >1{
            res += p
        }else if dj ==1{
            res += r+1
        }
    }
    

    return res

}

func dgt(n int)int{
    var cnt int
    for n != 0{
        cnt++
        n/=10
    }
    return cnt
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：1.8 MB, 在所有 Go 提交中击败了40.91%的用户
</p>
