title: Ubuntu14.04 Docker升级以及设置加速器（业界良心）
categories: docker##分类
tags: [docker,Linux]##标签，多标签格式为 [tag1,tag2,...]
keywords: docker,Linux##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 由于Ubuntu14.04源自带的Docker版本很旧，并且由于国内环境特殊原因在DockerHub上Pull镜像十分之慢，故作此文
date: 2017/02/15 14:33:25
---

由于Ubuntu14.04源自带的Docker版本很旧，并且由于国内环境特殊原因在DockerHub上Pull镜像十分之慢，故作此文

下面看看实现的方法，先说升级：

# 升级

## apt-get的安装方式

比较方便的方法就是通过包管理的方式安装了

首先添加docker仓库秘钥到本地：
``` bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
```

再将docker仓库添加到本地的软件源中：
``` bash
sudo sh -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
```

最后执行更新一下软件源
``` bash
sudo apt-get update
```

最后安装docker即可：
``` bash
sudo apt-get install lxc-docker
```

# 设置加速器

由于国内网络原因，连接docker hub速度特别慢，特别是在不稳定的网络环境下
在使用加速器后下载镜像时长由原来的几个小时到现在的几十分钟，大大的提升了下载速度！
这里我们使用的是国内良心厂家`DaoCloud`免费提供的加速器

<!--more-->
## 注册

首先要到`DaoCloud`先注册一个帐号，[这个是官网传送门](https://www.daocloud.io/)

## 设置加速器

``` bash
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://xxxxxx.m.daocloud.io
service docker restart
```
这里个人的加速器连接需要到官网去获取，[这个是官网传送门](https://www.daocloud.io/mirror#accelerator-doc)
设置完毕，搞定~!

### 使用

这里让我们来测试一下加速的效果吧
``` bash
docker pull mysql
```
淋漓尽致！


![加速效果图](/uploads/Docker升级以及加速器/加速效果.png)