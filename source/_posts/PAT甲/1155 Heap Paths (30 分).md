---
title: 1155 Heap Paths (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Heap Paths</font>

#### 题目描述：

In computer science, a **heap** is a specialized tree-based data structure that satisfies the heap property: if P is a parent node of C, then the key (the value) of P is either greater than or equal to (in a max heap) or less than or equal to (in a min heap) the key of C. A common implementation of a heap is the binary heap, in which the tree is a complete binary tree. (Quoted from Wikipedia at https://en.wikipedia.org/wiki/Heap_(data_structure))

One thing for sure is that all the keys along any path from the root to a leaf in a max/min heap must be in non-increasing/non-decreasing order.

Your job is to check every path in a given complete binary tree, in order to tell if it is a heap or not.

### Input Specification:

Each input file contains one test case. For each case, the first line gives a positive integer *N* (1<*N*≤1,000), the number of keys in the tree. Then the next line contains *N* distinct integer keys (all in the range of **int**), which gives the level order traversal sequence of a complete binary tree.

### Output Specification:

For each given tree, first print all the paths from the root to the leaves. Each path occupies a line, with all the numbers separated by a space, and no extra space at the beginning or the end of the line. The paths must be printed in the following order: for each node in the tree, all the paths in its right subtree must be printed before those in its left subtree.

Finally print in a line `Max Heap` if it is a max heap, or `Min Heap` for a min heap, or `Not Heap` if it is not a heap at all.

### Sample Input 1:

```in
8
98 72 86 60 65 12 23 50
```

### Sample Output 1:

```out
98 86 23
98 86 12
98 72 65
98 72 60 50
Max Heap
```

### Sample Input 2:

```in
8
8 38 25 58 52 82 70 60
```

### Sample Output 2:

```out
8 25 70
8 25 82
8 38 52
8 38 58 60
Min Heap
```

### Sample Input 3:

```in
8
10 28 15 12 34 9 8 56
```

### Sample Output 3:

```out
10 15 8
10 15 9
10 28 34
10 28 12 56
Not Heap
```

#### 思路:

先构建堆，再用dfs右边优先遍历，到子节点就输出，并且判断是升序还是降序还是错误序列。

#### 代码:

```go
#include "iostream"
#include "vector"
#include "algorithm"

using namespace std;

struct Node {
    int num;
    struct Node *left;
    struct Node *right;
};
int num[1002];
int n;
bool maxFlag = false, minFlag = false,validFlag= false;

Node *NewNode(int idx) {
    Node *node = (Node *) malloc(sizeof(Node));
    node->num = num[idx];
    node->left = nullptr;
    node->right = nullptr;
    return node;
}

bool check(vector<struct Node> res){
    if(minFlag){
        for(int i=1;i<res.size();i++){
            if(res[i].num < res[i-1].num){
                return false;
            }
        }
    }else if (maxFlag){
        for(int i=1;i<res.size();i++){
            if(res[i].num > res[i-1].num){
                return false;
            }
        }
    }
    return true;
}
Node *build(int idx) {
    if (idx >= n) {
        return nullptr;
    }
    Node *root = NewNode(idx);
    root->left = build(idx * 2 + 1);
    root->right = build((idx * 2 + 2));
    return root;
}


void dfs(Node *root, vector<struct Node> res) {
    if (root == nullptr) {
        return;
    }
    res.push_back(*root);
    dfs(root->right, res);
    dfs(root->left, res);
    if (root->left == nullptr && root->right == nullptr) {
        for (int i = 0; i < res.size(); i++) {
            if (i == 0) {
                cout << res[i].num;
            } else {
                cout << " " << res[i].num;
            }
        }
        cout << endl;
        if(!check(res)){
            validFlag = true;
        }
    }
}

int main() {
    cin >> n;
    for (int i = 0; i < n; i++) {
        cin >> num[i];
    }
    if (n == 1) {
        cout << num[0] << endl;
        cout << "Not Heap" << endl;
        return 0;
    }
    Node *root = build(0);
    if (num[0] > num[1]) {
        maxFlag = true;
    } else {
        minFlag = true;
    }
    vector<struct Node> res;
    dfs(root, res);
    if(validFlag){
        cout << "Not Heap" << endl;
    } else if (maxFlag){
        cout << "Max Heap" <<endl;
    }else if (minFlag){
        cout << "Min Heap" <<endl;
    }
}


```

