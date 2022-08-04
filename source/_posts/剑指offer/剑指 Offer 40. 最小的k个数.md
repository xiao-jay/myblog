---
title: 剑指 Offer 40. 最小的k个数
excerpt: 所在模块：array
tags: [go,array]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>最小的k个数</font>

#### 题目描述：

输入整数数组 arr ，找出其中最小的 k 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

 

示例 1：

输入：arr = [3,2,1], k = 2
输出：[1,2] 或者 [2,1]
示例 2：

输入：arr = [0,1,2,1], k = 1
输出：[0]


限制：

0 <= k <= arr.length <= 10000
0 <= arr[i] <= 10000

#### 思路:

```
时间复杂度：O(nlogn),空间复杂度O(n)
```

典型堆排序题

#### 代码:

```golang
func getLeastNumbers(arr []int, k int) []int {
    n := len(arr)-1
    for i:=n/2;i>=0;i--{
        HeapSortMin(arr,i,n)
    }

    for i:=0;i<k;i++{
        arr[0],arr[n-i] = arr[n-i],arr[0]
        HeapSortMin(arr,0,n-i-1)
    }
    //fmt.Println(arr)
    return arr[n-k+1:]
}

func HeapSortMin(arr []int,start int,end int){
    for{
        child := start*2 +1
        if child >end{
            return
        }
        if child < end &&arr[child] > arr[child+1]{
            child++
        }

        if arr[start] < arr[child]{
            return
        }

        arr[start],arr[child] = arr[child],arr[start]
        start = child
    }
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：24 ms, 在所有 Go 提交中击败了69.88%的用户<br>
   内存消耗：6.4 MB, 在所有 Go 提交中击败了64.23%的用户
</p>



