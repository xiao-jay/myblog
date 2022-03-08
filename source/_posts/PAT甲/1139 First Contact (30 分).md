---
title: 1139 First Contact (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>First Contact</font>

#### 题目描述：

Unlike in nowadays, the way that boys and girls expressing their feelings of love was quite subtle in the early years. When a boy A had a crush on a girl B, he would usually not contact her directly in the first place. Instead, he might ask another boy C, one of his close friends, to ask another girl D, who was a friend of both B and C, to send a message to B -- quite a long shot, isn't it? Girls would do analogously.

Here given a network of friendship relations, you are supposed to help a boy or a girl to list all their friends who can possibly help them making the first contact.

### Input Specification:

Each input file contains one test case. For each case, the first line gives two positive integers N (1 < N ≤ 300) and M, being the total number of people and the number of friendship relations, respectively. Then M lines follow, each gives a pair of friends. Here a person is represented by a 4-digit ID. To tell their genders, we use a negative sign to represent girls.

After the relations, a positive integer K (≤ 100) is given, which is the number of queries. Then K lines of queries follow, each gives a pair of lovers, separated by a space. It is assumed that the first one is having a crush on the second one.

### Output Specification:

For each query, first print in a line the number of different pairs of friends they can find to help them, then in each line print the IDs of a pair of friends.

If the lovers A and B are of opposite genders, you must first print the friend of A who is of the same gender of A, then the friend of B, who is of the same gender of B. If they are of the same gender, then both friends must be in the same gender as theirs. It is guaranteed that each person has only one gender.

The friends must be printed in non-decreasing order of the first IDs, and for the same first ones, in increasing order of the seconds ones.

### Sample Input:

```in
10 18
-2001 1001
-2002 -2001
1004 1001
-2004 -2001
-2003 1005
1005 -2001
1001 -2003
1002 1001
1002 -2004
-2004 1001
1003 -2002
-2003 1003
1004 -2002
-2001 -2003
1001 1003
1003 -2001
1002 -2001
-2002 -2003
5
1001 -2001
-2003 1001
1005 -2001
-2002 -2004
1111 -2003
```

### Sample Output:

```out
4
1002 2004
1003 2002
1003 2003
1004 2002
4
2001 1002
2001 1003
2002 1003
2002 1004
0
1
2003 2001
0
```

#### 思路:

题目意思是a和他的同性好友c，b和她同性好友d，如果c和d是认识的，那就打印出来，这题坑很多

1、需要打印的时候%04

2、a和b 可能是同性的

3、需要考虑-0和+0，只能接收string来做

#### 代码:

```go
#include "iostream"
#include "vector"
#include <algorithm>
#include <unordered_map>
using namespace std;
unordered_map<int, bool> res;
vector<int> v[10000];
struct Node {
    int a,b;
};
bool cmp(Node a,struct Node b){
    return a.a != b.a ? a.a<b.a : a.b<b.b;
}
int main(){
    int m, n = 0;
    cin >>m>>n;
    for(int i=0;i<n;i++){
        string a,b;
        cin >>a>>b;
        if(a.length() == b.length()){
            v[abs(stoi(a))].push_back(abs(stoi(b)));
            v[abs(stoi(b))].push_back(abs(stoi(a)));
        }
        res[abs(stoi(a))*10000 +abs(stoi(b))] = res[abs(stoi(b))*10000 +abs(stoi(a))] = true;
    }
    int p;
    cin >>p;
    for(int i=0; i<p;i++){
        int c,d;
        cin >>c>>d;
        vector<Node> ans;
        for(int j=0;j<v[abs(c)].size();j++){
            for(int k=0 ;k<v[abs(d)].size();k++){
                //是自己
                if (v[abs(c)][j] == abs(d) || abs(c) == v[abs(d)][k]) continue;
                // c和d认识
                if(res[v[abs(c)][j] * 10000 + v[abs(d)][k]]){
                    ans.push_back(Node{.a = v[abs(c)][j],.b = v[abs(d)][k]});
                }
            }
        }
        sort(ans.begin(),ans.end(), cmp);
        cout << ans.size()<<endl;
        for(int j=0;j<ans.size(); j++){
            printf("%04d %04d\n", ans[j].a, ans[j].b);
        }
    }
}
```

