title: ubuntu14.04关于Some index files failed to download. They have been ignored, or old ones used instead.的错误
categories: Linux##分类
tags: [Linux,Ubuntu]##标签，多标签格式为 [tag1,tag2,...]
keywords: Linux,Ubuntu##文章关键词，多关键词格式为 keyword1,keywords2,...
description: ubuntu14.04关于Some index files failed to download. They have been ignored, or old ones used instead.的错误
date: 2015/11/16 19:24:25 
---
今天在用PHP的时候发现PHP没有安装CURL拓展，于是打算用

apt-get php5-curl去自动安装，却发现了这样的提示：

php5-curl : 依赖: php5-common (= 5.5.9+dfsg-1ubuntu4) 但是 5.5.9+dfsg-1ubuntu4.14 正要被安装

于是百度了很久，终于找到了答案，原来是DNS的问题，直接到系统文件中修改一下DNS：

>1.修改/etc/resolv.conf文件：为nameserver=8.8.8.8

>2.sudo rm /var/lib/apt/lists/* -vf

>3.sudo apt-get update

搞定