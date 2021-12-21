---
title: 我为什么不用golang写PAT了
excerpt: 讲讲我为什么开始的时候要用go语言写算法后来又放弃
banner_img: /img/极光.jpg
categories: PAT刷题
---



#### 先说说我为什么要用golang写算法题

- 第一是想让我熟练使用golang语言的各种库，熟悉基础语法，比如go没while循环。。。
- 第二是go语言的性能比较高效，最快能与c语言相同，并且与c语言比较，golang库完善编写比如排序只需要调用函数就行，比较简单，c语言就要用到c++的东西，挺麻烦的，还要手写cmp函数。

#### 再说说为什么我不再用golang写PAT了

- 第一是因为你网上找不到用golang写PAT的人（力扣倒是挺多的，而且力扣比较人性化，会把测试用例错误答案和正确答案给你列出来，而且有社区，但是PAT锻炼了你的独立思考能力，自己思考错哪边了），就算写错了自己找不出原因的时候也没地方借鉴，而且PAT在考试的时候**只支持c/c++调试环境**
- 第二因为golang他速度只是最快能到c，有些时候会不达到，然后PAT他的**运行时间卡的比较紧**，举例一道简单题PAT乙级 <a herf="sets/994805260223102976/problems/994805289432236032)"> 1032挖掘技术那家强 </a>

```golang
package main

import "fmt"

func main()  {
    var n,a,b,maxi int
    var score [120000]int
    fmt.Scanf("%d",&n)
 
    for i:=0;i<n;i++{
        fmt.Scanf("%d %d",&a,&b)
        score[a]+=b
    }
    for i:=1;i<=n;i++{
        if score[maxi]<score[i]{
            maxi = i
        }
    }
    fmt.Println(maxi,score[maxi])
}

```



这道题用golang写最后一个测试点会是运行超时，如果要做的话得考虑用并发实现了，这样做一道题太费时间了，考场里面用这种方法不太可行，也用golang写了30多道题目了，锻炼效果应该差不多，现在开始用c语言写题目温习一下c语言技巧和排序的使用，加油考试吧，虽然只是个对别人来说简简单单的乙级。

