title: centos6、centos7通过yum方式安装php7
categories: Linux##分类
tags: [Linux,PHP7]##标签，多标签格式为 [tag1,tag2,...]
keywords: Linux,PHP7,安装##文章关键词，多关键词格式为 keyword1,keywords2,...
description: centos6、centos7通过yum方式安装php7
date: 2016/03/21 14:24:25 
---
# 安装PHP7
如果想要通过yum安装php7的话，根据你的centos的版本进行安装源的类型，从下面的地址进行选择
https://mirror.webtatic.com/
如果和我一样，是centos6选择https://mirror.webtatic.com/yum/el6/latest.rpm
如果是centos7的版本的话，选择https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
至于安装方式，也非常简单，centos6下面安装如下

rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm
centos7下面的安装方式

rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
然后就可以安装php7了，安装方式如下

<!--more-->

``` bash
[root@VM_13_18_centos /]# php -v      
PHP 7.0.4 (cli) (built: Mar  5 2016 00:55:49) ( NTS )
Copyright (c) 1997-2016 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2016 Zend Technologies
    with Zend OPcache v7.0.6-dev, Copyright (c) 1999-2016, by Zend Technologies
    with Xdebug v2.4.0RC3, Copyright (c) 2002-2015, by Derick Rethans
[root@VM_13_18_centos /]# yum search php70
Loaded plugins: fastestmirror, langpacks
Repository epel is listed more than once in the configuration
Loading mirror speeds from cached hostfile
 * webtatic: uk.repo.webtatic.com
=============================================================================================== N/S matched: php70 ===============================================================================================
php70w.x86_64 : PHP scripting language for creating dynamic web sites
php70w-bcmath.x86_64 : A module for PHP applications for using the bcmath library
php70w-cli.x86_64 : Command-line interface for PHP
php70w-common.x86_64 : Common files for PHP
php70w-dba.x86_64 : A database abstraction layer module for PHP applications
php70w-devel.x86_64 : Files needed for building PHP extensions
php70w-embedded.x86_64 : PHP library for embedding in applications
php70w-enchant.x86_64 : Enchant spelling extension for PHP applications
php70w-fpm.x86_64 : PHP FastCGI Process Manager
php70w-gd.x86_64 : A module for PHP applications for using the gd graphics library
php70w-imap.x86_64 : A module for PHP applications that use IMAP
php70w-interbase.x86_64 : A module for PHP applications that use Interbase/Firebird databases
php70w-intl.x86_64 : Internationalization extension for PHP applications
php70w-ldap.x86_64 : A module for PHP applications that use LDAP
php70w-mbstring.x86_64 : A module for PHP applications which need multi-byte string handling
php70w-mcrypt.x86_64 : Standard PHP module provides mcrypt library support
php70w-mysql.x86_64 : A module for PHP applications that use MySQL databases
php70w-mysqlnd.x86_64 : A module for PHP applications that use MySQL databases
php70w-odbc.x86_64 : A module for PHP applications that use ODBC databases
php70w-opcache.x86_64 : An opcode cache Zend extension
php70w-pdo.x86_64 : A database access abstraction module for PHP applications
php70w-pdo_dblib.x86_64 : MSSQL database module for PHP
php70w-pear.noarch : PHP Extension and Application Repository framework
php70w-pecl-apcu.x86_64 : APCu - APC User Cache
php70w-pecl-apcu-devel.x86_64 : APCu developer files (header)
php70w-pecl-imagick.x86_64 : Provides a wrapper to the ImageMagick library
php70w-pecl-imagick-devel.x86_64 : Imagick developer files (header)
php70w-pecl-xdebug.x86_64 : PECL package for debugging PHP scripts
php70w-pgsql.x86_64 : A PostgreSQL database module for PHP
php70w-phpdbg.x86_64 : Interactive PHP debugger
php70w-process.x86_64 : Modules for PHP script using system process interfaces
php70w-pspell.x86_64 : A module for PHP applications for using pspell interfaces
php70w-recode.x86_64 : A module for PHP applications for using the recode library
php70w-snmp.x86_64 : A module for PHP applications that query SNMP-managed devices
php70w-soap.x86_64 : A module for PHP applications that use the SOAP protocol
php70w-tidy.x86_64 : Standard PHP module provides tidy library support
php70w-xml.x86_64 : A module for PHP applications which use XML
php70w-xmlrpc.x86_64 : A module for PHP applications which use the XML-RPC protocol

``` 


根据自己需要进行安装，要说明的是，php70w-mysql.x86_64 和php70w-mysqlnd.x86_64 只能够安装其中一个,本地电脑带安装方式

``` bash
yum install php70w php70w-bcmath php70w-cli php70w-common php70w-dba php70w-devel php70w-embedded php70w-enchant php70w-gd php70w-imap php70w-interbase php70w-intl php70w-ldap php70w-mbstring php70w-mcrypt php70w-mysqlnd php70w-odbc php70w-opcache php70w-pdo php70w-pdo_dblib php70w-pear php70w-pecl-apcu php70w-pecl-apcu-devel php70w-pecl-imagick php70w-pecl-imagick-devel php70w-pecl-xdebug php70w-pgsql php70w-phpdbg php70w-process php70w-pspell php70w-recode php70w-snmp php70w-soap php70w-tidy php70w-xml php70w-xmlrpc
``` 

# 附：centos下编译安装PHP所需环境

``` bash
#c和c++编译器
yum install -y gcc gcc-c++
#PHP扩展依赖
yum install -y libxml2-devel openssl-devel libcurl-devel libjpeg-devel libpng-devel libicu-devel openldap-devel
``` 