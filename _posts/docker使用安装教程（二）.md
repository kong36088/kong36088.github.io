title: docker使用安装教程（二）
categories: Docker##分类
tags: [Docker,Linux]##标签，多标签格式为 [tag1,tag2,...]
keywords: Docker,Linux##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 个人安装docker小笔记
date: 2016/05/10 19:24:25 
---
根据上一节配置的容器，继续进行操作
``` bash
apt-get update
apt-get install -y nginx
``` 
安装好之后，退出容器
``` bash
root@ubuntu:/# exit 
exit
root@ubuntu:/home/jwl# 
``` 
保存修改
``` bash
root@ubuntu:/home/jwl# docker commit -m "add nginx" 596e william/ubuntu-nginx:v1
dab208031d1dc8f6a10f91363959a943f86f6233d790bbba10f44fe278b4c195
root@ubuntu:/home/jwl# docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
william/ubuntu-nginx   v1                  dab208031d1d        8 seconds ago       299.9 MB
ubuntu                 14.04               d4751aa1c40a        6 days ago          188 MB
``` 
此时已经加入到镜像了