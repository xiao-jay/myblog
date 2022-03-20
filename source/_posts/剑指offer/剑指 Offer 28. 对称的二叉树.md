---
title: 剑指 Offer 28. 对称的二叉树
excerpt: 所在模块：二叉树
tags: [go,二叉树]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>对称的二叉树</font>

#### 题目描述：

请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

例如，二叉树 [1,2,2,3,4,4,3] 是对称的。

    1
   / \
  2   2
 / \ / \
3  4 4  3
但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:

    1
   / \
  2   2
   \   \
   3    3

 

示例 1：

输入：root = [1,2,2,3,4,4,3]
输出：true
示例 2：

输入：root = [1,2,2,null,3,null,3]
输出：false


限制：

0 <= 节点个数 <= 1000。

#### 思路:

```
时间复杂度：O(n),空间复杂度O(1)
```

用bfs思路做

#### 代码:

```golang
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func isSymmetric(root *TreeNode) bool {
    if root == nil || (root.Left == nil && root.Right == nil){
        return true
    }
    if root.Left == nil || root.Right == nil{
        return false
    }
    var stack1,stack2 []*TreeNode
    stack1 = append(stack1, root.Left)
    stack2 = append(stack2,root.Right)
    for len(stack1)!=0 && len(stack2) != 0{
        left := stack1[0]
        right := stack2[0]
        stack1 = stack1[1:]
        stack2 = stack2[1:]
        if left.Val != right.Val{
            return false
        }
        if left.Left != nil && right.Right != nil{
            stack1 = append(stack1, left.Left)
            stack2 = append(stack2, right.Right)
        }else if left.Left != nil || right.Right != nil{
            return false
        }
        
        if left.Right != nil && right.Left != nil{
            stack1 = append(stack1, left.Right)
            stack2 = append(stack2, right.Left)
        }else if left.Right != nil || right.Left != nil{
            return false
        }
    }
    if len(stack1)!=0 || len(stack2)!= 0{
        
        return false
    }
    return true
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：2.8 MB, 在所有 Go 提交中击败了39.95%的用户
</p>

