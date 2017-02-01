title: PHP开发小技巧——显示错误
categories: PHP##分类
tags: [PHP]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,调试##文章关键词，多关键词格式为 keyword1,keywords2,...
description: PHP开发小技巧——显示错误
date: 2015/11/16 19:24:25 
---
在开发调试阶段，可以在代码的开头添加
``` php
error_reporting(E_ALL);

ini_set('display_errors', '1');
``` 
来显示出php运行时的错误，不用去修改php.ini