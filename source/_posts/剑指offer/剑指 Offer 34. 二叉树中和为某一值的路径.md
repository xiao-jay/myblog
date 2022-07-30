---
title: 剑指 Offer 34. 二叉树中和为某一值的路径
excerpt: 所在模块：二叉树
tags: [go,二叉树]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>二叉树中和为某一值的路径</font>

#### 题目描述：

给你二叉树的根节点 root 和一个整数目标和 targetSum ，找出所有 从根节点到叶子节点 路径总和等于给定目标和的路径。

叶子节点 是指没有子节点的节点。

 

示例 1：



输入：root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
输出：[[5,4,11,2],[5,8,4,5]]
示例 2：



输入：root = [1,2,3], targetSum = 5
输出：[]
示例 3：

输入：root = [1,2], targetSum = 0
输出：[]


提示：

树中节点总数在范围 [0, 5000] 内
-1000 <= Node.val <= 1000
-1000 <= targetSum <= 1000

#### 思路:

```
时间复杂度：O(n),空间复杂度O(n)
```

dfs遍历然后到根节点就判断是否等于target

#### 代码:

```golang
func pathSum(root *TreeNode, targetSum int) [][]int {
    var res [][]int
    var dfs func(root *TreeNode,tmp []int,sum int)
    dfs = func(root *TreeNode,tmp []int,sum int){
        if root == nil{
            return
        }
        tmp = append(tmp,root.Val)
        sum += root.Val
        dfs(root.Left,tmp,sum)
        dfs(root.Right,tmp,sum)
         if root.Left == nil && root.Right == nil{
            if sum == targetSum{
                tmp2 := make([]int,len(tmp))
                copy(tmp2,tmp)
                res = append(res,tmp2)
            }
        }
    }
    dfs(root,[]int{},0)
    return res
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：4 ms, 在所有 Go 提交中击败了86.83%的用户<br>
   内存消耗：4.4 MB, 在所有 Go 提交中击败了41.69%的用户
</p>



