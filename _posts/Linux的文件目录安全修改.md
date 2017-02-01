title: Linux的文件目录安全修改
categories: Linux##分类
tags: [Linux]##标签，多标签格式为 [tag1,tag2,...]
keywords: Linux##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 准备把代码放在服务器centos6.5上跑的时候，在更改了documentroot以后，发现无论如何服务器都返回一个403 forbidden，十分之无奈和纠结，把所有都allow from all了一遍然而还是不行。最后找到一个答案，原来是linux的文件的安全问题，以下献上方法
date: 2015/11/16 19:24:25 
---
准备把代码放在服务器centos6.5上跑的时候，在更改了documentroot以后，发现无论如何服务器都返回一个403 forbidden，十分之无奈和纠结，把所有都allow from all了一遍然而还是不行。最后找到一个答案，原来是linux的文件的安全问题，以下献上方法
``` bash
ls -Z -d public_html/
＃显示文件／目录的安全语境－Z, --context
Display  security context so it fits on most displays.  Displays only mode, user, group, security              context and file name.-d, --directory
list directory entries instead of contents, and do not dereference symbolic links

chcon -R -t httpd_user_content_t public_html/
＃修改文件／目录的安全语境-R, --recursive
change files and directories recursively-t, --type
set type TYPE in the target security context
``` 