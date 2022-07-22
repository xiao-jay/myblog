---
title: 剑指 Offer 18. 删除链表的节点
excerpt: 所在模块：链表
tags: [go,链表]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>删除链表的节点</font>

#### 题目描述：

给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。

返回删除后的链表的头节点。

注意：此题对比原题有改动

示例 1:

输入: head = [4,5,1,9], val = 5
输出: [4,1,9]
解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.
示例 2:

输入: head = [4,5,1,9], val = 1
输出: [4,5,9]
解释: 给定你链表中值为 1 的第三个节点，那么在调用了你的函数之后，该链表应变为 4 -> 5 -> 9.


说明：

题目保证链表中节点的值互不相同
若使用 C 或 C++ 语言，你不需要 free 或 delete 被删除的节点

#### 思路:

```
时间复杂度：O(n),空间复杂度O(1)
```



#### 代码:

```golang
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func deleteNode(head *ListNode, val int) *ListNode {
    if head.Val == val{
        return head.Next
    }
    head2 := new(ListNode)
    head2.Next = head
    for head2.Next != nil{
        if head2.Next.Val == val{
            head2.Next = head2.Next.Next
            break
        }
        head2 = head2.Next
    } 
    
    return head
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：2.7 MB, 在所有 Go 提交中击败了61.43%的用户
</p>

