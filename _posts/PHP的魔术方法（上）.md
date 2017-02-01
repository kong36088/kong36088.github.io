title: PHP的魔术方法（上）
categories: PHP##分类
tags: [PHP]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,魔术方法##文章关键词，多关键词格式为 keyword1,keywords2,...
description: PHP的魔术方法总结
date: 2016/02/25 14:24:25 
---
PHP所提供的"重载"（overloading）是指动态地"创建"类属性和方法。我们是通过魔术方法（magic methods）来实现的。

当调用当前环境下未定义或不可见的类属性或方法时，重载方法会被调用。本节后面将使用"不可访问属性（inaccessible properties）"和"不可访问方法（inaccessible methods）"来称呼这些未定义或不可见的类属性或方法。

所有的重载方法都必须被声明为 public。

在给不可访问属性赋值时，__set() 会被调用。

读取不可访问属性的值时，__get() 会被调用。

当对不可访问属性调用 isset() 或 empty() 时，__isset() 会被调用。

当对不可访问属性调用 unset() 时，__unset() 会被调用。

``` php
<?php
class Train {
	public $public_num = 999;
	private $private_num = 1;
	
	public function __construct(){
		echo __CLASS__." construct\n";
	}
	public function __clone() {
		echo __CLASS__ . " is cloned\n";
	}
	public function __get($name) {
		echo "Get {$name} ";
		return $this->$name;
	}
	public function __set($name, $value) {
		echo "Set {$name}\n";
		$this->$name = $value;
	}
	public function __destruct(){
		echo __CLASS__." destruct\n";
	}
	public function __isset($name){
		echo "judging {$name}\n";
		if(empty($this->$name)){
			return false;
		}else{
			return true;
		}
	}
	public function __unset($name){
		echo "unset {$name}\n";
	}
}

$train1 = new Train ();
$train2 = clone $train1;

echo $train1->private_num . "\n";
$train2->private_num = 666;
echo $train2->private_num . "\n";

echo $train1->public_num . "\n";

if(empty($train1->private_num)){
	echo '$private_num isn\'t set'."\n";
}else{
	echo '$private_num is set'."\n";
}

unset($train1->private_num);
``` 

测试结果：
``` bash
root@ubuntu:/var/www/html/train# php Train.php 
Train construct
Train is cloned
Get private_num 1
Set private_num
Get private_num 666
999
judging private_num
Get private_num $private_num is set
unset private_num
Train destruct
Train destruct
``` 