---
title: 去PAT考试需要记忆的东西
---

#### 1、使用map

##### 需要引入头

using namespace std;

##### 使用时定义

map<char[6],struct Output> Map;

要使用string得导入开头 #include <iostream>

#### 2、使用strupr（）来小写转大写，strlwr来大写转小写

需要导入#include<string.h>

#### 3、使用sort函数

需要头文件：using namespace std;   #include<algorithm>

例子

```
struct Info{
	int id;
	int de ;
	int cai ;
	int total ;
};
//按total降序
bool cmp(Info a,Info b){
	if (a.total!=b.total){
		return a.total>b.total;
	}else if (a.de!=b.de){
		return a.de>b.de;
	} else{
		return a.id<b.id;
	}
} 
sort(first,first+a,cmp);
```

#### 3、会用gcd和gcm，和swap

```
int gcd(int a,int b){
	return b==0?a:gcd(b,a%b);
} 
//最大公约数 
int gcm(int a,int b){
	int c=gcd(a,b);
	return a*b/c; 
}
```

用swap要导入using namespace std； #include<iostream>