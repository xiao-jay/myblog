---
title: 剑指 Offer 26. 树的子结构
excerpt: 所在模块：二叉树
tags: [go,二叉树]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>树的子结构</font>

#### 题目描述：

输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)

B是A的子结构， 即 A中有出现和B相同的结构和节点值。

例如:
给定的树 A:

     3
    / \
   4   5
  / \
 1   2
给定的树 B：

   4 
  /
 1
返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。

示例 1：

输入：A = [1,2,3], B = [3,1]
输出：false
示例 2：

输入：A = [3,4,5,1,2], B = [4,1]
输出：true
限制：

0 <= 节点个数 <= 10000



#### 思路:

```
时间复杂度：O(n*m),空间复杂度O()
```

A一步步遍历看是否等于B

#### 代码:

```golang
func isSubStructure(A *TreeNode, B *TreeNode) bool {
    if A == nil{
        return false
    }
    return A != nil && B != nil &&(bianli(A,B) || isSubStructure(A.Left,B) || isSubStructure(A.Right,B))
}

func bianli(A *TreeNode,B *TreeNode)bool{
    if  B == nil{
        return true
    }else if A == nil || B == nil{
        return false
    }
    if A.Val == B.Val{
        return bianli(A.Left,B.Left) && bianli(A.Right,B.Right)
    }
    return false
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：24 ms, 在所有 Go 提交中击败了14.20%的用户<br>
   内存消耗：6.8 MB, 在所有 Go 提交中击败了83.03%
</p>





