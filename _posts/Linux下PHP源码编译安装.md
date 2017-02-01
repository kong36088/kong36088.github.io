title: Linux下PHP源码编译安装
categories: Linux##分类
tags: [Linux,PHP]##标签，多标签格式为 [tag1,tag2,...]
keywords: Linux,PHP##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 要用swoole，首先需要有PHP环境。由于swoole的某些特性，最好是能够从源码编译安装PHP，这样在使用过程中可以避免很多不必要的错误。
date: 2015/11/30 14:24:25 
---
要用swoole，首先需要有PHP环境。由于swoole的某些特性，最好是能够从源码编译安装PHP，这样在使用过程中可以避免很多不必要的错误。PHP下载地址：[PHP官网](http://php.net/)在这里挑选你想用的版本即可。下载源码包后，解压至本地任意目录（保证读写权限），留待使用。安装PHP前，需要安装编译环境和PHP的相关依赖。下面是相关命令：Ubuntu环境下：
sudo apt-get install build-essential gcc g++ autoconf libiconv-hook-dev libmcrypt-dev libxml2-dev libmysqlclient-dev libcurl4-openssl-dev libjpeg8-dev libpng12-dev libfreetype6-dev
CentOS环境下：
yum -y install gcc gcc-c++ autoconf libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients openldap-servers gd gd2 gd-devel gd2-devel perl-CPAN pcre-devel
（注：以上命令是我在实际使用中验证过的可以使用的，可能会和其他教程提供的命令不同）当上述命令执行后，即可开始安装PHP。命令如下：
``` bash
cd php-5.5.10/
./configure --prefix=/usr/local/php --with-config-file-path=/etc/php --enable-fpm --enable-pcntl --enable-mysqlnd --enable-opcache --enable-sockets --enable-sysvmsg --enable-sysvsem  --enable-sysvshm --enable-shmop --enable-zip --enable-ftp --enable-soap --enable-xml --enable-mbstring --disable-rpath --disable-debug --disable-fileinfo --with-mysql=mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --with-pcre-regex --with-iconv --with-zlib --with-mcrypt --with-gd --with-openssl --with-mhash --with-xmlrpc --with-curl --with-imap-ssl
sudo make
sudo make install
sudo cp php.ini-development /etc/php/
``` 
至此，PHP已经成功安装，但是此时在终端里是无法直接通过php --version查看php版本的还需要将PHP的可执行目录添加到环境变量中。使用Vim/Sublime打开~/.bashrc，在末尾添加如下内容：
``` bash
export PATH=/usr/local/php/bin:$PATH
export PATH=/usr/local/php/sbin:$PATH
``` 
保存后，终端输入命令：
``` bash
source ~/.bashrc
``` 
此时即可通过php --version查看php版本，看到如下内容：
>PHP 5.5.10 (cli) (built: Apr 26 2014 09:46:14) 
>Copyright (c) 1997-2014 The PHP Group
>Zend Engine v2.5.0, Copyright (c) 1998-2014 Zend Technologies
即说明安装成功。




memcached扩展：
``` bash
wget https://launchpad.net/libmemcached/1.0/1.0.18/+download/libmemcached-1.0.18.tar.gz
tar -zxvf libmemcached-1.0.18.tar.gz 
./configure     make && make install
wget http://pecl.php.net/get/memcached-2.2.0.tgz
tar -zxvf  memcached-2.2.0.tgz
phpize
./configure     make && make install 
vi /etc/php/php.ini
extension = memcached.so
``` 

redis扩展：
``` bash
https://github.com/phpredis/phpredis.git
cd phpredis/
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make && make install
vi /etc/php/php.ini
extension=redis.so
``` 



mongo扩展：
``` bash
wget http://pecl.php.net/get/mongo-1.5.8.tgz
tar -zxvf mongo-1.5.8.tgz 
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make
make install
vi /etc/php/php.ini
extension=mongo.so
``` 