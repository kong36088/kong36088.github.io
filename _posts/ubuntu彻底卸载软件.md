title: ubuntu彻底卸载软件
categories: Linux##分类
tags: [Ubuntu,Linux]##标签，多标签格式为 [tag1,tag2,...]
keywords: Ubuntu,卸载软件##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 在ubuntu中安装软件后通常想卸载都会卸载得不干净，以下献上把软件完全卸载干净的方法
date: 2015/11/22 15:24:25 
---
在ubuntu中安装软件后通常想卸载都会卸载得不干净，以下献上把软件完全卸载干净的方法
首先找到你想卸载的软件：software（这个是你想要卸载的软件的名称）
然后打开teminal执行以下命令
``` bash
sudo apt-get purge software
``` 
purge参数为彻底删除文件,然后执行
``` bash
sudo apt-get autoremove software
sudo apt-get clean software
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P software
``` 
这些命令之后就可以彻底卸载干净软件了