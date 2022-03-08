---
title: 1131 Subway Map (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Subway Map</font>

#### 题目描述：

In the big cities, the subway systems always look so complex to the visitors. To give you some sense, the following figure shows the map of Beijing subway. Now you are supposed to help people with your computer skills! Given the starting position of your user, your task is to find the quickest way to his/her destination.

![subwaymap.jpg](https://images.ptausercontent.com/55799c23-4bdb-4e32-af7f-6d41accfdd2b.jpg)

### Input Specification:

Each input file contains one test case. For each case, the first line contains a positive integer *N* (≤ 100), the number of subway lines. Then *N* lines follow, with the *i*-th (*i*=1,⋯,*N*) line describes the *i*-th subway line in the format:

*M* S[1] S[2] ... S[*M*]

where *M* (≤ 100) is the number of stops, and S[*i*]'s (*i*=1,⋯,*M*) are the indices of the stations (the indices are 4-digit numbers from 0000 to 9999) along the line. It is guaranteed that the stations are given in the correct order -- that is, the train travels between S[*i*] and S[*i*+1] (*i*=1,⋯,*M*−1) without any stop.

Note: It is possible to have loops, but not self-loop (no train starts from S and stops at S without passing through another station). Each station interval belongs to a unique subway line. Although the lines may cross each other at some stations (so called "transfer stations"), no station can be the conjunction of more than 5 lines.

After the description of the subway, another positive integer *K* (≤ 10) is given. Then *K* lines follow, each gives a query from your user: the two indices as the starting station and the destination, respectively.

The following figure shows the sample map.

![samplemap.jpg](https://images.ptausercontent.com/932c8f1b-7dd5-489d-a774-a91c1fabba7f.jpg)

Note: It is guaranteed that all the stations are reachable, and all the queries consist of legal station numbers.

### Output Specification:

For each query, first print in a line the minimum number of stops. Then you are supposed to show the optimal path in a friendly format as the following:

```
Take Line#X1 from S1 to S2.
Take Line#X2 from S2 to S3.
......
```

where `X`*i*'s are the line numbers and `S`*i*'s are the station indices. Note: Besides the starting and ending stations, only the transfer stations shall be printed.

If the quickest path is not unique, output the one with the minimum number of transfers, which is guaranteed to be unique.

### Sample Input:

```in
4
7 1001 3212 1003 1204 1005 1306 7797
9 9988 2333 1204 2006 2005 2004 2003 2302 2001
13 3011 3812 3013 3001 1306 3003 2333 3066 3212 3008 2302 3010 3011
4 6666 8432 4011 1306
3
3011 3013
6666 2001
2004 3001
```

### Sample Output:

```out
2
Take Line#3 from 3011 to 3013.
10
Take Line#4 from 6666 to 1306.
Take Line#3 from 1306 to 2302.
Take Line#2 from 2302 to 2001.
6
Take Line#2 from 2004 to 1204.
Take Line#1 from 1204 to 1306.
Take Line#3 from 1306 to 3001.
```

#### 思路:

自己做还是做不出来啊，虽然思路想出来了但是太麻烦导致做不出来，还是太菜，通过dfs搜索来做，弄一个visit数组来存储已经访问过的点，并且地铁可以双向通行的。

#### 代码:

```go
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
vector<vector<int>> v(10000);
int visit[10000], minCnt, minTransfer, start, end1;
unordered_map<int, int> line;
vector<int> path, tempPath;
int transferCnt(vector<int> a) {
    int cnt = -1, preLine = 0;
    for (int i = 1; i < a.size(); i++) {
        if (line[a[i-1]*10000+a[i]] != preLine) cnt++;
        preLine = line[a[i-1]*10000+a[i]];
    }
    return cnt;
}
void dfs(int node, int cnt) {
    if (node == end1 && (cnt < minCnt || (cnt == minCnt && transferCnt(tempPath) < minTransfer))) {
        minCnt = cnt;
        minTransfer = transferCnt(tempPath);
        path = tempPath;
    }
    if (node == end1) return;
    for (int i = 0; i < v[node].size(); i++) {
        if (visit[v[node][i]] == 0) {
            visit[v[node][i]] = 1;
            tempPath.push_back(v[node][i]);
            dfs(v[node][i], cnt + 1);
            visit[v[node][i]] = 0;
            tempPath.pop_back();
        }
    }
}
int main() {
    int n, m, k, pre, temp;
    scanf("%d", &n);
    for (int i = 0; i < n; i++) {
        scanf("%d%d", &m, &pre);
        for (int j = 1; j < m; j++) {
            scanf("%d", &temp);
            v[pre].push_back(temp);
            v[temp].push_back(pre);
            line[pre*10000+temp] = line[temp*10000+pre] = i + 1;
            pre = temp;
        }
    }
    scanf("%d", &k);
    for (int i = 0; i < k; i++) {
        scanf("%d%d", &start, &end1);
        minCnt = 99999, minTransfer = 99999;
        tempPath.clear();
        tempPath.push_back(start);
        visit[start] = 1;
        dfs(start, 0);
        visit[start] = 0;
        printf("%d\n", minCnt);
        int preLine = 0, preTransfer = start;
        for (int j = 1; j < path.size(); j++) {
            if (line[path[j-1]*10000+path[j]] != preLine) {
                if (preLine != 0) printf("Take Line#%d from %04d to %04d.\n", preLine, preTransfer, path[j-1]);
                preLine = line[path[j-1]*10000+path[j]];
                preTransfer = path[j-1];
            }
        }
        printf("Take Line#%d from %04d to %04d.\n", preLine, preTransfer, end1);
    }
    return 0;
}
```

