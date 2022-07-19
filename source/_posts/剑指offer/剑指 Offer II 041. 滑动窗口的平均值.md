---
title: 剑指 Offer II 041. 滑动窗口的平均值
excerpt: 所在模块：array
tags: [go,array]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>滑动窗口的平均值</font>

#### 题目描述：

给定一个整数数据流和一个窗口大小，根据该滑动窗口的大小，计算滑动窗口里所有数字的平均值。

实现 MovingAverage 类：

MovingAverage(int size) 用窗口大小 size 初始化对象。
double next(int val) 成员函数 next 每次调用的时候都会往滑动窗口增加一个整数，请计算并返回数据流中最后 size 个值的移动平均值，即滑动窗口里所有数字的平均值。


示例：

输入：
inputs = ["MovingAverage", "next", "next", "next", "next"]
inputs = [[3], [1], [10], [3], [5]]
输出：
[null, 1.0, 5.5, 4.66667, 6.0]

解释：
MovingAverage movingAverage = new MovingAverage(3);
movingAverage.next(1); // 返回 1.0 = 1 / 1
movingAverage.next(10); // 返回 5.5 = (1 + 10) / 2
movingAverage.next(3); // 返回 4.66667 = (1 + 10 + 3) / 3
movingAverage.next(5); // 返回 6.0 = (10 + 3 + 5) / 3


提示：

1 <= size <= 1000
-105 <= val <= 105
最多调用 next 方法 104 次

#### 思路:

```
时间复杂度：O(size),空间复杂度O(n)
```



#### 代码:

```golang
type MovingAverage struct {
    nums []int
    i,j,size int
    
}


/** Initialize your data structure here. */
func Constructor(size int) MovingAverage {
    movingAverage := MovingAverage{}
    movingAverage.size = size
    return movingAverage

}


func (this *MovingAverage) Next(val int) float64 {
    n := len(this.nums)
    var res float64
    var sum float64
     this.nums = append(this.nums, val)
    if n<this.size{
        this.j++
        for i:=0;i<=n;i++{
            sum += float64(this.nums[i])
        }
        res = sum/(float64)(n+1)
        return res
    }else{
        this.i++
        this.j++
        for i:=this.i;i<this.j;i++{
            sum += float64(this.nums[i])
        }
        
        res = sum/(float64)(this.size)
        return res
    }
}


/**
 * Your MovingAverage object will be instantiated and called as such:
 * obj := Constructor(size);
 * param_1 := obj.Next(val);
 */
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：16 ms, 在所有 Go 提交中击败了70.54%的用户<br>
   内存消耗：7.3 MB, 在所有 Go 提交中击败了81.70%的用户
</p>



