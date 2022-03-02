---
title: 1151 LCA in a Binary Tree (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>LCA in a Binary Tree</font>

#### 题目描述：

The lowest common ancestor (LCA) of two nodes U and V in a tree is the deepest node that has both U and V as descendants.

Given any two nodes in a binary tree, you are supposed to find their LCA.

### Input Specification:

Each input file contains one test case. For each case, the first line gives two positive integers: M (≤ 1,000), the number of pairs of nodes to be tested; and N (≤ 10,000), the number of keys in the binary tree, respectively. In each of the following two lines, N distinct integers are given as the inorder and preorder traversal sequences of the binary tree, respectively. It is guaranteed that the binary tree can be uniquely determined by the input sequences. Then M lines follow, each contains a pair of integer keys U and V. All the keys are in the range of **int**.

### Output Specification:

For each given pair of U and V, print in a line `LCA of U and V is A.` if the LCA is found and `A` is the key. But if `A` is one of U and V, print `X is an ancestor of Y.` where `X` is `A` and `Y` is the other node. If U or V is not found in the binary tree, print in a line `ERROR: U is not found.` or `ERROR: V is not found.` or `ERROR: U and V are not found.`.

### Sample Input:

```in
6 8
7 2 3 4 6 5 1 8
5 3 7 2 6 4 8 1
2 6
8 1
7 9
12 -3
0 8
99 99
```

### Sample Output:

```out
LCA of 2 and 6 is 3.
8 is an ancestor of 1.
ERROR: 9 is not found.
ERROR: 12 and -3 are not found.
ERROR: 0 is not found.
ERROR: 99 and 99 are not found.
```

#### 思路:

根据前序和中序构建树，然后找两个人的公共祖先

#### 代码:

```go
#include<stdio.h>
#include<vector>
#include<string.h>
#include<map>
using namespace std;
typedef struct node{
	int data;
	node *lchild,*rchild;
}node;
map<int,node*>mp;
int in[1000007],pre[1000007];
node* build(int inl,int inr,int prl,int prr)
{
	if(inl>inr||prl>prr){
		return NULL;
	}
	node* root=new node;
	int temp,ky;
	root->data=in[inl];
	for(int i=prl;i<=prr;i++){
		if(pre[i]==in[inl]){
			temp=i;
			break;
		}
	}
    mp[in[inl]]=root;
	ky=temp-prl;
	root->lchild=build(inl+1,inl+ky,prl,temp-1);
	root->rchild=build(inl+1+ky,inr,temp+1,prr);
	return root;
}
node* lcd(node* root,node* a,node* b)
{
	if(root==NULL|root==a||root==b){
		return root;
	}
	node* l=lcd(root->lchild,a,b);
	node* r=lcd(root->rchild,a,b);
	if(l==NULL){	//如果在两边就会返回根节点，在一边的就会返回子节点。
		return r;
	}
	if(r==NULL){
		return l;
	}
	return root;
}
int main()
{
	int m,n,a,b;
	node *root;
	scanf("%d%d",&m,&n);
	for(int i=0;i<n;i++){
		scanf("%d",&pre[i]);
	}
	for(int i=0;i<n;i++){
		scanf("%d",&in[i]);
	}
	root=build(0,n-1,0,n-1);
	for(int i=0;i<m;i++){
		scanf("%d%d",&a,&b);
		if(!mp[a]&&!mp[b]){
			printf("ERROR: %d and %d are not found.\n",a,b);
		}else if(!mp[a]){
			printf("ERROR: %d is not found.\n",a);
		}else if(!mp[b]){
			printf("ERROR: %d is not found.\n",b);
		}else{
			node* p=lcd(root,mp[a],mp[b]);
			if(p->data==a){
				printf("%d is an ancestor of %d.\n",a,b);
			}else if(p->data==b){
				printf("%d is an ancestor of %d.\n",b,a);
			}else{
				printf("LCA of %d and %d is %d.\n",a,b,p->data);
			}
		}
	}
	return 0;
}

```

