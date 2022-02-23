---
title: 1159 Structure of a Binary Tree (30 分)
tags: [c]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Structure of a Binary Tree</font>

#### 题目描述：

Suppose that all the keys in a binary tree are distinct positive integers. Given the postorder and inorder traversal sequences, a binary tree can be uniquely determined.

Now given a sequence of statements about the structure of the resulting tree, you are supposed to tell if they are correct or not. A statment is one of the following:

- A is the root
- A and B are siblings
- A is the parent of B
- A is the left child of B
- A is the right child of B
- A and B are on the same level
- It is a full tree

Note:

- Two nodes are **on the same level**, means that they have the same depth.
- A **full binary tree** is a tree in which every node other than the leaves has two children.

### Input Specification:

Each input file contains one test case. For each case, the first line gives a positive integer *N* (≤30), the total number of nodes in the binary tree. The second line gives the postorder sequence and the third line gives the inorder sequence. All the numbers in a line are no more than 103 and are separated by a space.

Then another positive integer *M* (≤30) is given, followed by *M* lines of statements. It is guaranteed that both `A` and `B` in the statements are in the tree.

### Output Specification:

For each statement, print in a line `Yes` if it is correct, or `No` if not.

### Sample Input:

```in
9
16 7 11 32 28 2 23 8 15
16 23 7 32 11 2 28 15 8
7
15 is the root
8 and 2 are siblings
32 is the parent of 11
23 is the left child of 16
28 is the right child of 2
7 and 11 are on the same level
It is a full tree
```

### Sample Output:

```out
Yes
No
Yes
No
Yes
Yes
Yes
```

#### 思路:

先按照后序和中序遍历构造出二叉树，二叉树是特殊的，里面有指向父节点的和深度数据，string字符串中得出数字也有很巧妙的方式，看代码

#### 代码:

```go
#include "iostream"
#include "map"
using namespace  std;
typedef struct Node{
    int value;
    struct Node *left;
    struct Node *right;
    struct Node *father;
    int depth;
}Node;

map<int ,  Node *> mp ;
bool full = true;
int  Find(int array[], int size, int v){//找的始终是中序
    for (int i = 0; i < size; i++){
        if (array[i] == v){
            return i;
        }
    }
    return -1;
}
Node * BuildTree(int afterorder[], int inorder[], int size, int depth){
    if (size == 0){
        return  nullptr;
    }
    int rootValue = afterorder[size-1];
    int leftSize = Find(inorder, size, rootValue);//i的返回值就是左子树的个数
    //根
    Node *root = (Node *)malloc(sizeof(Node));
    root->value = rootValue;

    //左子树
    root->left = BuildTree(afterorder , inorder, leftSize, depth+1);
    if(root->left != nullptr){
        root->left->father = root;
    }
    //右子树
    root->right = BuildTree(afterorder + leftSize, inorder + leftSize + 1, size - 1 - leftSize, depth+1);
    if (root->right != nullptr){
        root->right ->father = root;
    }

    mp[rootValue] = root;
    if ((root->left && !root->right) || !root->left && root->right) full = false;
    root->depth = depth;
    return root;
}

int main(){
    int n;
    cin >>n;

    int postorder[n],inorder[n];
    for (int i=0;i<n;i++){
        cin >>postorder[i];
    }
    for (int i=0;i<n;i++){
        cin >>inorder[i];
    }
    Node * root = BuildTree(postorder,inorder, n, 0);

    int n2;
    cin >> n2;
    getchar();
    while (n2--){
        string s;
        int a,b ;
        getline(cin, s);
        if (s.find("root") != string::npos){
            sscanf(s.c_str(), "%d is the root", &a);
            if (root->value == a){
                cout << "Yes" << endl;
            }else{
                cout << "No" << endl;
            }
        }else if(s.find("siblings") != string::npos){
            sscanf(s.c_str(), "%d and %d are siblings", &a, &b);
            if (mp[a]->father == mp[b]->father){
                cout << "Yes" << endl;
            }else{
                cout << "No" << endl;
            }
        }else if(s.find("parent") != string::npos){
            sscanf(s.c_str(), "%d is the parent of %d", &a, &b);
            if(mp[b]->father == mp[a]){
                cout << "Yes" << endl;
            }else{
                cout << "No" << endl;
            }
        }else if(s.find("left") != string::npos){
            sscanf(s.c_str(), "%d is the left child of %d", &a, &b);
            if (mp[b]->left == mp[a]) cout << "Yes" << endl;
            else cout << "No" << endl;
        }else if(s.find("right") != string::npos){
            sscanf(s.c_str(), "%d is the right child of %d", &a, &b);
            if (mp[b]->right == mp[a]) cout << "Yes" << endl;
            else cout << "No" << endl;
        }else if(s.find("level") != string::npos){
            sscanf(s.c_str(), "%d and %d are on the same level", &a, &b);
            if (mp[b]->depth == mp[a]->depth) cout << "Yes" << endl;
            else cout << "No" << endl;

        }else if(s.find("full") != string::npos){
            if (full) cout << "Yes" << endl;
            else cout << "No" << endl;
        }
    }
    return 0;
}
```

