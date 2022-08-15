---
title: 剑指 Offer 35. 复杂链表的复制
excerpt: 所在模块：链表
tags: [go,链表]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>复杂链表的复制</font>

#### 题目描述：

请实现 copyRandomList 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。

 

示例 1：



输入：head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
输出：[[7,null],[13,0],[11,4],[10,2],[1,0]]
示例 2：



输入：head = [[1,1],[2,1]]
输出：[[1,1],[2,1]]
示例 3：



输入：head = [[3,null],[3,0],[3,null]]
输出：[[3,null],[3,0],[3,null]]
示例 4：

输入：head = []
输出：[]
解释：给定的链表为空（空指针），因此返回 null。


提示：

-10000 <= Node.val <= 10000
Node.random 为空（null）或指向链表中的节点。
节点数目不超过 1000 。

#### 思路:

```
时间复杂度：O(n),空间复杂度O(n)
```

使用map做最容易想到，用两个map，一个放node到位置，一个放位置到node，遍历两次

#### 代码:

```golang
/**
 * Definition for a Node.
 * type Node struct {
 *     Val int
 *     Next *Node
 *     Random *Node
 * }
 */

func copyRandomList(head *Node) *Node {
    if head == nil{
        return nil
    }
    mp := make(map[int]*Node, 0)
    mp_int := make(map[*Node]int ,0)
    tmp := head
    for k:=0; tmp != nil; k++{
        node := new(Node)
        node.Val = tmp.Val
        mp[k] = node
        mp_int[tmp] = k
        tmp = tmp.Next
    }
    tmp = head
    for k:=0; tmp != nil; k++{
        mp[k].Next = mp[k+1]
        
        if tmp.Random != nil{
            mp[k].Random = mp[mp_int[tmp.Random]]
        }else{
            mp[k].Random = nil
        }
        tmp = tmp.Next
    }
    return mp[0]
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：4 ms, 在所有 Go 提交中击败了100.00%的用户<br>
   内存消耗：3.5 MB, 在所有 Go 提交中击败了38.17%的用户
</p>


