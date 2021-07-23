---
title: 力扣九十三：复原IP地址
excerpt: 所在模块：string
tags: [golang,string]
categories: 力扣刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>复原IP地址</font>

#### 题目描述：

给定一个只包含数字的字符串，复原它并返回所有可能的 IP 地址格式。

有效的 IP 地址 正好由四个整数（每个整数位于 0 到 255 之间组成，且不能含有前导 0），整数之间用 '.' 分隔。

例如："0.1.2.201" 和 "192.168.1.1" 是 有效的 IP 地址，但是 "0.011.255.245"、"192.168.1.312" 和 "192.168@1.1" 是 无效的 IP 地址。

 

示例 1：

输入：s = "25525511135"
输出：["255.255.11.135","255.255.111.35"]
示例 2：

输入：s = "0000"
输出：["0.0.0.0"]
示例 3：

输入：s = "1111"
输出：["1.1.1.1"]
示例 4：

输入：s = "010010"
输出：["0.10.0.10","0.100.1.0"]
示例 5：

输入：s = "101023"
输出：["1.0.10.23","1.0.102.3","10.1.0.23","10.10.2.3","101.0.2.3"]


提示：

0 <= s.length <= 3000
s 仅由数字组成

#### 思路:

##### 学到的东西

1、搞清楚了切片和数组的区别，切片要导入的话得用append，没有大小限制

2、s[i]-'0'出来的结果是byte类型的

3、要重置一个字符串型的变量，var一个新的string变量，然后要它等于新的string变量

4、要返回一个空的字符串切片，return []string{}

5、要用len求数字的长度，要用strconv.Itoa把数字转化成字符串才能求

思路：

第一步先求长度符不符合标准，第二步求有无非法字符，第三步开始正式处理，这道题好像是一串数字，在里面加上3个点，然后判断被三个点隔开的4个数字符不符合标准，把4个数字取出来，然后分别判断是否小于255，然后判断这4个数字的长度和s的长度一不一样，如果不一样说明数字前面有0，就不符合条件

#### 代码:

```golang
import "strconv"
var s_len int
func restoreIpAddresses(s string) []string {
    var s1 ,s3 string
    var s2 []string
    var n1,n2,n3,n4,sum_len int
    s_len=len(s)
    //如果长度不达标
    if s_len<4 || s_len>12{
        return []string{}
    }
    //如果出现非法字符
    for i:=0;i<s_len;i++{
        if s[i]<'0' || s[i]>'9'{
            return []string{}
        }
    }
    for i:=0;i<s_len-3&&i<=2;i++{
        for j:=i+1;j<s_len-2&&j-i<=3;j++{
            for k:=j+1;k<s_len-1&&k-j<=3;k++{
                n1,n2,n3,n4,sum_len=stio(s,i,j,k)
                if n1<=255&&n2<=255&&n3<=255&&n4<=255&&sum_len==s_len{
                    for m:=0;m<s_len;m++{
                        s1=s1+string(s[m])
                        if m==i||m==j||m==k{
                            s1=s1+"."
                        }
                    }
                    s2=append(s2,s1)
                    //重置s1
                    s1=s3
                }
                n1,n2,n3=0,0,0

            }
        }
    }
    return s2
}
func stio(s string,a int,b int ,c int) (int,int,int,int,int) {
    var n1,n2,n3,n4 int
    var sum_len int
    for i:=0;i<=a;i++{
        n1=n1*10+int(s[i]-'0')
    }
    for i:=a+1;i<=b;i++{
        n2=n2*10+int(s[i]-'0')
    }
    for i:=b+1;i<=c;i++{
        n3=n3*10+int(s[i]-'0')
    }
    for i:=c+1;i<s_len;i++{
        n4=n4*10+int(s[i]-'0')
    }
    
    sum_len=len(strconv.Itoa(n1))+len(strconv.Itoa(n2))+len(strconv.Itoa(n3))+len(strconv.Itoa(n4))
    return n1,n2,n3,n4,sum_len
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：2.1 MB, 在所有 Go 提交中击败了56.25%的用户
</p>

