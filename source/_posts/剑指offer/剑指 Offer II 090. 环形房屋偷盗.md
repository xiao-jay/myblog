---
title: 剑指 Offer II 090. 环形房屋偷盗
excerpt: 所在模块：dp
tags: [c,array]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>环形房屋偷盗</font>

#### 题目描述：

一个专业的小偷，计划偷窃一个环形街道上沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 围成一圈 ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警 。

给定一个代表每个房屋存放金额的非负整数数组 nums ，请计算 在不触动警报装置的情况下 ，今晚能够偷窃到的最高金额。

 

示例 1：

输入：nums = [2,3,2]
输出：3
解释：你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
示例 2：

输入：nums = [1,2,3,1]
输出：4
解释：你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。
     偷窃到的最高金额 = 1 + 3 = 4 。
示例 3：

输入：nums = [0]
输出：0


提示：

1 <= nums.length <= 100
0 <= nums[i] <= 1000

#### 思路:

```
时间复杂度：O(n^2),空间复杂度O()
```

首个和最后一个只能取一个

#### 代码:

```golang
func rob(nums []int) int {
    if len(nums) == 1{
        return nums[0]
    }else if len(nums) == 2{
        return max(nums[0],nums[1])
    }
    return max(_rob(nums[:len(nums)-1]), _rob(nums[1:]))
}

func _rob(nums []int) int {
    if len(nums) == 0 {
        return 0
    }
    if len(nums) == 1 {
        return nums[0]
    }
    dp := make([]int, len(nums))
    dp[0] = nums[0]
    dp[1] = max(nums[0], nums[1])
    for i := 2; i < len(nums); i++ {
        dp[i] = max(dp[i-2] + nums[i], dp[i-1])
    }
    return dp[len(nums)-1]
}

func max(x, y int) int {
    if x > y {
        return x
    }
    return y
}


```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：1.9 MB, 在所有 Go 提交中击败了46.75%的用户
</p>

