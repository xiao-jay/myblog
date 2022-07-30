---
title: 剑指 Offer 07. 重建二叉树
excerpt: 所在模块：二叉树
tags: [go,二叉树]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>重建二叉树</font>

#### 题目描述：

输入某二叉树的前序遍历和中序遍历的结果，请构建该二叉树并返回其根节点。

假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

 

示例 1:


Input: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
Output: [3,9,20,null,null,15,7]
示例 2:

Input: preorder = [-1], inorder = [-1]
Output: [-1]


限制：

0 <= 节点个数 <= 5000

#### 思路:

```
时间复杂度：O(n),空间复杂度O()
```

递归

#### 代码:

```golang
func buildTree(preorder []int, inorder []int) *TreeNode {
    if len(preorder) == 0 {
        return nil
    }
    root := &TreeNode{preorder[0], nil, nil}
    i := 0
    for ; i < len(inorder); i++ {
        if inorder[i] == preorder[0] {
            break
        }
    }
    root.Left = buildTree(preorder[1:len(inorder[:i])+1], inorder[:i])
    root.Right = buildTree(preorder[len(inorder[:i])+1:], inorder[i+1:])
    return root
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：4 ms, 在所有 Go 提交中击败了93.47%的用户<br>
   内存消耗：4 MB, 在所有 Go 提交中击败了67.24%的用户
</p>



