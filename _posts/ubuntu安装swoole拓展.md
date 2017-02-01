title: ubuntu安装swoole拓展
categories: Linux##分类
tags: [Linux,Ubuntu,PHP]##标签，多标签格式为 [tag1,tag2,...]
keywords: Linux,Ubuntu,PHP##文章关键词，多关键词格式为 keyword1,keywords2,...
description: ubuntu安装swoole拓展
date: 2015/12/28 14:24:25 
---
1安装swoole：可pecl直接在线安装，或下载 releases版本的swoole编译安装2.11编译安装swoole扩展

从此处下载： http://pecl.php.net/package/swoole
``` bash
# cd swoole

# phpize

# ./configure

# make && make install
``` 
2.pecl安装swoole扩展

pecl不可用的请确认php安装目录的bin目录已加入系统变量

用pecl方式来安装
``` bash
zzs@ubuntu:~$ sudo pecl install swoole

downloading swoole-1.7.8.tgz …

Starting to download swoole-1.7.8.tgz (412,906 bytes)

….done: 412,906 bytes

130 source files, building

running: phpize

sh: 1: phpize: not found

If the command failed with ‘phpize: not found’ then you need to install php5-dev packageYou can do it by running ‘apt-get install php5-dev’ as a root userERROR: `phpize’ failed

zzs@ubuntu:~$ sudo apt-get install php5-dev

正在读取软件包列表… 完成

正在分析软件包的依赖关系树

正在读取状态信息… 完成

将会安装下列额外的软件包：

pkg-php-tools shtool

建议安装的软件包：

dh-make

下列【新】软件包将被安装：

php5-dev pkg-php-tools shtool

升级了 0 个软件包，新安装了 3 个软件包，要卸载 0 个软件包，有 13 个软件包未被升级。

需要下载 0 B/527 kB 的软件包。

解压缩后会消耗掉 4,431 kB 的额外空间。

您希望继续执行吗？ [Y/n] y

…

install ok: channel://pecl.php.net/swoole-1.7.8

configuration option “php_ini” is not set to php.ini location

You should add “extension=swoole.so” to php.ini
``` 
``` bash
zzs@ubuntu:~$ sudo vim /etc/php5/mods-available/swoole.ini
``` 

里面加入内容
``` bash
; configuration for php Swoole module
extension=swoole.so
php -m | grep swoole
``` 
此时还没有swoole模块
``` bash
sudo ln -s /etc/php5/mods-available/swoole.ini /etc/php5/cli/conf.d/20-swoole.ini
下面两行酌情使用
sudo ln -s /etc/php5/mods-available/swoole.ini /etc/php5/apache2/conf.d/20-swoole.ini
sudo ln -s /etc/php5/mods-available/swoole.ini /etc/php5/fpm/conf.d/20-swoole.ini
php -m | grep swoole
swoole
``` 

测试扩展

server.php:
``` bash
on('connect', function ($serv, $fd){
    echo "Client:Connect.\n";
  });
  $serv->on('receive', function ($serv, $fd, $from_id, $data) {
    $serv->send($fd, 'Swoole: '.$data);
  });
  $serv->on('close', function ($serv, $fd) {
    echo "Client: Close.\n";
  });
  $serv->start();
?>
``` 
client.php:
``` php
on("connect", function($cli) {
    $cli->send("hello world\n");
  });
  $client->on("receive", function($cli, $data){
    echo "Receive: $data\n";
  });
  $client->on("error", function($cli){
    echo "connect fail\n";
  });
  $client->on("close", function($cli){
    echo "close\n";
  });
  $client->connect('127.0.0.1', 9501, 0.5);
?>
``` 