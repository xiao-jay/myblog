---
title: 1161 Merging Linked Lists (25 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Merging Linked Lists</font>

#### 题目描述：

Given two singly linked lists *L*1=*a*1→*a*2→⋯→*a**n*−1→*a**n* and *L*2=*b*1→*b*2→⋯→*b**m*−1→*b**m*. If *n*≥2*m*, you are supposed to reverse and merge the shorter one into the longer one to obtain a list like *a*1→*a*2→*b**m*→*a*3→*a*4→*b**m*−1⋯. For example, given one list being 6→7 and the other one 1→2→3→4→5, you must output 1→2→7→3→4→6→5.

### Input Specification:

Each input file contains one test case. For each case, the first line contains the two addresses of the first nodes of *L*1 and *L*2, plus a positive *N* (≤105) which is the total number of nodes given. The address of a node is a 5-digit nonnegative integer, and NULL is represented by `-1`.

Then *N* lines follow, each describes a node in the format:

```
Address Data Next
```

where `Address` is the position of the node, `Data` is a positive integer no more than 105, and `Next` is the position of the next node. It is guaranteed that no list is empty, and the longer list is at least twice as long as the shorter one.

### Output Specification:

For each case, output in order the resulting linked list. Each node occupies a line, and is printed in the same format as in the input.

### Sample Input:

```in
00100 01000 7
02233 2 34891
00100 6 00001
34891 3 10086
01000 1 02233
00033 5 -1
10086 4 00033
00001 7 -1
```

### Sample Output:

```out
01000 1 02233
02233 2 00001
00001 7 34891
34891 3 10086
10086 4 00100
00100 6 00033
00033 5 -1
```

#### 思路:

模拟法做，构建两个链表，长的链表一次输出两个短的一次输出一个

#### 代码:

```go
//pat 1161
#include <bits/stdc++.h>
using namespace std;

const int maxn = 1e5+10;
int begin1, begin2, n;

struct node{
	int add, data, next;
}a[maxn];
vector<node> L1, L2, res; 

int main(){
	//freopen("in.txt", "r", stdin);
	//freopen("out.txt", "w", stdout);
	
	cin >> begin1 >> begin2 >> n;
	for(int i = 1; i <= n; i ++ ){
		int address, data, next;
		cin >> address >> data >> next;
		a[address].add = address;
		a[address].data = data;
		a[address].next = next;
	}
	
	for(int p = begin1; p != -1; p = a[p].next){
		L1.push_back(a[p]); 
	}
	for(int p = begin2; p != -1; p = a[p].next){
		L2.push_back(a[p]); 
	}
	
	int head;	//res的头 
	if(L1.size() > L2.size()) head = begin1;
	else{
		head = begin2;
		L2 = L1;	//L2作为短链表 长链表L1根据head在a[]中得到 
	}
	
	int k = 0;	//控制每2个插入一个 
    while (head != -1){
        res.push_back(a[head]);
        ++k;
        if (k % 2 == 0 && !L2.empty()){	//每两个中间插一个 
            res.push_back(L2.back());
            L2.pop_back();
        }
        head = a[head].next;
    }
    
    //输出 
    for (int i = 0; i < res.size() - 1; ++i)
        printf("%05d %d %05d\n", res[i].add, res[i].data, res[i+1].add);
    printf("%05d %d -1", res[res.size()-1].add, res[res.size()-1].data);

	return 0;
}


```

