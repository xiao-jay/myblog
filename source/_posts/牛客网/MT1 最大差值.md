---
title: MT1 最大差值
excerpt: 所在模块：array
tags: [c++,array]
categories: 牛客刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>最大差值</font>

#### 题目描述：

有一个长为 n 的数组 A ，求满足 0 ≤ a ≤ b < n 的 A[b] - A[a] 的最大值。

给定数组 A 及它的大小 n ，请返回最大差值。

数据范围： 2 < n \le 2*10^5\2<*n*≤2∗105 ，数组中的值满足 0 \le |val| \le 5*10^8 \0≤∣*v**a**l*∣≤5∗108 

## 示例1

输入：

```
[5,1],2
```

复制

返回值：

```
0
```

复制

## 示例2

输入：

```
[5,6],2
```

复制

返回值：

```
1
```

#### 思路:

```
时间复杂度：O(n),空间复杂度：O(1)
```

模拟即可

#### 代码:

```golang
class Solution {
public:
    /**
     * 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
     *
     * 
     * @param A int整型vector 
     * @param n int整型 
     * @return int整型
     */
    int getDis(vector<int>& A, int n) {
        // write code here
        if (A.size() <n){
            n = A.size();
        }
        int min = A[0];
        int res = 0;
        for (int i=1; i<n;i++){
            if(min > A[i]){
                min = A[i];
            }
            res = max(res, A[i]-min);
        }
        return res;
    }
};
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   运行时间：55ms,超过64.24% 用C++提交的代码<br>
   占用内存：8060KB ,超过33.24%用C++提交的代码
</p>





