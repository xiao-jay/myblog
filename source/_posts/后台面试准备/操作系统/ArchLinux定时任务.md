---
title: ArchLinux定时任务
---

#### 安装

```sudo pacman -S cronie```

#### 启动服务

```
#设置开机启动
sudo systemctl enable cronie.service
#立即启动
sudo systemctl start cronie.service
```

#### crontab用法

crontab –e : 修改 crontab 文件，如果文件不存在会自动创建。 
crontab –l : 显示 crontab 文件。 
crontab -r : 删除 crontab 文件。
crontab -ir : 删除 crontab 文件前提醒用户。

```
minute hour day-of-month month-of-year day-of-week commands  
```

除了数字还有几个特殊的符号："*"、"/"和"-"、","

- *代表所有的取值范围内的数字
- "/"代表每的意思,"/5"表示每5个单位
- "-"代表从某个数字到某个数字
- ","分开几个离散的数字

#### 示例
##### 三月份 周一到周五 每1分钟执行一次./runonce.sh

*/1 * * mar mon-fri ./runonce.sh

#### 每分钟执行一次~/restartTomcat.sh

*/1 * * * * sh /home/itkey/restartTomcat.sh

#####  凌晨1点10分执行一次操作

10 1 * * * sh /home/apex/restartTomcat.sh