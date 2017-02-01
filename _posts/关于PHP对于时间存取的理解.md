title: 关于PHP对于时间存取的理解
categories: PHP##分类
tags: [ThinkPHP,验证码]##标签，多标签格式为 [tag1,tag2,...]
keywords: ThinkPHP,验证码##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 在使用PHP存取时间时，数据库表尽量使用int(10)这种类型，而不要使用datetime。原因一：采用int数据量小，便于提高数据库效率原因二：在进行提取时间操作的时候时间可以进行任意转换，可以通过php自带的函数
date: 2015/11/16 19:24:25 
---
在使用PHP存取时间时，数据库表尽量使用int(10)这种类型，而不要使用datetime。

原因一：采用int数据量小，便于提高数据库效率

原因二：在进行提取时间操作的时候时间可以进行任意转换，可以通过php自带的函数

date(format,timestamp)进行转换

在储存数据的时候同样，可以利用函数strtotime转换成UNIX时间戳，然后作为int存入数据库，灵活变换

PHP中要获取一个月前的时间戳可以这样用：
``` php
strtotime("-1 month")
``` 
结果为：
>1429287142

一年前的时间戳：
``` php
strtotime("-1 year")
``` 
结果为：
>1400343128

转换成可读时间：
``` php
date("Y-m-d",strtotime("-1 month"))
``` 
结果为：
>2015-04-18

由此可见，strtotime这个函数十分之强大