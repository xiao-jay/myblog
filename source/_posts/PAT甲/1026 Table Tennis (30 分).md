---
title: 1026 Table Tennis (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Table Tennis</font>

#### 题目描述：

A table tennis club has N tables available to the public. The tables are numbered from 1 to N. For any pair of players, if there are some tables open when they arrive, they will be assigned to the available table with the smallest number. If all the tables are occupied, they will have to wait in a queue. It is assumed that every pair of players can play for at most 2 hours.

Your job is to count for everyone in queue their waiting time, and for each table the number of players it has served for the day.

One thing that makes this procedure a bit complicated is that the club reserves some tables for their VIP members. When a VIP table is open, the first VIP pair in the queue will have the privilege to take it. However, if there is no VIP in the queue, the next pair of players can take it. On the other hand, if when it is the turn of a VIP pair, yet no VIP table is available, they can be assigned as any ordinary players.

### Input Specification:

Each input file contains one test case. For each case, the first line contains an integer `N` (≤10000) - the total number of pairs of players. Then `N` lines follow, each contains 2 times and a VIP tag: `HH:MM:SS` - the arriving time, `P` - the playing time in minutes of a pair of players, and `tag` - which is 1 if they hold a VIP card, or 0 if not. It is guaranteed that the arriving time is between 08:00:00 and 21:00:00 while the club is open. It is assumed that no two customers arrives at the same time. Following the players' info, there are 2 positive integers: `K` (≤100) - the number of tables, and `M` (< K) - the number of VIP tables. The last line contains `M` table numbers.

### Output Specification:

For each test case, first print the arriving time, serving time and the waiting time for each pair of players in the format shown by the sample. Then print in a line the number of players served by each table. Notice that the output must be listed in chronological order of the serving time. The waiting time must be rounded up to an integer minute(s). If one cannot get a table before the closing time, their information must NOT be printed.

### Sample Input:

```in
10
20:52:00 10 0
08:00:00 20 0
08:02:00 30 0
20:51:00 10 0
08:10:00 30 0
08:12:00 10 1
20:40:00 13 0
08:01:30 15 1
20:53:00 10 1
20:54:00 10 0
3 1
2
```

### Sample Output:

```out
08:00:00 08:00:00 0
08:01:30 08:01:30 0
08:02:00 08:02:00 0
08:12:00 08:16:30 5
08:10:00 08:20:00 10
20:40:00 20:40:00 0
20:51:00 20:51:00 0
20:52:00 20:52:00 0
20:53:00 20:53:00 0
4 3 2
```

**鸣谢用户 黄卓斌 补充数据！鸣谢用户 徐向荣 修正标程！**

#### 思路:

柳神代码有问题，我还给他提了pr，开心

**分析：建立两个结构体，person 和 tablenode，分别创建他们的结构体数组player和table。**
**因为给出的输入是无序的所以要先排序。可以根据最早空闲的桌子是不是vip桌进行分类讨论。**
**如果最早空闲的桌子index是vip桌，那么寻找队列里面第一个vip球员。根据他的到达时间继续分类讨论。**
**如果最早空闲的桌子index不是vip桌，那么看队首的球员是不是vip球员。如果是普通人，就直接把球桌分配给他，如果是vip，那么需要找到最早空闲的vip桌子的号vipindex，根据vip球员的到达时间和vipindex桌子的空闲时间继续分类讨论。**
**分配的时候标记table的num++，统计该table服务的人数。**

#### 代码:

```go
#include <iostream>
#include <vector>
#include <algorithm>
#include <cmath>
using namespace std;
struct person {
    int arrive, start, time;
    bool vip;
}tempperson;
struct tablenode {
    int end = 8 * 3600, num;
    bool vip;
};
bool cmp1(person a, person b) {
    return a.arrive < b.arrive;
}
bool cmp2(person a, person b) {
    return a.start < b.start;
}
vector<person> player;
vector<tablenode> table;
void alloctable(int personid, int tableid) {
    if(player[personid].arrive <= table[tableid].end)
        player[personid].start = table[tableid].end;
    else
        player[personid].start = player[personid].arrive;
    table[tableid].end = player[personid].start + player[personid].time;
    table[tableid].num++;
}

// 找到第一个vip用户
int findnextvip(int vipid) {
    vipid++;
    while(vipid < player.size() && !player[vipid].vip) vipid++;
    return vipid;
}
int main() {
    int n, k, m, viptable;
    scanf("%d", &n);
    for(int i = 0; i < n; i++) {
        int h, m, s, temptime, flag;
        scanf("%d:%d:%d %d %d", &h, &m, &s, &temptime, &flag);
        tempperson.arrive = h * 3600 + m * 60 + s;
        tempperson.start = 21 * 3600;
        if(tempperson.arrive >= 21 * 3600) continue;
        tempperson.time = temptime <= 120 ? temptime * 60 : 7200;
        tempperson.vip = (flag == 1);
        player.push_back(tempperson);
    }
    scanf("%d%d", &k, &m);
    table.resize(k + 1);
    for(int i = 0; i < m; i++) {
        scanf("%d", &viptable);
        table[viptable].vip = true;
    }
    sort(player.begin(), player.end(), cmp1);
    int i = 0, vipid = -1;
    vipid = findnextvip(vipid);
    while(i < player.size()) {
        int index = -1, minendtime = 999999999;
        // 找到最早结束的桌子
        for(int j = 1; j <= k; j++) {
            if(table[j].end < minendtime) {
                minendtime = table[j].end;
                index = j;
            }
        }
        if(table[index].end >= 21 * 3600) break;
        // 这个vip已经被分配过了
        if(player[i].vip && i < vipid) {
            i++;
            continue;
        }
        // 第一个结束的桌子是vip桌
        if(table[index].vip) {
            // 下一个玩家刚好是vip
            if(player[i].vip) {
                alloctable(i, index);
                if(vipid == i) vipid = findnextvip(vipid);
                i++;
            } else {  //vip桌子空闲了但是下一个不是vip
                // 看看是否有空闲的非vip桌子
                bool flag = true;
               for (int j=1;j<=index;j++){
                   if(!table[j].vip && player[i].arrive >table[j].end){
                       // 分配给普通用户
                       alloctable(i, j);
                       i++;
                       flag = false;
                       break;
                   }
               }
               if(flag){
                   // 最近的vip在桌子结束前到达
                   if(vipid < player.size() && player[vipid].arrive <= table[index].end) {
                       alloctable(vipid, index);
                       vipid = findnextvip(vipid);
                   } else {
                       // 分配给普通用户
                       alloctable(i, index);
                       i++;
                   }
               }

            }
        } else { // 第一个结束的桌子不是vip桌
            if(!player[i].vip) { // 最近到达的用户不是vip，就分配给他
                alloctable(i, index);
                i++;
            } else {//最近的用户是vip
                int vipindex = -1, minvipendtime = 999999999;
                // 看是最快的vip桌哪时候空闲下来
                for(int j = 1; j <= k; j++) {
                    if(table[j].vip && table[j].end < minvipendtime) {
                        minvipendtime = table[j].end;
                        vipindex = j;
                    }
                }
                // vip桌在vip玩家来之前就空闲下来就分配vip桌给vip用户
                if(vipindex != -1 && player[i].arrive >= table[vipindex].end) {
                    alloctable(i, vipindex);
                    if(vipid == i) vipid = findnextvip(vipid);
                    i++;
                } else {
                    alloctable(i, index);
                    if(vipid == i) vipid = findnextvip(vipid);
                    i++;
                }
            }
        }
    }
    sort(player.begin(), player.end(), cmp2);
    for(i = 0; i < player.size() && player[i].start < 21 * 3600; i++) {
        printf("%02d:%02d:%02d ", player[i].arrive / 3600, player[i].arrive % 3600 / 60, player[i].arrive % 60);
        printf("%02d:%02d:%02d ", player[i].start / 3600, player[i].start % 3600 / 60, player[i].start % 60);
        printf("%.0f\n", round((player[i].start - player[i].arrive) / 60.0));
    }
    for(int i = 1; i <= k; i++) {
        if(i != 1) printf(" ");
        printf("%d", table[i].num);
    }
    return 0;
}
```

