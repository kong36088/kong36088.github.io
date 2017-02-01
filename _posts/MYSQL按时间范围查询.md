title: MYSQL按时间范围查询
categories: PHP##分类
tags: [PHP,MYSQL]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,MYSQL##文章关键词，多关键词格式为 keyword1,keywords2,...
description: MYSQL时间查取的体悟
date: 2015/11/16 20:24:25 
---
MYSQL按时间范围查询方法

前30天内的mysql查询：
``` bash
mysql> select * from TABLENAME where TO_DAYS(NOW())-TO_DAYS(Time_column)<=30;
```
其中30为时间范围，Time_column可以替换成表中的时间字段名，TABLENAME换成表名。

另一种方法：
``` bash
select * from TALBLENAME where DATE_SUB(CURDATE(), INTERVAL 30 DAY) <= date(Time_column)
```
一段时间范围的查询：
``` bash
SELECT * FROM sp_xuanjiang WHERE Time between '2015-03-01 00:00:00' AND '2015-05-0 16:59:59'(类型为datetime)
``` 