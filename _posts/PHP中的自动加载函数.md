title: PHP中的自动加载函数
categories: PHP##分类
tags: [PHP]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP##文章关键词，多关键词格式为 keyword1,keywords2,...
description: spl_autoload_register与__autoload
date: 2016/03/04 14:24:25 
---
对于大多数框架中，都会使用spl_autoload_register函数，而不使用__autoload函数

在官方文档的解释是这样的：

> If there must be multiple autoload functions, spl_autoload_register() allows for this. It effectively creates a queue of autoload functions, and runs through each of them in the order they are defined. By contrast, __autoload() may only be defined once.

> spl_autoload_register 可以很好地处理需要多个加载器的情况，这种情况下 spl_autoload_register 会按顺序依次调用之前注册过的加载器。作为对比， __autoload 因为是一个函数，所以只能被定义一次。


__autoload只能被定义一次，自然spl_autoload_register更好用

以下是代码：

Main.php

``` php
<?php
class Loader {
	public static function load($class) {
		$file = $class . '.class.php';
		if (is_file ( $file )) {
			include ($file);
		}
	}
}

spl_autoload_register(array('Loader','load'));

/* 替代方法
function __autoload($class){
	$file = $class . '.class.php';
	if (is_file ( $file )) {
		include ($file);
	}
}
*/


$main=new Class1();
$main->index();

$main=new Class2();
$main->index();
``` 

Class1.class.php
``` php
<?php
class Class1{
	public function index(){
		echo "Class Class1 function index here\n";
	}
}
``` 


Class2.class.php
``` php
<?php
class Class2{
	public function index(){
		echo "Class Class2 function index here\n";
	}
}
``` 

运行Main.php输出：
``` bash
Class Class1 function index here
class Class2 function index here
``` 