---
title: 1030 Travel Plan (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Travel Plan</font>

#### 题目描述：

A traveler's map gives the distances between cities along the highways, together with the cost of each highway. Now you are supposed to write a program to help a traveler to decide the shortest path between his/her starting city and the destination. If such a shortest path is not unique, you are supposed to output the one with the minimum cost, which is guaranteed to be unique.

### Input Specification:

Each input file contains one test case. Each case starts with a line containing 4 positive integers *N*, *M*, *S*, and *D*, where *N* (≤500) is the number of cities (and hence the cities are numbered from 0 to *N*−1); *M* is the number of highways; *S* and *D* are the starting and the destination cities, respectively. Then *M* lines follow, each provides the information of a highway, in the format:

```
City1 City2 Distance Cost
```

where the numbers are all integers no more than 500, and are separated by a space.

### Output Specification:

For each test case, print in one line the cities along the shortest path from the starting point to the destination, followed by the total distance and the total cost of the path. The numbers must be separated by a space and there must be no extra space at the end of output.

### Sample Input:

```in
4 5 0 3
0 1 1 20
1 3 2 30
0 3 4 10
0 2 2 20
2 3 1 20
```

### Sample Output:

```out
0 2 3 3 40
```

#### 思路:

用狄杰斯特拉算法做，再加上一个pre数组来记录前一个数，再dfs来遍历最小cost

#### 代码:

```go
#include "iostream"
#include "string.h"
#include "vector"

#define inf 0x3f3f3f
using  namespace std;
int dest[501][501];
vector<int> pre[501];
vector<int>temp, res;
int costs[501][501];
int vis[501];
int dis[501];
int n,m,s,d;
int minCost = inf;
// 得到最短距离结果之后从后往前遍历最小花费
void dfs(int v){
    temp.push_back(v);
    if(v == s){
        int tempCost = 0;
        for(int i=1;i<temp.size(); i++){
            tempCost += costs[temp[i-1]][temp[i]];
        }
        if(minCost > tempCost){
            res = temp;
            minCost = tempCost;
        }
        temp.pop_back();
        return ;
    }
    for(int i = 0; i < pre[v].size(); i++)
        dfs(pre[v][i]);
    temp.pop_back();
}
int main(){
    memset(dis,inf, sizeof (dis));
    fill(dest[0],dest[0]+501*501, inf);
    cin >>n>>m>>s>>d;

    memset(vis,0, sizeof(vis));
    int a,b,distance,cost;
    while(m--){
        cin >>a>>b>>distance>>cost;
        dest[a][b] = dest[b][a] = distance;
        costs[a][b] = costs[b][a] = cost;
    }
    dis[s] = 0;
   for(int i=0;i<n ;i++){
       int u = -1,minn = inf;
       for (int j=0; j<n; j++){
           if(vis[j] == 0 && ( minn > dis[j])){
               minn = dis[j];
               u = j;
           }
       }
       if(u == -1) break;
       vis[u] = 1;
       for(int j=0; j<n;j++){
           if( !vis[j] && dest[u][j] != inf){
               if (dis[j] > dis[u] + dest[u][j]){
                   dis[j] = dis[u] + dest[u][j];
                   pre[j].clear();
                   pre[j].push_back(u);
               }else if (dis[j] == dis[u] + dest[u][j]){
                   pre[j].push_back(u);
               }
           }
       }
   }
    dfs(d);
   for(int i=res.size()-1;i>=0;i--){
       if(i == res.size()-1) cout <<res[i];
       else cout <<" "<<res[i];
   }
   cout <<" "<<dis[d]<<" "<<minCost;
}
```

