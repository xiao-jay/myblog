---
title: 浅谈git的使用
excerpt: 配置环境和最基本的git使用
index_img: /img/git.jpg
tags: [git]
categories: git
---

## <p class="note note-primary">观前提醒：我写这个主要是来巩固我学的git操作</p>



在程序中#这个符号后面代表我的注释

## 配置环境

#### 0、先安装git，这里我引用别人的文章[git安装](https://www.cnblogs.com/xueweisuoyong/p/11914045.html)

####  1、创建一个文件夹，创建一个readme.txt文件，里面写入hello git文字（这个之后会用到）

#### 2、在文件夹中右键，会有一个Git Bosh Here，点这个

![](/img/gitbosh.png)

#### 3、配置环境，输入提交人的名字和邮箱

```
$git config --global user.name "输入你的名字"
$git config --global user.email "输入你的邮箱"
$git config --global -l  #查看仓库配置，用来查看你是否配置成功
```

#### 4.建立仓库

```
$git init     #会给你创建一个.git隐藏文件，用来存放你的操作记录，注意在你的路径里面最好不要有中文
```

## 添加和提交文件

创建好文件夹和txt文件之后，在git里面输入

```
$git status #查看现在的仓库状态
```

会出现这样的语句

```
Untracked files:  #有个未被跟踪的readme.txt文件
  (use "git add <file>..." to include in what will be committed)
        readme .txt

no changes added to commit (use "git add" and/or "git commit -a")
```

然后添加文件

```
git add readme.txt
```

如果提交了没提示那就是提交正确了，输入了命令什么都没提示就是最好的提示。



最后提交文件

```
git commit -m"写你提交了什么东西，方便回溯的时候看回溯到哪个版本"  #可以add多个文件一起commit
```

现在再输入git status会显示

```
$ git status
On branch dev
nothing to commit, working tree clean #没有东西可以提交
```

用 git log命令能知道提交人的id和提交日期

```
$ git log

commit 507671f473bbbfa18ec0c2de9ed80c27cc483b53
Author: jess-weigou <895006375@qq.com>
Date:   Fri Nov 13 00:10:45 2020 +0800
		wrote a readme.file
		
```

用 git show命令能知道最近一个人的提交id，id后面回溯的时候要用到

```
$ git show
commit 20b009be6781d2fe5df568f8bf859bdf7e489408 (HEAD -> dev) #这一串字母就是                                                                    #commit id
Author: jess-weigou <895006375@qq.com>
Date:   Thu Nov 26 09:44:19 2020 +0800

    write a readme.txt

```

我们再在readme.txt文件中“hello git。”的基础上再添加“hello world”文字，并保存。

## 回溯

  我们来讲git最有趣的回溯功能,先用git log功能看提交的记录和提交人的id,这里我提交了多次。

```
$ git log
commit 80ee417c0d4eb1803202b762dbabe747fa38d1f6 (HEAD -> dev)
Author: jess-weigou <895006375@qq.com>
Date:   Mon Nov 30 16:40:22 2020 +0800

    add hello world

commit 3bb0b8bfb6f2f9178d31a4d0cb372b3d87b461ae
Author: jess-weigou <895006375@qq.com>
Date:   Sun Nov 29 09:46:26 2020 +0800

    add hello world

commit 20b009be6781d2fe5df568f8bf859bdf7e489408
Author: jess-weigou <895006375@qq.com>
Date:   Thu Nov 26 09:44:19 2020 +0800

    write a readme.txt

commit 268d66e08b1ebf50d9ddbf20096504592463051d
Author: jess-weigou <895006375@qq.com>
Date:   Thu Nov 26 09:34:36 2020 +0800

    write a readme.txt
```



用git reset --hard “写提交的id”

```
$ git reset --hard 20b009 #只要前几个字母id就可以匹配到你的提交id
HEAD is now at 20b009b write a readme.txt 
```

这里说head指针已经指向20b009b,我们打开readme.txt，会发现写的“hello world”已经不见了。

**如果回退到以前的版本了还想回会到最新的版本，那咋办？**

找到之前git log的界面，就像我之前git log，那一条最新的日志记录

```
$ git log
commit 80ee417c0d4eb1803202b762dbabe747fa38d1f6 (HEAD -> dev)
Author: jess-weigou <895006375@qq.com>
Date:   Mon Nov 30 16:40:22 2020 +0800

    add hello world
```

输入 git reset --hard “80ee417”就可以回退到之前的最新版本

```
$ git reset --hard 80ee417c0
HEAD is now at 80ee417 add hello world
```

你会发现hello world又出现了，所以别轻易清屏，清屏了也没关系，用git reflog查看历史命令

```
$ git reflog
80ee417 (HEAD -> dev) HEAD@{0}: reset: moving to 80ee417c0
20b009b HEAD@{1}: reset: moving to 20b009
80ee417 (HEAD -> dev) HEAD@{2}: commit: add hello world
3bb0b8b HEAD@{3}: commit: add hello world
20b009b HEAD@{4}: commit: write a readme.txt
268d66e HEAD@{5}: commit: write a readme.txt
e845d2b (github/master, master) HEAD@{6}: checkout: moving from master to dev
e845d2b (github/master, master) HEAD@{7}: checkout: moving from dev to master
e845d2b (github/master, master) HEAD@{8}: checkout: moving from master to dev
e845d2b (github/master, master) HEAD@{9}: reset: moving to e845
507671f HEAD@{10}: reset: moving to 50767
e845d2b (github/master, master) HEAD@{11}: commit: add some words
507671f HEAD@{12}: commit (initial): wrote a readme.file
```

