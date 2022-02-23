---
title: 1103 Integer Factorization (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Integer Factorization </font>

#### 题目描述：

The *K*−*P* factorization of a positive integer *N* is to write *N* as the sum of the *P*-th power of *K* positive integers. You are supposed to write a program to find the *K*−*P* factorization of *N* for any positive integers *N*, *K* and *P*.

### Input Specification:

Each input file contains one test case which gives in a line the three positive integers *N* (≤400), *K* (≤*N*) and *P* (1<*P*≤7). The numbers in a line are separated by a space.

### Output Specification:

For each case, if the solution exists, output in the format:

```
N = n[1]^P + ... n[K]^P
```

where `n[i]` (`i` = 1, ..., `K`) is the `i`-th factor. All the factors must be printed in non-increasing order.

Note: the solution may not be unique. For example, the 5-2 factorization of 169 has 9 solutions, such as 122+42+22+22+12, or 112+62+22+22+22, or more. You must output the one with the maximum sum of the factors. If there is a tie, the largest factor sequence must be chosen -- sequence { *a*1,*a*2,⋯,*a**K* } is said to be **larger** than { *b*1,*b*2,⋯,*b**K* } if there exists 1≤*L*≤*K* such that *a**i*=*b**i* for *i*<*L* and *a**L*>*b**L*.

If there is no solution, simple output `Impossible`.

### Sample Input 1:

```in
169 5 2
```

### Sample Output 1:

```out
169 = 6^2 + 6^2 + 6^2 + 6^2 + 5^2
```

### Sample Input 2:

```in
169 167 3
```

### Sample Output 2:

```out
Impossible
```

#### 思路:

用dfs 遍历所有可能性，并且选择合最大的一组组合

#### 代码:

```go
#include "iostream"
#include "vector"
#include "math.h"
using namespace std;
int n,p,k;
vector<int> res,temp,fac;
int indexSum = -9999;
void dfs(int index, int nowK,int sum, int facSum){
    if(nowK > k || sum >n) return ;
    if (sum == n && nowK == k){
        if(facSum > indexSum ){
            indexSum = facSum;
            res = temp;
        }
        return ;
    }
    if (index >=1){
        temp.push_back(index);
        dfs(index, nowK+1, sum+fac[index], facSum+index);
        temp.pop_back();
        dfs(index-1, nowK, sum , facSum );
    }

}
int main(){
    cin >> n>>k>>p;
    
    int tmp =0;
    int j = 1;
    while(tmp <= n){
        fac.push_back(tmp);
        tmp = int(pow(j++,p));
    }
    dfs(fac.size()-1, 0,0,0);
    for(int i=0;i<res.size(); i++){
        if(i == 0){
            cout << n<<" = ";
            cout << res[i] <<"^"<<p;
        }else
            cout <<" + " << res[i] <<"^"<<p;
    }
    if(indexSum == -9999){
        cout << "Impossible"<<endl;
    }
    return 0;
}
```

