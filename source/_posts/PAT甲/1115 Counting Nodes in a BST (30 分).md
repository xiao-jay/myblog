---
title: 1115 Counting Nodes in a BST (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Counting Nodes in a BST</font>

#### 题目描述：

A Binary Search Tree (BST) is recursively defined as a binary tree which has the following properties:

- The left subtree of a node contains only nodes with keys less than or equal to the node's key.
- The right subtree of a node contains only nodes with keys greater than the node's key.
- Both the left and right subtrees must also be binary search trees.

Insert a sequence of numbers into an initially empty binary search tree. Then you are supposed to count the total number of nodes in the lowest 2 levels of the resulting tree.

### Input Specification:

Each input file contains one test case. For each case, the first line gives a positive integer *N* (≤1000) which is the size of the input sequence. Then given in the next line are the *N* integers in [−1000,1000] which are supposed to be inserted into an initially empty binary search tree.

### Output Specification:

For each case, print in one line the numbers of nodes in the lowest 2 levels of the resulting tree in the format:

```
n1 + n2 = n
```

where `n1` is the number of nodes in the lowest level, `n2` is that of the level above, and `n` is the sum.

### Sample Input:

```in
9
25 30 42 16 20 20 35 -5 28
```

### Sample Output:

```out
2 + 4 = 6
```

#### 思路:

递归构建二叉树之后，层次遍历各个层的数量，最后输出最后两层。

#### 代码:

```go
#include "iostream"
#include "vector"
#include "queue"
using namespace std;

struct Node {
    Node * left,*right;
    int num;
};


Node *buildNode(int a){
    Node* node = (Node *)malloc(sizeof(Node));
    node->num = a;
    node->right = nullptr;
    node->left = nullptr;
    return node;
};
Node *build(Node * root, int a){
    if(root == nullptr){
        return buildNode(a);
    }
    if(a > root->num) root->right = build(root->right, a);
    else  root->left = build(root->left, a);
    return root;
}
int main(){
    int n = 0;
    cin >> n ;
    Node * root = nullptr;
    while(n--){
        int a;
        cin >>a;
        root = build(root, a);
    }
    vector<int> level;
    queue<Node *> que1,que2;
    que1.push(root);
    while(!que1.empty()  ){
        int levels = 0;
        while(!que1.empty()){
            Node* node = que1.front();
            levels++;
            que1.pop();
            if(node->left != nullptr){
                que2.push(node->left);
            }
            if(node->right != nullptr){
                que2.push(node->right);
            }
        }
        level.push_back(levels);
        que1 = que2;
        while(!que2.empty()) que2.pop();
    }
    cout<< level[level.size()-1]<<" + "<<level[level.size()-2]<<" = "<<level[level.size()-1]+level[level.size()-2]<<endl;
}
```

