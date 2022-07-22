---
title: 剑指 Offer II 024. 反转链表
excerpt: 所在模块：链表
tags: [go,链表]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>反转链表</font>

#### 题目描述：

给定单链表的头节点 head ，请反转链表，并返回反转后的链表的头节点。

 

示例 1：


输入：head = [1,2,3,4,5]
输出：[5,4,3,2,1]
示例 2：


输入：head = [1,2]
输出：[2,1]
示例 3：

输入：head = []
输出：[]


提示：

链表中节点的数目范围是 [0, 5000]
-5000 <= Node.val <= 5000


进阶：链表可以选用迭代或递归方式完成反转。你能否用两种方法解决这道题？

#### 思路:

```
时间复杂度：O(n),空间复杂度O(1)
```

用头插法

#### 代码:

```golang
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func reverseList(head *ListNode) *ListNode {
    if head == nil{
        return nil
    }
    var head2 *ListNode
    for head != nil{
        next := head.Next
        head.Next = head2
        head2 = head
        head = next
    }
    return head2
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：2.4 MB, 在所有 Go 提交中击败了65.36%的用户
</p>

