---
title: 1165 Block Reversing (25 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Block Reversing</font>

#### 题目描述：

Given a singly linked list *L*. Let us consider every *K* nodes as a **block** (if there are less than *K* nodes at the end of the list, the rest of the nodes are still considered as a block). Your job is to reverse all the blocks in *L*. For example, given *L* as 1→2→3→4→5→6→7→8 and *K* as 3, your output must be 7→8→4→5→6→1→2→3.

### Input Specification:

Each input file contains one test case. For each case, the first line contains the address of the first node, a positive *N* (≤105) which is the total number of nodes, and a positive *K* (≤*N*) which is the size of a block. The address of a node is a 5-digit nonnegative integer, and NULL is represented by −1.

Then *N* lines follow, each describes a node in the format:

```
Address Data Next
```

where `Address` is the position of the node, `Data` is an integer, and `Next` is the position of the next node.

### Output Specification:

For each case, output the resulting ordered linked list. Each node occupies a line, and is printed in the same format as in the input.

### Sample Input:

```in
00100 8 3
71120 7 88666
00000 4 99999
00100 1 12309
68237 6 71120
33218 3 00000
99999 5 68237
88666 8 -1
12309 2 33218
```

### Sample Output:

```out
71120 7 88666
88666 8 00000
00000 4 99999
99999 5 68237
68237 6 00100
00100 1 12309
12309 2 33218
33218 3 -1
```

#### 思路:

有两种思路做，一种是链表反转，一种是模拟成结果想要的状态，我是第二种

#### 代码:

```go
#include<iostream>
#include<vector>
using namespace std;
struct node{
    int id,dat,next;
};
int main(){
    int begin, n, k;
    scanf("%d%d%d",&begin,&n,&k);
    node a[100010];     //特别注意数组大小
    vector<node> v, ans;
    int s,d,e;
    for(int i=0; i<n; i++){
        scanf("%d%d%d",&s,&d,&e);
        a[s]={s,d,e};
    }
    for(; begin!=-1; begin=a[begin].next){
        v.push_back(a[begin]);
    }
    int num=n/k;
    if(n%k) num++;
    int len=v.size();
    for(int i=num-1; i>=0; i--){
        for(int j=0; j<k; j++){
            int t=i*k+j;
            if(t<len) ans.push_back(v[t]);//注意插入的写法
        }
    }
    int i;
    for(i=0; i<ans.size()-1; i++){
        printf("%05d %d %05d\n", ans[i].id, ans[i].dat, ans[i+1].id);
    }
    printf("%05d %d -1\n", ans[i].id, ans[i].dat);
    return 0;
}

```

