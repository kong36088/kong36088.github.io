title: Sphinx的安装
categories: Sphnix##分类
tags: [Sphnix]##标签，多标签格式为 [tag1,tag2,...]
keywords: Sphnix##文章关键词，多关键词格式为 keyword1,keywords2,...
description: Sphnix的安装
date: 2015/11/16 14:24:25 
---
Sphinx不推荐在windows下使用

在ubuntu和debian关于Sphinx的安装有两种途径，一种是通过deb包安装，另一种则是通过PPA的途径

Deb包:

利用apt-get安装libpq5拓展
``` bash
$ sudo apt-get install mysql-client unixodbc libpq5
``` 
安装deb包
``` bash
$ sudo dpkg -i sphinxsearch_2.3.1-beta-0ubuntu11~trusty_amd64.deb
``` 
通过PPA的方式 （仅限于ubuntu）

用PPA的方式来安装Sphinx会简单许多

添加软件源
``` bash
$ sudo add-apt-repository ppa:builds/sphinxsearch-rel23
$ sudo apt-get update
``` 
安装Sphinx$ sudo apt-get install sphinxsearch
然后我们就可以通过命令来开启Sphinx了
``` bash
$ sudo service sphinxsearch start
``` 