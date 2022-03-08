---
title: 1164 Good in C (20 分)
tags: [c++]
categories: PAT刷题
banner_img: /img/壁纸.jpg
hide: true
---

### <font size=6px>Good in C</font>

#### 题目描述：

When your interviewer asks you to write "Hello World" using C, can you do as the following figure shows?

![HWC.jpg](https://images.ptausercontent.com/ba3b8678-061d-4fc6-a87e-ce08e1434410.jpg)

### Input Specification:

Each input file contains one test case. For each case, the first part gives the 26 capital English letters A-Z, each in a 7×5 matrix of `C`'s and `.`'s. Then a sentence is given in a line, ended by a return. The sentence is formed by several words (no more than 10 continuous capital English letters each), and the words are separated by any characters other than capital English letters.

It is guaranteed that there is at least one word given.

### Output Specification:

For each word, print the matrix form of each of its letters in a line, and the letters must be separated by exactly one column of space. There must be no extra space at the beginning or the end of the word.

Between two adjacent words, there must be a single empty line to separate them. There must be no extra line at the beginning or the end of the output.

### Sample Input:

```in
..C..
.C.C.
C...C
CCCCC
C...C
C...C
C...C
CCCC.
C...C
C...C
CCCC.
C...C
C...C
CCCC.
.CCC.
C...C
C....
C....
C....
C...C
.CCC.
CCCC.
C...C
C...C
C...C
C...C
C...C
CCCC.
CCCCC
C....
C....
CCCC.
C....
C....
CCCCC
CCCCC
C....
C....
CCCC.
C....
C....
C....
CCCC.
C...C
C....
C.CCC
C...C
C...C
CCCC.
C...C
C...C
C...C
CCCCC
C...C
C...C
C...C
CCCCC
..C..
..C..
..C..
..C..
..C..
CCCCC
CCCCC
....C
....C
....C
....C
C...C
.CCC.
C...C
C..C.
C.C..
CC...
C.C..
C..C.
C...C
C....
C....
C....
C....
C....
C....
CCCCC
C...C
C...C
CC.CC
C.C.C
C...C
C...C
C...C
C...C
C...C
CC..C
C.C.C
C..CC
C...C
C...C
.CCC.
C...C
C...C
C...C
C...C
C...C
.CCC.
CCCC.
C...C
C...C
CCCC.
C....
C....
C....
.CCC.
C...C
C...C
C...C
C.C.C
C..CC
.CCC.
CCCC.
C...C
CCCC.
CC...
C.C..
C..C.
C...C
.CCC.
C...C
C....
.CCC.
....C
C...C
.CCC.
CCCCC
..C..
..C..
..C..
..C..
..C..
..C..
C...C
C...C
C...C
C...C
C...C
C...C
.CCC.
C...C
C...C
C...C
C...C
C...C
.C.C.
..C..
C...C
C...C
C...C
C.C.C
CC.CC
C...C
C...C
C...C
C...C
.C.C.
..C..
.C.C.
C...C
C...C
C...C
C...C
.C.C.
..C..
..C..
..C..
..C..
CCCCC
....C
...C.
..C..
.C...
C....
CCCCC
HELLO~WORLD!
```

### Sample Output:

```out
C...C CCCCC C.... C.... .CCC.
C...C C.... C.... C.... C...C
C...C C.... C.... C.... C...C
CCCCC CCCC. C.... C.... C...C
C...C C.... C.... C.... C...C
C...C C.... C.... C.... C...C
C...C CCCCC CCCCC CCCCC .CCC.

C...C .CCC. CCCC. C.... CCCC.
C...C C...C C...C C.... C...C
C...C C...C CCCC. C.... C...C
C.C.C C...C CC... C.... C...C
CC.CC C...C C.C.. C.... C...C
C...C C...C C..C. C.... C...C
C...C .CCC. C...C CCCCC CCCC.
```

#### 思路:

题目思路不难想到，就是各种特殊情况，比如有空格，前面有几个非大写字母的字符，连续出现几个非大写字母字符只换行一次

#### 代码:

```go
#include<bits/stdc++.h>
using namespace std;
string s[27][8];
int main()
{
    for(int i=0; i<26; i++)
    {
        for(int j=0; j<7; j++)
        {
            getline(cin,s[i][j]);
        }
    }
    int a[1000],cnt=0;
    string str;
    getline(cin,str);
    while(!isupper(str[0]))
        str.erase(str.begin());

    int flag=0;
    for(int i=0; i<str.length(); i++)
    {
        if(str[i]>='A'&&str[i]<='Z')
        {
            a[cnt]=str[i]-'A';
            cnt++;
        }
        else
        {

            if(cnt!=0)
            {
                if(flag==0)
            {
                flag=1;
            }
            else
            {
                cout<<endl;
            }
                for(int j=0; j<7; j++)
                {
                    for(int k=0; k<cnt; k++)
                    {
                        if(k==0)
                        {
                            cout<<s[a[k]][j];
                        }
                        else
                        {
                            cout<<" "<<s[a[k]][j];
                        }
                    }
                    cout<<endl;
                }
                cnt=0;
            }
        }

    }
    if(cnt!=0)
    {
        if(flag==0)
        {
            flag=1;
        }
        else
        {
            cout<<endl;
        }
        for(int j=0; j<7; j++)
        {
            for(int k=0; k<cnt; k++)
            {
                if(k==0)
                {
                    cout<<s[a[k]][j];
                }
                else
                {
                    cout<<" "<<s[a[k]][j];
                }
            }
            cout<<endl;
        }
    }
    return 0;
}

```

