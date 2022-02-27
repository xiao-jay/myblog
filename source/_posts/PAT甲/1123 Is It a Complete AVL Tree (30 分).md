---
title: 1123 Is It a Complete AVL Tree (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Is It a Complete AVL Tre</font>

#### 题目描述：

An AVL tree is a self-balancing binary search tree. In an AVL tree, the heights of the two child subtrees of any node differ by at most one; if at any time they differ by more than one, rebalancing is done to restore this property. Figures 1-4 illustrate the rotation rules.

| ![F1.jpg](https://images.ptausercontent.com/fb337acb-93b0-4af2-9838-deff5ce98058.jpg) | ![F2.jpg](https://images.ptausercontent.com/d1635de7-3e3f-4aaa-889b-ba29f35890db.jpg) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![F3.jpg](https://images.ptausercontent.com/e868e4b9-9fea-4f70-b7a7-1f5d8a3be4ef.jpg) | ![F4.jpg](https://images.ptausercontent.com/98aa1782-cea5-4792-8736-999436cf43a9.jpg) |

Now given a sequence of insertions, you are supposed to output the level-order traversal sequence of the resulting AVL tree, and to tell if it is a complete binary tree.

### Input Specification:

Each input file contains one test case. For each case, the first line contains a positive integer N (≤ 20). Then N distinct integer keys are given in the next line. All the numbers in a line are separated by a space.

### Output Specification:

For each test case, insert the keys one by one into an initially empty AVL tree. Then first print in a line the level-order traversal sequence of the resulting AVL tree. All the numbers in a line must be separated by a space, and there must be no extra space at the end of the line. Then in the next line, print `YES` if the tree is complete, or `NO` if not.

### Sample Input 1:

```in
5
88 70 61 63 65
```

### Sample Output 1:

```out
70 63 88 61 65
YES
```

### Sample Input 2:

```in
8
88 70 61 96 120 90 65 68
```

### Sample Output 2:

```out
88 65 96 61 70 90 120 68
NO
```

#### 思路:

构建avl树，测试点2，3是来判断是否是完全二叉树的情况的

#### 代码:

```go
#include "iostream"
#include "vector"
#include "queue"
using namespace std;
int n;

struct Node {
    Node *left, *right;
    int num;
};

int getHeight(Node *tree) {
    if (tree == nullptr) return 0;
    int l = getHeight(tree->left);
    int r = getHeight(tree->right);
    return max(l, r) + 1;
}

bool isBalanced(Node *left, Node *right) {
    return abs(getHeight(left) - getHeight(right)) < 2;

}

Node * LeftLeft(Node* t){
    Node * x = t->right;
    Node * y = x->left;
    x->left = t;
    t->right = y;
    return x;
}

Node * RightRight(Node* t){
    Node * x = t->left;
    Node * y = x->right;
    x->right = t;
    t->left = y;
    return x;
}


Node * LeftRight(Node *t){
    t->left = LeftLeft(t->left);
    return RightRight(t);
}

Node * RightLeft(Node *t){
    t->right = RightRight(t->right);
    return LeftLeft(t);
}
Node *buildNode(int num) {
    Node *node = (Node *) malloc(sizeof(Node));
    node->left = node->right = nullptr;
    node->num = num;
    return node;
}

Node *buildAvl(Node *root, int num) {
    if (root == nullptr) {
        return buildNode(num);
    }
    if (num > root->num) {
        root->right = buildAvl(root->right, num);
        if(!isBalanced(root->left, root->right)){
            if(num > root->right->num){
                root = LeftLeft(root);
            }else{
                root = RightLeft(root);
            }
        }
    } else {
        root->left = buildAvl(root->left, num);
        if(!isBalanced(root->left, root->right)){
            if (num < root->left->num){
                root = RightRight(root);
            }else{
                root = LeftRight(root);
            }
        }
    }
    return root;
}

int main() {
    cin >> n;
    Node *root = nullptr;
    while (n--) {
        int a;
        cin >> a;
        root = buildAvl(root, a);
    }
    queue<Node *> queue;
    queue.push(root);
    int flag = 0;
    int treeComplete = 1;
    int after = 0;
    while(!queue.empty()){
        Node * node = queue.front();
        queue.pop();
        if(!flag){
            cout << node->num;
            flag = 1;
        } else{
            cout <<" "<< node->num;
        }
        //if((node->right != nullptr && node->left == nullptr) || (node->right == nullptr && node->left != nullptr)) treeComplete = 0;
        if(node->left != nullptr){
            queue.push(node->left);
            if(after) treeComplete = 0;
        }else{
            after = 1;
        }
        if(node->right != nullptr){
            queue.push(node->right);
            if(after) treeComplete = 0;
        }else{
            after = 1;
        }
    }
    cout <<endl;
    if(treeComplete) cout << "YES"<<endl;
    else cout <<"NO"<<endl;
    return 0;
}
```

