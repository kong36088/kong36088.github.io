title: Shell脚本错误提示——binsh^Mbad interpreter
categories: Linux##分类
tags: [Linux]##标签，多标签格式为 [tag1,tag2,...]
keywords: Linux##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 学习笔记
date: 2016/08/19 14:24:25 
---

执行脚本的时候一直发现执行失败，命令没有达到预期效果

`/bin/sh^M:bad interpreter: No such file or directory`

这个错误发生在你在windows下编写文件上传到linux服务器去运行的时候。
错误原因：windows和linux的文件不一样。
解决办法:vi该文件 在命令模式下输入 :set ff=unix 回车
例如 `a.sh`

``` bash
vi a.sh
``` 
进入输入 `:set ff=unix` 回车
输入`:wq` 回车
 
再次执行就不会有这样的问题了。
