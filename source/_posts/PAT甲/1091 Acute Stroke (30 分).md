---
title: 1091 Acute Stroke (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Acute Stroke (30 分)</font>

#### 题目描述：

One important factor to identify acute stroke (急性脑卒中) is the volume of the stroke core. Given the results of image analysis in which the core regions are identified in each MRI slice, your job is to calculate the volume of the stroke core.

### Input Specification:

Each input file contains one test case. For each case, the first line contains 4 positive integers: *M*, *N*, *L* and *T*, where *M* and *N* are the sizes of each slice (i.e. pixels of a slice are in an *M*×*N* matrix, and the maximum resolution is 1286 by 128); *L* (≤60) is the number of slices of a brain; and *T* is the integer threshold (i.e. if the volume of a connected core is less than *T*, then that core must not be counted).

Then *L* slices are given. Each slice is represented by an *M*×*N* matrix of 0's and 1's, where 1 represents a pixel of stroke, and 0 means normal. Since the thickness of a slice is a constant, we only have to count the number of 1's to obtain the volume. However, there might be several separated core regions in a brain, and only those with their volumes no less than *T* are counted. Two pixels are **connected** and hence belong to the same region if they share a common side, as shown by Figure 1 where all the 6 red pixels are connected to the blue one.

![figstroke.jpg](https://images.ptausercontent.com/f85c00cc-62ce-41ff-8dd0-d1c288d87409.jpg)

Figure 1

### Output Specification:

For each case, output in a line the total volume of the stroke core.

### Sample Input:

```in
3 4 5 2
1 1 1 1
1 1 1 1
1 1 1 1
0 0 1 1
0 0 1 1
0 0 1 1
1 0 1 1
0 1 0 0
0 0 0 0
1 0 1 1
0 0 0 0
0 0 0 0
0 0 0 1
0 0 0 1
1 0 0 0
```

### Sample Output:

```out
26
```

#### 思路:

一个三维bfs题，题目不难，细节难，dfs会爆栈不行，bfs不能用vector要用queue，不然会超时。bfs细节是vis[endd.x][endd.y][endd.z] = true;这句话放的位置很重要

#### 代码:

```go
#include<iostream>
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<vector>
#include<map>
#include<queue>
using namespace std;
int maze[60][1286][128];
int vis[60][1286][128];
int n,m,l,t;
int nxt[6][3] = {{1,0,0},{-1,0,0},{0,1,0},{0,-1,0},{0,0,1},{0,0,-1}};
struct node
{
    int x,y,z;
};
int cnt = 0;
int total = 0;
void BFS(int i,int j,int k)
{
    int cnt = 0;
    queue<node>qu;
    node s = {i,j,k};
    qu.push(s);
    vis[i][j][k] = true;
    while(!qu.empty())
    {
        node tmp = qu.front();
        qu.pop();
        node endd;
        cnt++;
        for(int i = 0 ; i < 6; i++)
        {
            endd.x = tmp.x + nxt[i][0];
            endd.y = tmp.y + nxt[i][1];
            endd.z = tmp.z + nxt[i][2];
            if(endd.x >=0 && endd.x < l && endd.y >= 0 && endd.y < n && endd.z >= 0 && endd.z < m
               && vis[endd.x][endd.y][endd.z] == false  && maze[endd.x][endd.y][endd.z] == 1)
            {
                vis[endd.x][endd.y][endd.z] = true;
                qu.push(endd);
            }
        }
    }
    if(cnt >= t)
        total += cnt;
}
int main()
{
    scanf("%d%d%d%d",&n,&m,&l,&t);
    for(int i = 0; i < l; i++)
    {
        for(int j = 0; j < n; j++)
        {
            for(int k = 0; k < m; k++)
                scanf("%d",&maze[i][j][k]);
        }
    }//表示输入

    for(int i = 0; i < l; i++)
    {
        for(int j = 0; j < n; j++)
        {
            for(int k = 0; k < m; k++)
            {
                node s = {i,j,k};
                if(maze[i][j][k] == 1 && !vis[i][j][k])//首先这个东西是1? 其次还没有走过它
                    BFS(i,j,k);
            }
        }
    }
    printf("%d\n",total);
    return 0;
}
```

