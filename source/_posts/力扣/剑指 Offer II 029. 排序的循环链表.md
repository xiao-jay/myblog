---
title: 剑指 Offer II 029. 排序的循环链表
excerpt: 所在模块：链表
tags: [golang,链表]
categories: 力扣刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>排序的循环链表</font>

#### 题目描述：

给定循环单调非递减列表中的一个点，写一个函数向这个列表中插入一个新元素 insertVal ，使这个列表仍然是循环升序的。

给定的可以是这个列表中任意一个顶点的指针，并不一定是这个列表中最小元素的指针。

如果有多个满足条件的插入位置，可以选择任意一个位置插入新的值，插入后整个列表仍然保持有序。

如果列表为空（给定的节点是 null），需要创建一个循环有序列表并返回这个节点。否则。请返回原先给定的节点。

 

示例 1：




输入：head = [3,4,1], insertVal = 2
输出：[3,4,1,2]
解释：在上图中，有一个包含三个元素的循环有序列表，你获得值为 3 的节点的指针，我们需要向表中插入元素 2 。新插入的节点应该在 1 和 3 之间，插入之后，整个列表如上图所示，最后返回节点 3 。


示例 2：

输入：head = [], insertVal = 1
输出：[1]
解释：列表为空（给定的节点是 null），创建一个循环有序列表并返回这个节点。
示例 3：

输入：head = [1], insertVal = 0
输出：[1,0]


提示：

0 <= Number of Nodes <= 5 * 10^4
-10^6 <= Node.val <= 10^6
-10^6 <= insertVal <= 10^6



#### 思路:

```
时间复杂度：O(n),空间复杂度O(1)
```

先找到有序的头部，比如3 1 2，先找到有序的开头1，然后把这个作为头部，开始遍历找到插入点

#### 代码:

```golang
/**
 * Definition for a Node.
 * type Node struct {
 *     Val int
 *     Next *Node
 * }
 */

func news(x int)*Node{
    node := Node{}
    node.Val = x
    node.Next = nil
    return &node
}

func insert(aNode *Node, x int) *Node {
    node := news(x)
    if aNode == nil{
        node.Next = node
        return node
    }
    if aNode.Next == aNode{
        aNode.Next = node
        node.Next = aNode
        return aNode
    }
    head := aNode
    tmp := aNode
    for aNode.Next != head{
        next := aNode.Next
        if next.Val < aNode.Val{
            head = next
            break
        }
        aNode = next
    }
    bNode := head
    flag := false
    for bNode.Next != head{
        next := bNode.Next
        if next.Val >=x && bNode.Val <=x{
            bNode.Next = node
            node.Next = next
            flag = true
            break
        }
        bNode = next
    }
    if !flag{
        bNode.Next = node
        node.Next = head
    }
    return tmp

}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：3.5 MB, 在所有 Go 提交中击败了98.29%的用户
</p>

