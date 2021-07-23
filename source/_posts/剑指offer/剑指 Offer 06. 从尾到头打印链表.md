---
title: 剑指 Offer 06. 从尾到头打印链表
excerpt: 所在模块：链表
tags: [c,链表]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>06. 从尾到头打印链表</font>

#### 题目描述：

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

 

**示例 1：**

```
输入：head = [1,3,2]
输出：[2,3,1]
```

#### 思路:

先将链表逆置，在逆置过程中算出长度，最后赋值给数组中

#### 代码:

```golang
struct ListNode* reverse(struct ListNode *head,int *len){
     struct ListNode *head1=NULL;
     struct ListNode *tmp =NULL;
     while(head!=NULL){
         tmp = head->next;
         head->next = head1;
         head1 = head;
         head = tmp;
         *len+=1;
     }
     return head1;
 }
int* reversePrint(struct ListNode* head, int* returnSize){
    int *num;
    int len = 0;
    head = reverse(head, &len);
    num = (int *)malloc(sizeof(int)*10000);
    int i=0;
    while(head!=NULL){
        
        num[i++] = head->val;
        head = head->next;
    }
    
    *returnSize = len;
    return num;
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：12 ms, 在所有 C 提交中击败了57.35% 的用户<br>
   内存消耗：7.4 MB, 在所有 C 提交中击败了20.63% 的用户
</p>