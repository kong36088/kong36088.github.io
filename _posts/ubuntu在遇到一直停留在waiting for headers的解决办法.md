title: ubuntu在遇到一直停留在waiting for headers的解决办法
categories: Linux##分类
tags: [Ubuntu,Linux]##标签，多标签格式为 [tag1,tag2,...]
keywords: Ubuntu##文章关键词，多关键词格式为 keyword1,keywords2,...
description:  ubuntu在遇到一直停留在waiting for headers的解决办法
date: 2015/12/26 14:24:25 
---
新装了一个ubuntu kylin15.10，在更换成阿里的源之后，前面的update和install都非常的顺畅
但是在安装phpmyadmin的时候却一直在waiting for headers
网上说可以rm -rvf /var/cache/apt/archives
但是除了重新加载到79%然后继续waiting之外毫无作用
最后看了下install的过程，发现
``` bash
Get:12 http://mirrors.hust.edu.cn/ubuntu/ wily-security/main php5-mysql amd64 5.6.11+dfsg-1ubuntu3.1 [65.3 kB]
``` 
最后通过取消Software&Updates->other software里面所有源的钩子，再执行
``` bash
sudo apt-get update
``` 
搞定