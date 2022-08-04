---
title: linux常用命令
excerpt: 所在模块：操作系统
tags: [操作系统]
categories: 后端面试
banner_img: /img/壁纸.jpg
---

1、ls (list) 查看linux文件夹包含的文件

ls -a 列出所以文件，包含隐藏文件

ls -t 以文件修改时间排序

ls -S 以文件大小排序

ls -h 以易读大小显示

ls -l除了文件名之外，还有文件的权限、所有者、文件大小等信息

2、cd (chaneg directory) 进入目录

cd   / 	进入根目录

cd ~ 	进入home目录

cd - 	进入上一次工作路径

cd 	!$ 把上个命令的参数作为cd参数用

3、pwd 	查看当前工作路径目录

pwd	-p	查看软连接的实际路径

4、rm	remove

rm	-rf	删除子目录及子目录中所以档案

5、mkdir	（make directory）创建文件夹

6、rmdir	(remove directory) 只能删除空目录，删除时必须具有对其父目录的写权限。

7、mv	（move）	移动文件

8、cp	（copy）  将原文件复制至目标文件

cp -i	提示

cd -r	复制目录及目录内所有项目

cp	-a	复制的文件与原文件时间一样

9、cat	

cat  filename	一次性显示整个文件

cat	>	filename	从键盘创建一个文件

cat	file1	file2	>	file	将几个文件合并为一个文件

cat	-n	输出所有行号

10、more	功能类似于cat但是可以一页一页方便逐页阅读，按space下一页，b就往回

more	+3	text.txt	显示文件从第三行起的内容

11、less	和more类型

12、head	用来显示档案的开头至标志输出

13、tail	用于显示指定文件末尾内容

14、which	

15、whereis

16、locate

17、find

18、chmod	用于改变linux系统文件或者目录的访问权限，

u 表示该档案的拥有者，g 表示与该档案的拥有者属于同一个群体(group)者，o 表示其他以外的人，a 表示这三者皆是

常用参数：

​	-c 当发送改变时，报告处理信息

​	-R处理指定目录以及子目录下所有文件

权限范围：

​	u：目录或者文件的当前的用户

​	g：目录或者文件的当前的群组

​	a：所有 的用户及群组

实例：

chmod	a+x	t.log	增加文件t.log所有用户可执行权限

chmod 777 +文件 就是给这个文件所以人的读、写、执行权限

```
读权限（read，r）的值是4，写权限（write，w）的值是2，执行权限（execute，x）的值是1
```

19、tar	用于压缩和解压文件，本身只有打包功能，打包是将一大堆文件或者目录变成一个总的文件

常用参数：

​	-c	（creat）建立新的压缩文件

​	-f	(file)指定压缩文件

​	-u 添加改了和现有的文件到压缩包中

​	-x	从压缩包中抽取文件

​	-t	显示压缩文件的内容

​	-z	支持gzip压缩

​	-j	支持bzip2压缩

​	-Z	支持compress解压文件

​	-v	显示操作过程

20、chown

21、df	显示磁盘空间使用情况,磁盘空间以1KB显示

-a	全部文件系统列表

-h	方便阅读方式显示信息

-i	显示inode信息

-l	只显示本地磁盘

22、du命令

23、ln	（link）为文件在另外一个位置建立一个同步的链接，默认为硬链接

[硬链接和软链接](http://sjy.xn--6qq986b3xl/2022/01/21/%E5%90%8E%E5%8F%B0%E9%9D%A2%E8%AF%95%E5%87%86%E5%A4%87/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/linux%E7%A1%AC%E8%BF%9E%E6%8E%A5%E5%92%8C%E8%BD%AF%E8%BF%9E%E6%8E%A5/)

dmesg

isomod

rmmod

make

dhcpcd

ipconfig

ifconfig

xrandr

pacman