title: ubuntu设置系统时间与网络同步（转）
categories: Linux##分类
tags: [Linux,Ubuntu]##标签，多标签格式为 [tag1,tag2,...]
keywords: Linux,Ubuntu##文章关键词，多关键词格式为 keyword1,keywords2,...
description: ubuntu设置系统时间与网络同步
date: 2016/01/06 16:24:25 
---
Linux的时间分为System Clock（系统时间）和Real Time Clock （硬件时间，简称RTC）。

系统时间：指当前Linux Kernel中的时间。

硬件时间：主板上有电池供电的时间。

查看系统时间的命令： #date

设置系统时间的命令： #date –set（月/日/年 时：分：秒）

例：#date –set “10/11/10 10:15”

查看硬件时间的命令： # hwclock

设置硬件时间的命令： # hwclock –set –date = （月/日/年 时：分：秒）

上述提到的是手动设置时间到一个时间点，可能与当前网络的时间有误差。下面介绍一下与时间服务器上的时间同步的方法

1.  安装ntpdate工具
``` bash
# sudo apt-get install ntpdate
``` 
2.  设置系统时间与网络时间同步
``` bash
# ntpdate cn.pool.ntp.org
``` 
3.  将系统时间写入硬件时间
``` bash
# hwclock --systohc
``` 