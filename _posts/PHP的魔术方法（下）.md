title: PHP的魔术方法（下）
categories: PHP##分类
tags: [PHP]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,魔术方法##文章关键词，多关键词格式为 keyword1,keywords2,...
description: PHP的魔术方法总结
date: 2016/02/25 23:24:25 
---

> __sleep和__wakeup

serialize() 函数会检查类中是否存在一个魔术方法 __sleep()。如果存在，该方法会先被调用，然后才执行序列化操作。此功能可以用于清理对象，并返回一个包含对象中所有应被序列化的变量名称的数组。如果该方法未返回任何内容，则 NULL 被序列化，并产生一个 E_NOTICE 级别的错误。
serialize — 产生一个可存储的值的表示。

> __toString

__toString() 方法用于一个类被当成字符串时应怎样回应。例如 echo $obj; 应该显示些什么。此方法必须返回一个字符串，否则将发出一条 E_RECOVERABLE_ERROR 级别的致命错误。

> __invoke

当尝试以调用函数的方式调用一个对象时，__invoke() 方法会被自动调用。

> __set_state

自 PHP 5.1.0 起当调用 var_export() 导出类时，此静态 方法会被调用。

本方法的唯一参数是一个数组，其中包含按 array('property' => value, ...) 格式排列的类属性。

> __debugInfo

当尝试使用var_dump()打印出对象时，如果没有定义该魔术方法，PHP会打印出所有public，protected，private属性

<!--more-->

``` php
<?php
class Train2 {
	public $public_num = 1;
	protected $protected_num = 2;
	private $private_num = 3;
	public function __sleep() {
		return array (
				'public_num' 
		);
	}
	public function __wakeup() {
		echo __CLASS__ . " wakeup\n";
	}
	public function __invoke() {
		echo "invoke here\n";
	}
	public function __toString() {
		return __CLASS__ . " to string \n";
	}
	public function __debugInfo() {
		return [ 
				'public_num' => $this->public_num * 100 
		];
	}
	public static function __set_state() {
		$obj = new Train2 ();
		$obj->public_num = 999;
		return $obj;
	}
}
$train = new Train2 ();

// serialize — 产生一个可存储的值的表示(via php手册)
$serial = serialize ( $train );

echo "serial value: " . $serial . "\n";
unset ( $train );
$train = unserialize ( $serial );

echo $train ();
echo $train;
var_dump ( $train ) . "\n";

eval ( '$val=' . var_export ( $train, true ) . ';' );
echo var_export ( $val ) . "\n";
``` 

输出：

``` bash
root@ubuntu:/var/www/html/train# php Train2.php 
serial value: O:6:"Train2":1:{s:10:"public_num";i:1;}
Train2 wakeup
invoke here
Train2 to string 
object(Train2)#1 (1) {
  ["public_num"]=>
  int(100)
}
Train2::__set_state(array(
   'public_num' => 999,
   'protected_num' => 2,
   'private_num' => 3,
))
``` 

参考自[PHP手册](http://php.net/manual/zh/language.oop5.magic.php#object.debuginfo)