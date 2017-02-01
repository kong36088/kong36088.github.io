title: Laravel中提示Class XXSeeder does not exist解决办法
categories: PHP##分类
tags: [PHP,Laravel]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,Laravel##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 在stackoverflow上找到的解决办法
date: 2016/04/21 14:24:25 
---
在coding的时候写了一个seed，用作填充数据库数据
在本地调试的时候没有任何问题，`php artisan db:seed` 完美运行
当把代码上传到服务器进行调试的时候却出现了问题，提示以下错误：
``` bash
[ReflectionException]                 
  Class EquipmentSeeder does not exist 
``` 

在stackoverflow上找到了这个解决方案：

You need to put SongsTableSeeder into file SongsTableSeeder.php in the same directory where you have your DatabaseSeeder.php file.

And you need to run in your console:

composer dump-autoload

to generate new class map and then run:

php artisan db:seed
I've just tested it. It is working without a problem in Laravel 5

根据意思就是，要把新建的seeder文件与DatabaseSeeder.php放在同一目录下
并且执行命令`composer dump-autoload` ，问题解决