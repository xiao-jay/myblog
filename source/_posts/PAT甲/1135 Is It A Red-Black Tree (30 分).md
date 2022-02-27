---
title: 1135 Is It A Red-Black Tree (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Is It A Red-Black Tree</font>

#### 题目描述：

There is a kind of balanced binary search tree named **red-black tree** in the data structure. It has the following 5 properties:

- (1) Every node is either red or black.
- (2) The root is black.
- (3) Every leaf (NULL) is black.
- (4) If a node is red, then both its children are black.
- (5) For each node, all simple paths from the node to descendant leaves contain the same number of black nodes.

For example, the tree in Figure 1 is a red-black tree, while the ones in Figure 2 and 3 are not.

| ![rbf1.jpg](https://images.ptausercontent.com/eff80bd4-c833-4818-9786-81680d1b304a.jpg) | ![rbf2.jpg](https://images.ptausercontent.com/b11184df-eaab-451c-b7d4-7fc1dc82b028.jpg) | ![rbf3.jpg](https://images.ptausercontent.com/625c532b-22fc-47b9-80ea-0537cf00d922.jpg) |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Figure 1                                                     | Figure 2                                                     | Figure 3                                                     |

For each given binary search tree, you are supposed to tell if it is a legal red-black tree.

### Input Specification:

Each input file contains several test cases. The first line gives a positive integer K (≤30) which is the total number of cases. For each case, the first line gives a positive integer N (≤30), the total number of nodes in the binary tree. The second line gives the preorder traversal sequence of the tree. While all the keys in a tree are positive integers, we use negative signs to represent red nodes. All the numbers in a line are separated by a space. The sample input cases correspond to the trees shown in Figure 1, 2 and 3.

### Output Specification:

For each test case, print in a line "Yes" if the given tree is a red-black tree, or "No" if not.

### Sample Input:

```in
3
9
7 -2 1 5 -4 -11 8 14 -15
9
11 -2 1 -7 5 -4 8 14 -15
8
10 -7 5 -6 8 15 -11 17
```

### Sample Output:

```out
Yes
No
No
```

#### 思路:

只有三种情况不符合，根结点是负值，连续两个红色，每条path的黑色节点数量不同，其他都是yes

#### 代码:

```go
#include "iostream"

using namespace std;

int m;
int num[31];
bool is_valid;
struct Node {
    Node *left, *right;
    int num;
    bool isRed;
};

Node *buildNode(int a, bool is_red) {
    Node *node = (Node *) malloc(sizeof(Node));
    node->num = a;
    node->isRed = is_red;
    node->right = nullptr;
    node->left = nullptr;
    return node;
};

Node *build(Node *root, int a, bool is_red) {
    if (root == nullptr) {
        return buildNode(a, is_red);
    }
    if (a > root->num) root->right = build(root->right, a, is_red);
    else root->left = build(root->left, a, is_red);
    return root;

}

int blackNodeNums = 0;

void dfs(Node *root, int blackNodeNum) {
    //如果知道错的就直接return
    if (is_valid) return;

    if (root == nullptr) {
        if (blackNodeNums == 0) blackNodeNums = blackNodeNum;
        else if (blackNodeNums != blackNodeNum) is_valid = true;
        return;
    }
    if (!root->isRed) {
        dfs(root->left, blackNodeNum + 1);
        dfs(root->right, blackNodeNum + 1);
    } else {
        dfs(root->left, blackNodeNum);
        dfs(root->right, blackNodeNum);
    }
}

void dfsRed(Node *root) {
    if (root->left != nullptr) {
        if (root->isRed && root->left->isRed) is_valid = true;
        else dfsRed(root->left);
    }
    if (root->right != nullptr) {
        if (root->isRed && root->right->isRed) is_valid = true;
        else dfsRed(root->left);
    }
}

int main() {
    int n;
    cin >> n;
    while (n--) {
        cin >> m;
        for (int i = 0; i < m; i++) {
            cin >> num[i];
        }
        is_valid = false;
        blackNodeNums = 0;
        if (num[0] < 0) {
            cout << "No" << endl;
            continue;
        }
        Node *root = nullptr;
        for (int i = 0; i < m; i++) {
            if (num[i] < 0) root = build(root, -num[i], true);
            else root = build(root, num[i], false);
        }
        dfs(root, 0);
        if (is_valid) {
            cout << "No" << endl;
            continue;
        }
        dfsRed(root);
        if (is_valid) {
            cout << "No" << endl;
            continue;
        }
        cout << "Yes" << endl;
    }
}
```

