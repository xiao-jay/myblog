---
title: 剑指 Offer 22. 链表中倒数第k个节点
excerpt: 所在模块：链表
tags: [go,链表]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>链表中倒数第k个节点</font>

#### 题目描述：

输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。

例如，一个链表有 6 个节点，从头节点开始，它们的值依次是 1、2、3、4、5、6。这个链表的倒数第 3 个节点是值为 4 的节点。

 

示例：

给定一个链表: 1->2->3->4->5, 和 k = 2.

返回链表 4->5.

#### 思路:

```
时间复杂度：O(n),空间复杂度O()
```

双指针法

#### 代码:

```golang
func getKthFromEnd(head *ListNode, k int) *ListNode {
    fast, slow := head, head
    for fast != nil && k > 0 {
        fast = fast.Next
        k--
    }
    for fast != nil {
        fast = fast.Next
        slow = slow.Next
    }
    return slow
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：2.1 MB, 在所有 Go 提交中击败了12.35%的用户
</p>
