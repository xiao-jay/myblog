---
title: 1167 Cartesian Tree (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Cartesian Tree</font>

#### 题目描述：

A **Cartesian tree** is a binary tree constructed from a sequence of distinct numbers. The tree is heap-ordered, and an inorder traversal returns the original sequence. For example, given the sequence { 8, 15, 3, 4, 1, 5, 12, 10, 18, 6 }, the min-heap Cartesian tree is shown by the figure.

![CTree.jpg](https://images.ptausercontent.com/6a99f68a-6578-46e0-9232-fbf0adf3691f.jpg)

Your job is to output the level-order traversal sequence of the min-heap Cartesian tree.

### Input Specification:

Each input file contains one test case. Each case starts from giving a positive integer *N* (≤30), and then *N* distinct numbers in the next line, separated by a space. All the numbers are in the range of **int**.

### Output Specification:

For each test case, print in a line the level-order traversal sequence of the min-heap Cartesian tree. All the numbers in a line must be separated by exactly one space, and there must be no extra space at the beginning or the end of the line.

### Sample Input:

```in
10
8 15 3 4 1 5 12 10 18 6
```

### Sample Output:

```out
1 3 5 8 4 6 15 10 12 18
```

#### 思路:

别管他什么inorder，先找到序列的最小值，左边是它的左子树，右边是它的右子树，然后构造好树然后层次遍历。

#### 代码:

```go
#include "iostream"
#include "math.h"
#include "vector"
using namespace  std;
struct Node{
    int val;
    struct Node * left;
    struct Node * right;
};
int findMin(int nums[], int size){
    int mini = 0 ;
    for (int i=1;i<size; i++){
        if (nums[mini] > nums[i]){
            mini = i;
        }
    }
    return mini;
}

Node * build(int nums [],int size){
    if(size <= 0){
        return nullptr;
    }
    int mini = findMin(nums, size);
    Node *root = (Node *)malloc(sizeof(Node));
    root->val = nums[mini];
    root->left = build(nums, mini);
    root->right = build(nums+mini+1, size-mini-1);
    return root;
}


int main(){
    int n;
    cin >> n;
    int inorder[n];
    for(int i=0; i<n; i++){
        cin >> inorder[i];
    }
    Node * root = build(inorder, n);
    vector<Node *> vec;
    vec.push_back(root);
    cout << root->val;
    while (!vec.empty()){
        Node * node = vec.back();
        vec.pop_back();
        if(node->left != nullptr){

            vec.insert(vec.begin(),node->left );
            cout <<" "<<node->left->val;
        }
        if(node->right != nullptr){
            vec.insert(vec.begin(),node->right );
            cout <<" "<<node->right->val;
        }
    }

}
```

