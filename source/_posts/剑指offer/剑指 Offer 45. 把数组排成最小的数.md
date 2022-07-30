---
title: 剑指 Offer 45. 把数组排成最小的数
excerpt: 所在模块：array
tags: [go,array]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>把数组排成最小的数</font>

#### 题目描述：

输入一个非负整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。

 

示例 1:

输入: [10,2]
输出: "102"
示例 2:

输入: [3,30,34,5,9]
输出: "3033459"


提示:

0 < nums.length <= 100
说明:

输出结果可能非常大，所以你需要返回一个字符串而不是整数
拼接起来的数字可能会有前导 0，最后结果不需要去掉前导 0

#### 思路:

```
时间复杂度：O(nlogn),空间复杂度O(n)
```

排序的方式比较有创新点，排序方式为a+b < b+a

#### 代码:

```golang
import "strconv"
import "strings"
import "bufio"
import "os"
import "fmt"
import "io"
import "sort"
func minNumber(nums []int) string {
    var res []string
    for i:=0;i<len(nums); i++{
        s := strconv.Itoa(nums[i])
        res = append(res,s)
    }
    sort.Slice(res,func(i,j int)bool{
        a,_ := strconv.Atoi(res[i]+res[j])
        b,_ := strconv.Atoi(res[j] + res[i])
        return a<b
    })
    return strings.Join(res,"")
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：2.6 MB, 在所有 Go 提交中击败了51.65%的用户
</p>
