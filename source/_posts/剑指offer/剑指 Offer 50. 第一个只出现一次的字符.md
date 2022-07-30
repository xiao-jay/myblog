---
title: 剑指 Offer 50. 第一个只出现一次的字符
excerpt: 所在模块：string
tags: [go,string]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>第一个只出现一次的字符</font>

#### 题目描述：

在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 s 只包含小写字母。

示例 1:

输入：s = "abaccdeff"
输出：'b'
示例 2:

输入：s = "" 
输出：' '


限制：

0 <= s 的长度 <= 50000

#### 思路:

```
时间复杂度：O(n),空间复杂度O()
```



#### 代码:

```golang
func firstUniqChar(s string) byte {
    mp := make(map[byte]int,0)
    for i:=0;i<len(s);i++{
        mp[s[i]]++
    }
    for i:=0;i<len(s);i++{
        if mp[s[i]] == 1{
            return s[i]
        }
    }
    return ' '
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：40 ms, 在所有 Go 提交中击败了33.88%的用户<br>
   内存消耗：5.2 MB, 在所有 Go 提交中击败了44.69%的用户
</p>



