---
title: 1099 Build A Binary Search Tree (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Build A Binary Search Tree </font>

#### 题目描述：

A Binary Search Tree (BST) is recursively defined as a binary tree which has the following properties:

- The left subtree of a node contains only nodes with keys less than the node's key.
- The right subtree of a node contains only nodes with keys greater than or equal to the node's key.
- Both the left and right subtrees must also be binary search trees.

Given the structure of a binary tree and a sequence of distinct integer keys, there is only one way to fill these keys into the tree so that the resulting tree satisfies the definition of a BST. You are supposed to output the level order traversal sequence of that tree. The sample is illustrated by Figure 1 and 2.

![figBST.jpg](https://images.ptausercontent.com/24c2521f-aaed-4ef4-bac8-3ff562d80a1b.jpg)

### Input Specification:

Each input file contains one test case. For each case, the first line gives a positive integer *N* (≤100) which is the total number of nodes in the tree. The next *N* lines each contains the left and the right children of a node in the format `left_index right_index`, provided that the nodes are numbered from 0 to *N*−1, and 0 is always the root. If one child is missing, then −1 will represent the NULL child pointer. Finally *N* distinct integer keys are given in the last line.

### Output Specification:

For each test case, print in one line the level order traversal sequence of that tree. All the numbers must be separated by a space, with no extra space at the end of the line.

### Sample Input:

```in
9
1 6
2 3
-1 -1
-1 4
5 -1
-1 -1
7 -1
-1 8
-1 -1
73 45 11 58 82 25 67 38 42
```

### Sample Output:

```out
58 25 82 11 38 67 45 73 42
```

#### 思路:

先按照序列构造二叉树，再讲数组排序用中序插入数字

#### 代码:

```go
#include <iostream>
#include <algorithm>
#include <queue>
using namespace std;
struct node {
    int data, left, right;
}tree[99999];
int ele[99999], N, l, r, ele_i = 0, start = true;
void inorder(int root) {
    if(tree[root].left !=  -1) inorder(tree[root].left);
    tree[root].data = ele[ele_i++];
    if(tree[root].right !=  -1) inorder(tree[root].right);
}
void levelorder(int root) {
    queue<int> que;
    que.push(root);
    while(!que.empty()) {
        node n = tree[que.front()];
        if(start) printf("%d", n.data);
        else printf(" %d", n.data);
        start = false;
        que.pop();
        if(n.left != -1) que.push(n.left);
        if(n.right != -1) que.push(n.right);
    }
}
int main() {
    scanf("%d", &N);
    for(int i = 0; i < N; i++) scanf("%d%d", &tree[i].left, &tree[i].right);
    for(int i = 0; i < N; i++) scanf("%d", &ele[i]);
    sort(ele, ele + N);
    inorder(0);
    levelorder(0);
    return 0;
}

```

