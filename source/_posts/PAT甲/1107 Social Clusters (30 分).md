---
title: 1107 Social Clusters (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Social Clusters</font>

#### 题目描述：

When register on a social network, you are always asked to specify your hobbies in order to find some potential friends with the same hobbies. A **social cluster** is a set of people who have some of their hobbies in common. You are supposed to find all the clusters.

### Input Specification:

Each input file contains one test case. For each test case, the first line contains a positive integer *N* (≤1000), the total number of people in a social network. Hence the people are numbered from 1 to *N*. Then *N* lines follow, each gives the hobby list of a person in the format:

*K**i*: *h**i*[1] *h**i*[2] ... *h**i*[*K**i*]

where *K**i* (>0) is the number of hobbies, and *h**i*[*j*] is the index of the *j*-th hobby, which is an integer in [1, 1000].

### Output Specification:

For each case, print in one line the total number of clusters in the network. Then in the second line, print the numbers of people in the clusters in non-increasing order. The numbers must be separated by exactly one space, and there must be no extra space at the end of the line.

### Sample Input:

```in
8
3: 2 7 10
1: 4
2: 5 3
1: 4
1: 3
1: 4
4: 6 8 1 5
1: 4
```

### Sample Output:

```out
3
4 3 1
```

#### 思路:

纯纯的并查集题目

#### 代码:

```go
#include "iostream"
#include "string.h"
#include "algorithm"
using namespace std;

int father[1001],hobby[1001];
int num[1001];
int find(int a){
    while(a != father[a]){
        a = father[a];
    }
    return a;
}

void Union(int a,int b){
    int fa = find(a);
    int fb = find(b);
    if(fa != fb){
        father[fb] = fa;
    }
}
bool cmp(int a,int b){
    return a>b;
}
int main(){
    int n;
    cin >>n;
    memset(hobby, 0 ,sizeof (hobby));
    memset(num, 0 ,sizeof (hobby));
    for(int i=1;i<=n ;i++){
        father[i] = i;
    }
    for(int i=1;i<=n;i++){
        int num1;
        scanf("%d: ",&num1);
        for(int j=0;j < num1; j++){
            int t ;
            cin >>t;
            if( hobby[t] == 0)  hobby[t] = i;
            else {
               Union(hobby[t], i);
           }
        }
    }
    int sum = 0;
    for(int i=1; i<=n; i++){
        int temp = find(i);
        if (i == temp) sum++;
        num[temp]++;
    }
    sort(num,num+1000,cmp);
    cout << sum<<endl;
    for(int i=0;i<sum;i++){
        if(i == 0){
            cout<<num[i];
        }else{
            cout<<" "<<num[i];
        }
    }
}
```

