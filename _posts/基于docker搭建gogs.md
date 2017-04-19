title: 基于docker搭建gogs
categories: docker##分类
tags: [docker,Linux]##标签，多标签格式为 [tag1,tag2,...]
keywords: docker,Linux##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 利用docker+docker-compose快速搭建gogs服务，花式搭建gogs
date: 2016/07/08 14:24:25 
---

在linux下利用docker搭建gogs
首先需要在服务器上安装docker-compose和docker
在之前的 [docker-compose快速搭建lnmp+redis服务器环境](http://www.jwlchina.cn/2016/06/01/docker-compose%E5%BF%AB%E9%80%9F%E6%90%AD%E5%BB%BAlnmp7+redis%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%8E%AF%E5%A2%83/) 文章中有介绍，这里就不再赘述了

# 搭建mysql的容器

``` bash
mkdir /mysql
cd /mysql
vi docker-compose.yml
```
生成docker-compose.yml并且内容如下
``` 
mysql:
    container_name: mysql
    build: .
    volumes:
      - /mysql/data/:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
    ports:
      - "3306:3306"
    restart: always
```
<!--more-->

保存并退出 `:wq`，在同一目录下创建Dockerfile
``` bash
vi Dockerfile
#编辑Dockerfile文件保存
From mysql
#保存退出 :wq
mkdir /mysql/data
docker-compose up -d
```
mysql容器搭建完成

# 搭建gogs容器

``` bash
mkdir /gogs
mkdir /gogs/data
cd /gogs
vi docker-compose.yml
```
其中docker-compose.yml内容如下，完成编辑后保存退出
``` 
gogs:
    container_name: gogs
    build: .
    ports:
      - "10002:22"
      - "3000:3000"
    external_links:
      - mysql:mysql
    volumes:
      - /gogs/data:/data
    restart: always
```
``` bash
vi Dockerfile
#内容如下
From gogs/gogs
docker-compose up -d
```
完成创建

进入mysql创建gogs库
``` bash
mysql -uroot -proot -h0.0.0.0 
mysql > CREATE DATABASE `gogs` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
mysql > exit
```
# 进行gogs的相关设置

在浏览器访问本机地址的3000端口，进行初始化设置
按照下图参考设置

![gogs搭建1](/uploads/gogs搭建1.png)

![gogs搭建2](/uploads/gogs搭建2.png)

安装完成后注册一个账户作为管理员账户
大功告成！
