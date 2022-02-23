---
title: 1080 Graduate Admission (30 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>Graduate Admission</font>

#### 题目描述：

It is said that in 2011, there are about 100 graduate schools ready to proceed over 40,000 applications in Zhejiang Province. It would help a lot if you could write a program to automate the admission procedure.

Each applicant will have to provide two grades: the national entrance exam grade *G**E*, and the interview grade *G**I*. The final grade of an applicant is (*G**E*+*G**I*)/2. The admission rules are:

- The applicants are ranked according to their final grades, and will be admitted one by one from the top of the rank list.
- If there is a tied final grade, the applicants will be ranked according to their national entrance exam grade *G**E*. If still tied, their ranks must be the same.
- Each applicant may have *K* choices and the admission will be done according to his/her choices: if according to the rank list, it is one's turn to be admitted; and if the quota of one's most preferred shcool is not exceeded, then one will be admitted to this school, or one's other choices will be considered one by one in order. If one gets rejected by all of preferred schools, then this unfortunate applicant will be rejected.
- If there is a tied rank, and if the corresponding applicants are applying to the same school, then that school must admit all the applicants with the same rank, **even if its quota will be exceeded**.

### Input Specification:

Each input file contains one test case.

Each case starts with a line containing three positive integers: *N* (≤40,000), the total number of applicants; *M* (≤100), the total number of graduate schools; and *K* (≤5), the number of choices an applicant may have.

In the next line, separated by a space, there are *M* positive integers. The *i*-th integer is the quota of the *i*-th graduate school respectively.

Then *N* lines follow, each contains 2+*K* integers separated by a space. The first 2 integers are the applicant's *G**E* and *G**I*, respectively. The next *K* integers represent the preferred schools. For the sake of simplicity, we assume that the schools are numbered from 0 to *M*−1, and the applicants are numbered from 0 to *N*−1.

### Output Specification:

For each test case you should output the admission results for all the graduate schools. The results of each school must occupy a line, which contains the applicants' numbers that school admits. The numbers must be in increasing order and be separated by a space. There must be no extra space at the end of each line. If no applicant is admitted by a school, you must output an empty line correspondingly.

### Sample Input:

```in
11 6 3
2 1 2 2 2 3
100 100 0 1 2
60 60 2 3 5
100 90 0 3 4
90 100 1 2 0
90 90 5 1 3
80 90 1 0 2
80 80 0 1 2
80 80 0 1 2
80 70 1 3 2
70 80 1 2 3
100 100 0 2 4
```

### Sample Output:

```out
0 10
3
5 6 7
2 8

1 4
```

#### 思路:

根据总成绩和第一个成绩开始降序排序，根据名额把序号进行push中数组中。

#### 代码:

```go
#include "iostream"
#include "vector"
#include "algorithm"
using namespace std;
int m,n,k;
struct Stu{
    int no;
    float score;
    int ge;

    int pre_sch[6];
};

bool cmp(struct Stu a, struct Stu b){
    if(a.score == b.score){
        return a.ge > b.ge;
    }
    return a.score > b.score;
}
int main(){

    cin >>m>>n>>k;
    int a,b;
    struct Stu stu[m+1];
    int sch[n];
    vector<vector<int>> sch_grad;
    // 输入学校的容量
    for (int i=0;i<n;i++){
        cin >>sch[i];
        vector<int> c;
        sch_grad.push_back(c);
    }
    for (int i=0;i<m; i++){
        cin >>a>>b;
        stu[i].ge = a;
        stu[i].score = float (a+b)/2;
        for(int j=0; j<k; j++){
            cin >>stu[i].pre_sch[j];
        }
        stu[i].no = i;
    }
    sort(stu, stu+m, cmp);
    for(int i=0; i<m; i++){
        for(int j=0;j<k;j++){
            //找到一个符合的学校
            if(sch[stu[i].pre_sch[j]] >0){
                // 把合适的节点加入
                sch_grad[stu[i].pre_sch[j]].push_back(stu[i].no);
                sch[stu[i].pre_sch[j]]--;
                if(i<m-1 &&stu[i].score == stu[i+1].score && stu[i].ge == stu[i+1].ge){
                    bool flag = true;
                    for(int p=0;p<k;p++){
                        if(stu[i].pre_sch[p] != stu[i+1].pre_sch[p] ){
                            flag = false;
                            break;
                        }
                    }
                    // 和后一个成绩和意愿都一样
                    if (flag){
                        if(sch[stu[i].pre_sch[j]] == 0){
                            sch[stu[i].pre_sch[j]]++;
                        }
                    }
                }
                break;
            }
        }
    }
    for (int i=0; i<n;i++){
        sort(sch_grad[i].begin(),sch_grad[i].end() );
        for(int j=0;j<sch_grad[i].size(); j++){
            if(j == 0){
                cout << sch_grad[i][j];
            }else{
                cout <<" " <<sch_grad[i][j];
            }
        }
        cout<<endl;
    }
}
```

