---
title: 剑指 Offer 27. 二叉树的镜像
excerpt: 所在模块：二叉树
tags: [golang,二叉树]
categories: 力扣刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>二叉树的镜像</font>

#### 题目描述：

请完成一个函数，输入一个二叉树，该函数输出它的镜像。

例如输入：

     4
   /   \
  2     7
 / \   / \
1   3 6   9
镜像输出：

     4
   /   \
  7     2
 / \   / \
9   6 3   1

 

示例 1：

输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]

#### 思路:

每次交互一个父节点的左右子结点

#### 代码:

```golang
func mirrorTree(root *TreeNode) *TreeNode {
    if root==nil{
        return  root
    }
    root.Left,root.Right = mirrorTree(root.Right),mirrorTree(root.Left)
    return root
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：2.1 MB, 在所有 Go 提交中击败了18.27%的用户
</p>

