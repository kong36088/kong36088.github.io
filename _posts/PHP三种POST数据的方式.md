title: PHP三种POST数据的方式
categories: PHP##分类
tags: [PHP]##标签，多标签格式为 [tag1,tag2,...]
keywords: POST,PHP##文章关键词，多关键词格式为 keyword1,keywords2,...
description: PHP三种POST数据的方式的对比区别
date: 2015/12/28 16:24:25 
---
# 第一种 $_POST
这种方式只能够接收Content-type为application/x-www-form-urlencoded提交的数据
这种方式提交server会自动将数据转为key=>value的方式

# 第二种 $GLOBALS['HTTP_RAW_POST_DATA']
总是产生 $HTTP_RAW_POST_DATA  变量包含有原始的 POST 数据。
此变量仅在碰到未识别 MIME 类型的数据时产生。
$HTTP_RAW_POST_DATA  对于 enctype="multipart/form-data"  表单数据不可用
如果post过来的数据不是PHP能够识别的，可以用 $GLOBALS['HTTP_RAW_POST_DATA']来接收，
比如 text/xml 或者 soap 等等

# 第三种 file_get_contents("php://input")
允许读取 POST 的原始数据。
和 $HTTP_RAW_POST_DATA 比起来，它给内存带来的压力较小，并且不需要任何特殊的 php.ini 设置。
php://input 不能用于 enctype="multipart/form-data"。
这一种接受原始数据的方式优于第二种方式，推荐使用该方法接受POST数据

>参考自——[here](http://www.jb51.net/article/61690.htm)