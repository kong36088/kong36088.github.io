title: Centos升级php版本
categories: Linux##分类
tags: [Linux,Centos]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP安装,Centos##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 花式升级php版本
date: 2016/02/20 14:24:25 
---
默认的版本太低了，手动安装有一些麻烦，想采用Yum安装的可以使用下面的方案：

检查当前安装的PHP包
``` bash
yum list installed | grep php
``` 
如果有安装的PHP包，先删除他们
``` bash
 yum remove php.x86_64 php-cli.x86_64 php-common.x86_64 php-gd.x86_64 php-ldap.x86_64 php-mbstring.x86_64 php-mcrypt.x86_64 php-mysql.x86_64 php-pdo.x86_64
``` 

<!--more-->

> Centos 5.X

  rpm -Uvh http://mirror.webtatic.com/yum/el5/latest.rpm

> CentOs 6.x

  rpm -Uvh http://mirror.webtatic.com/yum/el6/latest.rpm

> CentOs 7.X

rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

如果想删除上面安装的包，重新安装
rpm -qa | grep webstatic
rpm -e  上面搜索到的包即可

运行yum install

  yum install php55w.x86_64 php55w-cli.x86_64 php55w-common.x86_64 php55w-gd.x86_64 php55w-ldap.x86_64 php55w-mbstring.x86_64 php55w-mcrypt.x86_64 php55w-mysql.x86_64 php55w-pdo.x86_64

yum install php56w.x86_64 php56w-cli.x86_64 php56w-common.x86_64 php56w-gd.x86_64 php56w-ldap.x86_64 php56w-mbstring.x86_64 php56w-mcrypt.x86_64 php56w-mysql.x86_64 php56w-pdo.x86_64

注：如果想升级到5.6把上面的55w换成56w就可以了。

yum install php70w.x86_64 php70w-cli.x86_64 php70w-common.x86_64 php70w-gd.x86_64 php70w-ldap.x86_64 php70w-mbstring.x86_64 php70w-mcrypt.x86_64 php70w-mysql.x86_64 php70w-pdo.x86_64
4.安装PHP FPM
yum install php55w-fpm 
yum install php56w-fpm 
yum install php70w-fpm
注：如果想升级到5.6把上面的55w换成56w就可以了。

转自[vb2005xu](http://www.blogjava.net/nkjava/archive/2015/01/20/422289.html)