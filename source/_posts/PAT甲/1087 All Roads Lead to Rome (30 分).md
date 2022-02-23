---
title: 1087 All Roads Lead to Rome (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>All Roads Lead to Rome</font>

#### 题目描述：

Indeed there are many different tourist routes from our city to Rome. You are supposed to find your clients the route with the least cost while gaining the most happiness.

### Input Specification:

Each input file contains one test case. For each case, the first line contains 2 positive integers *N* (2≤*N*≤200), the number of cities, and *K*, the total number of routes between pairs of cities; followed by the name of the starting city. The next *N*−1 lines each gives the name of a city and an integer that represents the happiness one can gain from that city, except the starting city. Then *K* lines follow, each describes a route between two cities in the format `City1 City2 Cost`. Here the name of a city is a string of 3 capital English letters, and the destination is always `ROM` which represents Rome.

### Output Specification:

For each test case, we are supposed to find the route with the least cost. If such a route is not unique, the one with the maximum happiness will be recommanded. If such a route is still not unique, then we output the one with the maximum average happiness -- it is guaranteed by the judge that such a solution exists and is unique.

Hence in the first line of output, you must print 4 numbers: the number of different routes with the least cost, the cost, the happiness, and the average happiness (take the integer part only) of the recommanded route. Then in the next line, you are supposed to print the route in the format `City1->City2->...->ROM`.

### Sample Input:

```in
6 7 HZH
ROM 100
PKN 40
GDN 55
PRS 95
BLN 80
ROM GDN 1
BLN ROM 1
HZH PKN 1
PRS ROM 2
BLN HZH 2
PKN GDN 1
HZH PRS 1
```

### Sample Output:

```out
3 3 195 97
HZH->PRS->ROM
```

#### 思路:

和1030题很像，动态规划➕dfs，用pre数组存储这个节点之前的节点

#### 代码:

```go
#include "iostream"
#include "vector"
#include "string.h"
#include "map"
using namespace std;
#define inf 0x3f3f3f3f
int dis[201][201];
int dest[201];
int vis[201];
vector<int> pre[201],temp,res;
int maxHappy = 0;
struct City{
    int no,happy;
    string name;
};
int m,n;
string start;
map<string, City> mapCity;
map<int, City> mapCityNo;
int pathNumber=0;
void dfs(int v){
    temp.push_back(v);
    if(v == mapCity[start].no){
        pathNumber ++;
        int tempHappy = 0;
        for (int i=0;i<temp.size(); i++){
            tempHappy += mapCityNo[temp[i]].happy;
        }
        if(tempHappy > maxHappy){
            maxHappy = tempHappy;
            res = temp;
        }
        temp.pop_back();
        return ;
    }
    for(int i: pre[v]) dfs(i);
    temp.pop_back();
}
int main(){
    memset(dis,inf, sizeof(dis));
    memset(dest,inf, sizeof(dest));
    memset(vis,0, sizeof(vis));
    cin >>m>>n>>start;
    City city;
    city.no = 0;
    city.name = start;
    city.happy = 0;
    mapCity[start] = city;
    mapCityNo[0] = city;
    for(int i=1;i<m;i++){
        City node;
        cin >>node.name >>node.happy;
        node.no = i;
        mapCity[node.name] = node;
        mapCityNo[node.no] = node;
    }
    for(int i=0; i<n; i++){
        string a,b;
        int c;
        cin >>a>>b>>c;
        dis[mapCity[a].no][mapCity[b].no] =dis[mapCity[b].no][mapCity[a].no]= c;
    }
    dest[0] = 0;
    for(int i=0;i<m; i++){
        int u = -1, minn = inf;
        for(int j=0;j<m;j++){
            if( vis[j] == 0 && dest[j] < minn){
                minn = dest[j];
                u = j;
            }
        }
        if(u == -1) break;
        vis[u] = 1;
        for(int j=0;j<m ; j++){
            if(vis[j] == 0 && dis[u][j] != inf){
                if(dest[j] >dest[u] + dis[u][j]){
                    dest[j] = dest[u] + dis[u][j];
                    pre[j].clear();
                    pre[j].push_back(u);
                }else if(dest[j] == dest[u] + dis[u][j]){
                    pre[j].push_back(u);
                }
            }
        }
    }
    dfs(mapCity["ROM"].no);
    cout << pathNumber<<" "<<dest[mapCity["ROM"].no]<<" "<<maxHappy<<" "<<maxHappy/(res.size()-1)<<endl;
    for(int i=res.size()-1;i >=0 ;i--){
        if(i == res.size()-1){
            cout << mapCityNo[res[i]].name;
        }else{
           cout <<  "->"<< mapCityNo[res[i]].name;
        }
    }
}
```

