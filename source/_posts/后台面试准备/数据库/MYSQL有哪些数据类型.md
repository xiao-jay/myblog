---
title: MYSQL有哪些数据类型
excerpt: 所在模块：数据库
tags: [数据库]
categories: 后台面试
banner_img: /img/壁纸.jpg
---



### Mysql 有哪些数据类型，使用的时候有没有什么注意点
**整数类型**：BIT、BOOL、TINY INT、SMALL INT、MEDIUM INT、INT、BIG INT
**浮点数类型：**FLOAT、DOUBLE、DECIMAL
**字符串类型：**CHAR、VARCHAR、TINY TEXT、TEXT、MEDIUM TEXT、LONGTEXT、TINY BLOB、MEDIUM BLOB、LONG BLOB
**日期类型：**Date、DateTime、TIMESTAMP、TIME、YEAR
使用的时候建议遵循从小原则。

使用char和vahrchar的时候，注意char会去掉字符串末尾的空格
使用text和blob的时候，注意定期清理碎片空间，使用OPTIMIZE TABLE命令
浮点数会造成精度丢失，尽量使用定点数DECIMAL
