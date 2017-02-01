title: ubuntu下apache开启SSL提示443端口被占用
categories: Linux##分类
tags: [Linux,Ubuntu]##标签，多标签格式为 [tag1,tag2,...]
keywords: Linux,Ubuntu##文章关键词，多关键词格式为 keyword1,keywords2,...
description: ubuntu下apache开启SSL提示443端口被占用
date: 2016/01/30 23:24:25 
---
>You guys are going to laugh at this. I had an extra Listen 443 in ports.conf that shouldn't have been there. Removing that solved this.

因为在ports.conf中重复出现443端口的配置，所以做以下修改 
把多余的Listen 443注释掉，所以ports.conf的内容应该是这样的

``` bash
NameVirtualHost *:80
Listen 80

NameVirtualHost *:443
#Listen 443

<IfModule mod_ssl.c>
    Listen 443
</IfModule>

<IfModule mod_gnutls.c>
    Listen 443
</IfModule>
``` 
另外在虚拟服务器中不要加 NameVirtualHost *:443 
