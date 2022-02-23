---
title: 1127 ZigZagging on a Tree (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>ZigZagging on a Tree</font>

#### 题目描述：

Suppose that all the keys in a binary tree are distinct positive integers. A unique binary tree can be determined by a given pair of postorder and inorder traversal sequences. And it is a simple standard routine to print the numbers in level-order. However, if you think the problem is too simple, then you are too naive. This time you are supposed to print the numbers in "zigzagging order" -- that is, starting from the root, print the numbers level-by-level, alternating between left to right and right to left. For example, for the following tree you must output: 1 11 5 8 17 12 20 15.

![zigzag.jpg](https://images.ptausercontent.com/337cbfb0-a7b2-4500-9664-318e9ffc870e.jpg)

### Input Specification:

Each input file contains one test case. For each case, the first line gives a positive integer N (≤30), the total number of nodes in the binary tree. The second line gives the inorder sequence and the third line gives the postorder sequence. All the numbers in a line are separated by a space.

### Output Specification:

For each test case, print the zigzagging sequence of the tree in a line. All the numbers in a line must be separated by exactly one space, and there must be no extra space at the end of the line.

### Sample Input:

```in
8
12 11 20 17 1 15 8 5
12 20 17 11 15 8 5 1
```

### Sample Output:

```out
1 11 5 8 17 12 20 15
```

#### 思路:

先根据后序和中序构建二叉树，再层次遍历。

#### 代码:

```go
#include <bits/stdc++.h>
using namespace std;

int inorder[31], postorder[31];
int n ;

struct Node{
    int num;
    struct Node *left,*right;
};
int find(const int nums[], int size, int val){
    for (int i=0;i<size; i++){
        if (val == nums[i]){
            return i;
        }
    }
}

Node * buildTree(int afterOrder[],int inOrder[], int size){
    if(size <= 0){
        return nullptr;
    }
    Node *root = (Node *)malloc(sizeof(Node));
    int postIndex = find(inOrder,size,afterOrder[size-1]);
    root->num = afterOrder[size-1];
    root->left = buildTree(afterOrder,inOrder, postIndex);
    root->right = buildTree(afterOrder+postIndex,inOrder+ postIndex + 1, size - postIndex - 1);
    return root;
}

int main(){
    cin >>n;
    vector<Node *> vec1,vec2;
    vector<int> temp;
    for(int i=0;i<n;i++){
        cin >> inorder[i];
    }
    for(int i=0;i<n;i++){
        cin >> postorder[i];
    }
    Node* root  = buildTree(postorder,inorder,n);
    bool flag = true;
    bool first = true;
    vec1.insert(vec1.begin(),root);
    while(!vec1.empty()  || !vec2.empty()){
        temp.clear();
        while(!vec1.empty()){
            Node * node = vec1[vec1.size()-1];
            temp.push_back(node->num);
            vec1.pop_back();
            if(node->left != nullptr){
                vec2.insert(vec2.begin(), node->left);
            }
            if(node->right != nullptr){
                vec2.insert(vec2.begin(), node->right);
            }
        }
        if(flag){
            std::reverse(temp.begin(), temp.end());
        }
        flag = !flag;
        vec1 = vec2;
        vec2.clear();
        if(first){
            cout << temp[0];
            first = false;
        }else{
            for(int j=0;j< temp.size(); j++){
                cout<<" "<<temp[j];
            }
        }
    }
    return 0;
}
```

