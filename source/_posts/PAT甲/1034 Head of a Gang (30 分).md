---
title: 1034 Head of a Gang (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Head of a Gang</font>

#### 题目描述：

One way that the police finds the head of a gang is to check people's phone calls. If there is a phone call between *A* and *B*, we say that *A* and *B* is related. The weight of a relation is defined to be the total time length of all the phone calls made between the two persons. A "Gang" is a cluster of more than 2 persons who are related to each other with total relation weight being greater than a given threshold *K*. In each gang, the one with maximum total weight is the head. Now given a list of phone calls, you are supposed to find the gangs and the heads.

### Input Specification:

Each input file contains one test case. For each case, the first line contains two positive numbers *N* and *K* (both less than or equal to 1000), the number of phone calls and the weight threthold, respectively. Then *N* lines follow, each in the following format:

```
Name1 Name2 Time
```

where `Name1` and `Name2` are the names of people at the two ends of the call, and `Time` is the length of the call. A name is a string of three capital letters chosen from `A`-`Z`. A time length is a positive integer which is no more than 1000 minutes.

### Output Specification:

For each test case, first print in a line the total number of gangs. Then for each gang, print in a line the name of the head and the total number of the members. It is guaranteed that the head is unique for each gang. The output must be sorted according to the alphabetical order of the names of the heads.

### Sample Input 1:

```in
8 59
AAA BBB 10
BBB AAA 20
AAA CCC 40
DDD EEE 5
EEE DDD 70
FFF GGG 30
GGG HHH 20
HHH FFF 10
```

### Sample Output 1:

```out
2
AAA 3
GGG 3
```

### Sample Input 2:

```in
8 70
AAA BBB 10
BBB AAA 20
AAA CCC 40
DDD EEE 5
EEE DDD 70
FFF GGG 30
GGG HHH 20
HHH FFF 10
```

### Sample Output 2:

```out
0
```

#### 思路:

先把字母转化成编号，用dfs遍历每一个节点，遍历的时候计算出总重和总人数，并且用vis数组来保证一个节点只遍历一次，注意遍历完之后要G[u][i] = G[i][u] = 0;保证没有回边

注意点1:

```
G[id1][id2] += time;
G[id2][id1] += time;
```

因为可能有 重复的情况，比如aaa bbb 1，bbb aaa 2

#### 代码:

```go
#include <iostream>
#include <map>
using namespace std;
map<string, int> stringToInt;
map<int, string> intToString;
map<string, int> ans;
int G[2010][2010], weight[2010];
bool vis[2010];
int stringId=1;
int creatId(string a){
    if(stringToInt.find(a) != stringToInt.end()){
        return stringToInt[a];
    }
    intToString[stringId] = a;
    stringToInt[a] = stringId;
    return stringId++;
}

void dfs(int u,int &head,int &membernum,int &totalweight){
    vis[u] = true;
    if(weight[head] < weight[u]) head = u;
    membernum++;
    for(int i=1;i<stringId;i++){
        if(G[i][u] >0 ){
            totalweight += G[u][i];
            G[u][i] = G[i][u] = 0;
            if(!vis[i]){
                dfs(i,head,membernum,totalweight);
            }

        }
    }
}

int main(){
    int m,n;
    string a,b;
    int time;
    cin >>m>>n;

    int cnt = 0;
    while(m--){
        cin >>a >>b>>time;
        int id1 = creatId(a);
        int id2 = creatId(b);
        weight[id1] += time;
        weight[id2] += time;
        G[id1][id2] += time;
        G[id2][id1] += time;
    }

   for(int i=1;i<stringId;i++){
       if(vis[i] == false){
           int head = i,membernum = 0,totalweight = 0;
           dfs(i,head,membernum,totalweight);
           if(totalweight > n && membernum >2){
               ans[intToString[head]] = membernum;
           }
       }
   }
   cout << ans.size()<<endl;
   for( auto i = ans.begin();i!= ans.end();i++){
       if(i != ans.begin()) cout <<endl;
       cout << i->first<<" "<<i->second;
   }
    return 0;
}

```

