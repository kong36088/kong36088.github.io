title: dirname(__FILE__) 的使用总结
categories: PHP##分类
tags: [PHP]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP##文章关键词，多关键词格式为 keyword1,keywords2,...
description: dirname(__FILE__)
date: 2015/11/16 14:24:25 
---
dirname(__FILE__)
php中定义了一个很有用的常数，即
__file__

这个内定常数是当前php程序的就是完整路径（路径+文件名）。

即使这个文件被其他文件引用(include或require)，__file__始终是它所在文件的完整路径，而不是引用它的那个文件完整路径。

请看下面例子：
/home/data/demo/test/a.php
``` php
$the_full_name=__FILE__;
$the_dir=dirname(__FILE__);
echo $the_full_name; //返回/home/data/demo/test/a.php
echo $the_dir;            //返回/home/data/demo/test
``` 
>__FILE__     返回当前 路径+文件名
>dirname(__FILE__) 返回当前文件路径的 路径部分
>dirname(dirname(__FILE__));得到的是文件上一层目录名（不含最后一个“/”号）