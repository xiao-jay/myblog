---
title: 剑指 Offer 05. 替换空格
excerpt: 所在模块：string
tags: [c,string]
categories: 剑指offer
banner_img: /img/壁纸.jpg
---

### <font size=6px>05. 替换空格</font>

#### 题目描述：

请实现一个函数，把字符串 s 中的每个空格替换成"%20"。

 

示例 1：

输入：s = "We are happy."
输出："We%20are%20happy."

#### 思路:

#### 代码:

```golang
char* replaceSpace(char* s){
    char* s1;
    s1 = (char *)malloc(sizeof(char)*10000);
    int j=0;
    for(int i=0; i<strlen(s); i++){
        if(s[i] == ' '){
            s1[j++] = '%';
            s1[j++] = '2';
            s1[j++] = '0';
        }else{
            s1[j] = s[i];
            j++;
        }
    }
    s1[j] = '\0';
    return s1;
    }
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：4 ms, 在所有 C 提交中击败了16.70% 的用户<br>
   内存消耗：5.6 MB, 在所有 C 提交中击败了31.01% 的用户
</p>