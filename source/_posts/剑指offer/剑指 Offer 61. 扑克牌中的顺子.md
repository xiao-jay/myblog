---
title: 剑指 Offer 61. 扑克牌中的顺子
excerpt: 所在模块：array
tags: [go,array]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>扑克牌中的顺子</font>

#### 题目描述：

从若干副扑克牌中随机抽 5 张牌，判断是不是一个顺子，即这5张牌是不是连续的。2～10为数字本身，A为1，J为11，Q为12，K为13，而大、小王为 0 ，可以看成任意数字。A 不能视为 14。

 

示例 1:

输入: [1,2,3,4,5]
输出: True


示例 2:

输入: [0,0,1,2,5]
输出: True


限制：

数组长度为 5 

数组的数取值为 [0, 13] .

#### 思路:

```
时间复杂度：O(n),空间复杂度O(n)
```

判断一个顺子里面的个数加上0的个数是不是5

#### 代码:

```golang
func isStraight(nums []int) bool {
    sort.Ints(nums)
    cnt0,cnt1 := 0,1
    mp := make(map[int]bool,0)
    i :=0
    for ;i<len(nums);i++{
        if nums[i] == 0{
            cnt0++
        }else{
            break
        }
    }
    for j:=i;j<len(nums);j++{
        mp[nums[j]] = true
    }
    for j:=1;j<5;j++{
        if mp[nums[i]+j]{
            cnt1++
        }
    }
    //fmt.Println(cnt0,cnt1)
    if cnt1+cnt0 == 5{
        return true
    }else{
        return false
    }

}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：1.9 MB, 在所有 Go 提交中击败了14.92%的用户
</p>
