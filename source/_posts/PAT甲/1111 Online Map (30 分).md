---
title: 1111 Online Map (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Online Map </font>

#### 题目描述：

Input our current position and a destination, an online map can recommend several paths. Now your job is to recommend two paths to your user: one is the shortest, and the other is the fastest. It is guaranteed that a path exists for any request.

### Input Specification:

Each input file contains one test case. For each case, the first line gives two positive integers *N* (2≤*N*≤500), and *M*, being the total number of streets intersections on a map, and the number of streets, respectively. Then *M* lines follow, each describes a street in the format:

```
V1 V2 one-way length time
```

where `V1` and `V2` are the indices (from 0 to *N*−1) of the two ends of the street; `one-way` is 1 if the street is one-way from `V1` to `V2`, or 0 if not; `length` is the length of the street; and `time` is the time taken to pass the street.

Finally a pair of source and destination is given.

### Output Specification:

For each case, first print the shortest path from the source to the destination with distance `D` in the format:

```
Distance = D: source -> v1 -> ... -> destination
```

Then in the next line print the fastest path with total time `T`:

```
Time = T: source -> w1 -> ... -> destination
```

In case the shortest path is not unique, output the fastest one among the shortest paths, which is guaranteed to be unique. In case the fastest path is not unique, output the one that passes through the fewest intersections, which is guaranteed to be unique.

In case the shortest and the fastest paths are identical, print them in one line in the format:

```
Distance = D; Time = T: source -> u1 -> ... -> destination
```

### Sample Input 1:

```in
10 15
0 1 0 1 1
8 0 0 1 1
4 8 1 1 1
3 4 0 3 2
3 9 1 4 1
0 6 0 1 1
7 5 1 2 1
8 5 1 2 1
2 3 0 2 2
2 1 1 1 1
1 3 0 3 1
1 4 0 1 1
9 7 1 3 1
5 1 0 5 2
6 5 1 1 2
3 5
```

### Sample Output 1:

```out
Distance = 6: 3 -> 4 -> 8 -> 5
Time = 3: 3 -> 1 -> 5
```

### Sample Input 2:

```in
7 9
0 4 1 1 1
1 6 1 1 3
2 6 1 1 1
2 5 1 2 2
3 0 0 1 1
3 1 1 1 3
3 2 1 1 2
4 5 0 2 2
6 5 1 1 2
3 5
```

### Sample Output 2:

```

```

#### 思路:

比普通dijstra多一个单路和双路，思路是两个dijstra算法和前缀算出最短路径的最短时间和最短时间路径最短。

#### 代码:

```go
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;
const int inf = 999999999;
int dis[510], Time[510], e[510][510], w[510][510], dispre[510],Timepre[510], weight[510],NodeNum[510];
bool visit[510];
vector<int> dispath, Timepath, temppath;
int st, fin, minnode = inf;
void dfsdispath(int v) {
    dispath.push_back(v);
    if(v == st) return ;
    dfsdispath(dispre[v]);
}
void dfsTimepath(int v) {
    Timepath.push_back(v);
    if(v == st) return ;
    dfsTimepath(Timepre[v]);
}
int main() {
    fill(dis, dis + 510, inf);
    fill(Time, Time + 510, inf);
    fill(weight, weight + 510, inf);
    fill(e[0], e[0] + 510 * 510, inf);
    fill(w[0], w[0] + 510 * 510, inf);
    int n, m;
    scanf("%d %d", &n, &m);
    int a, b, flag, len, t;
    for(int i = 0; i < m; i++) {
        scanf("%d %d %d %d %d", &a, &b, &flag, &len, &t);
        e[a][b] = len;
        w[a][b] = t;
        if(flag != 1) {
            e[b][a] = len;
            w[b][a] = t;
        }
    }
    scanf("%d %d", &st, &fin);
    dis[st] = 0;
    for(int i = 0; i < n; i++)
        dispre[i] = i;
    for(int i = 0; i < n; i++) {
        int u = -1, minn = inf;
        for(int j = 0; j < n; j++) {
            if(visit[j] == false && dis[j] < minn) {
                u = j;
                minn = dis[j];
            }
        }
        if(u == -1) break;
        visit[u] = true;
        for(int v = 0; v < n; v++) {
            if(visit[v] == false && e[u][v] != inf) {
                if(e[u][v] + dis[u] < dis[v]) {
                    dis[v] = e[u][v] + dis[u];
                    dispre[v] = u;
                    weight[v] = weight[u] + w[u][v];
                } else if(e[u][v] + dis[u] == dis[v] && weight[v] > weight[u] + w[u][v]) {
                    weight[v] = weight[u] + w[u][v];
                    dispre[v] = u;
                }
            }
        }
    }
    dfsdispath(fin);
    Time[st] = 0;
    fill(visit, visit + 510, false);
    for(int i = 0; i < n; i++) {
        int u = -1, minn = inf;
        for(int j = 0; j < n; j++) {
            if(visit[j] == false && minn > Time[j]) {
                u = j;
                minn = Time[j];
            }
        }
        if(u == -1) break;
        visit[u] = true;
        for(int v = 0; v < n; v++) {
            if(visit[v] == false && w[u][v] != inf) {
                if(w[u][v] + Time[u] < Time[v]) {
                    Time[v] = w[u][v] + Time[u];
                    Timepre[v]= u;
                    NodeNum[v]=NodeNum[u]+1;
                } else if(w[u][v] + Time[u] == Time[v]&&NodeNum[u]+1<NodeNum[v]) {
                    Timepre[v]= u;
                    NodeNum[v]=NodeNum[u]+1;
                }
            }
        }
    }
    dfsTimepath(fin);
    printf("Distance = %d", dis[fin]);
    if(dispath == Timepath) {
        printf("; Time = %d: ", Time[fin]);
    } else {
        printf(": ");
        for(int i = dispath.size() - 1; i >= 0; i--) {
            printf("%d", dispath[i]);
            if(i != 0) printf(" -> ");
        }
        printf("\nTime = %d: ", Time[fin]);
    }
    for(int i = Timepath.size() - 1; i >= 0; i--) {
        printf("%d", Timepath[i]);
        if(i != 0) printf(" -> ");
    }
    return 0;
}
```

