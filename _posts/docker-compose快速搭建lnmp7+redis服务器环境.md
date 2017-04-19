title: docker-compose快速搭建lnmp+redis服务器环境
categories: docker##分类
tags: [docker,Linux]##标签，多标签格式为 [tag1,tag2,...]
keywords: docker,Linux##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 利用docker+docker-compose快速搭建服务器环境
date: 2016/06/01 14:24:25 
---
# 安装最新版本docker

这里我系统用的是centos7，具体安装方法也可以到官网上去查看
[官网地址](https://docs.docker.com/mac/)
> Docker requires a 64-bit installation regardless of your CentOS version. Also, your kernel must be 3.10 at minimum, which CentOS 7 runs.
> To check your current kernel version, open a terminal and use uname -r to display your kernel version:

``` bash
$ uname -r
3.10.0-229.el7.x86_64
```
确认系统版本符合后开始安装
``` bash
$ sudo yum update
$ sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/$releasever/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
EOF
$ sudo yum install docker-engine
$ sudo service docker start
```

<!--more-->

# 安装docker-compose

先安装pip
``` bash
sudo yum update
sudo yum -y install epel-release
sudo yum -y install python-pip
```
安装完成后我们接着安装docker-compose
``` bash
 sudo pip install -U docker-compose
```
大功告成

# 搭建lnmp7+redis环境

## 生成目录结构

在根目录下创建一个app目录
然后在app目录下生成nginx-php mysql redis子目录，用于存放各类数据
``` bash
sudo mkdir /app
```
这里是目录结构

~/Dockerfiles
├── mysql
│   └── Dockerfile
├── nginx-php
│   ├── Dockerfile
├── redis
│   └── Dockerfile
└── www
    └── 网站代码

## 利用docker-compose生成环境

`docker-compose.yml`的内容
```
nginx-php:
    build: ./nginx-php
    ports:
      - "80:80"
    links:
      - "mysql"
    volumes:
      - /app/www:/var/www/html
    environment:
      WEB_DOCUMENT_ROOT: /var/www/html

mysql:
    build: ./mysql
    ports:
      - "3306:3306"
    volumes:
      - /app/mysql/data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: root

redis:
    build: ./redis
    ports:
      - "6379:6379"
    volumes:
      - /app/redis/data:/data
```
`nginx-php`的`Dockerfile`
``` bash
From webdevops/php-nginx:debian-8-php7
```
这个是一个第三方的nginx php服务器，自带redis memcached gd mysql等拓展
`mysql`的`Dockerfile`
``` bash
From mysql:latest
```
`redis`的`Dockerfile`
``` bash
From redis:latest
```

最后运行命令
``` bash
cd /app
docker-compose up -d
```
等待自动生成容器后环境搭建成功

# 参考资料
[Docker File Reference](https://docs.docker.com/compose/compose-file/)
[Docker在PHP项目开发环境中的应用](http://avnpc.com/pages/build-php-develop-env-by-docker)
[用 Docker 来运行和调试 PHP 网站](https://tommy.net.cn/2015/02/13/run-and-debug-php-website-with-docker-part-1/)