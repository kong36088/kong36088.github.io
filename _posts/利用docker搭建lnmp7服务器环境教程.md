title: 利用docker搭建lnmp7服务器环境教程
categories: Docker##分类
tags: [docker,Linux]##标签，多标签格式为 [tag1,tag2,...]
keywords: docker,Linux##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 简便搭建服务器环境
date: 2016/05/30 14:24:25 
---

# ubuntu镜像
从官方下载一个ubuntu镜像
``` bash
docker pull ubuntu:14.04
```

# 安装nginx

## 安装nginx服务
``` bash
docker run --net=host -it ubuntu:14.04 bash

apt-get update 
apt-get install nginx
```
完成安装
<!--more-->

## 配置
``` bash
sudo vim /etc/nginx/sites-available/kejyun.dev
```
完成主机的设定
``` bash
server {
    # 設定 Listen 的 port
    listen 80;

    # 設定服務主機名稱
    server_name kejyun.dev;

    # 設定網站根目錄路徑
    root "/home/kejyun/laravel52/public";

    # 設定讀取檔案優先順序
    index index.html index.htm index.php;

    # 設定網站編碼
    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    access_log off;
    # 設定 Log 路徑
    error_log  /var/log/nginx/kejyun.dev-error.log error;

    sendfile off;

    client_max_body_size 100m;

    # 設定 php 檔案處理方式
    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

        fastcgi_intercept_errors off;
        fastcgi_buffer_size 16k;
        fastcgi_buffers 4 16k;
        fastcgi_connect_timeout 300;
        fastcgi_send_timeout 300;
        fastcgi_read_timeout 300;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
连接配置文件
``` bash
sudo ln -s /etc/nginx/sites-available/kejyun.dev /etc/nginx/sites-enabled/kejyun.dev
```
重启服务
``` bash
service nginx restart
```

# 安装php7

## 添加环境
安装添加ppa所需环境
``` bash
sudo apt-get install software-properties-common
sudo apt-get install python3-software-properties
```

添加源
``` bash
LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php
```
LC_ALL=C.UTF-8是为了指定编码，不添加这一段会报错，这是官方的一个bug


## 关闭selinux
这里要先关闭selinux否则会报错
``` bash
sudo vim /etc/selinux/.conf
```

在文件底部增加这一行
``` bash
SELINUX=disabled
```
重启容器
``` bash
exit
docker restart xxxx 
```
## 安装php7
``` bash
sudo apt-get update
sudo apt-get install php7.0-fpm php7.0-mysql php7.0-mcrypt php7.0-gd php7.0-cli php7.0-curl php7.0-imap
```
完成

# 安装mysql5.7

获取mysql5.7套件
``` bash
wget http://dev.mysql.com/get/mysql-apt-config_0.6.0-1_all.deb
sudo dpkg -i mysql-apt-config_0.6.0-1_all.deb
sudo dpkg-reconfigure mysql-apt-config
```
设定mysql预设安装版本
![install-mysql-5.7-setting-1.png](/uploads/install-mysql-5.7-setting-1.png)
![install-mysql-5.7-setting-2.png](/uploads/install-mysql-5.7-setting-2.png)
![install-mysql-5.7-setting-apply.png](/uploads/install-mysql-5.7-setting-apply.png)

安装mysql
``` bash
sudo apt-get update
sudo apt-get install mysql-server-5.7
```
完成

# 参考资料
[主機環境建置](http://laravel5-book.kejyun.com/hosting/Hosting-README.html)
