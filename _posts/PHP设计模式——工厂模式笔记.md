title: PHP设计模式——工厂模式笔记
categories: PHP##分类
tags: [PHP,设计模式]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,设计模式##文章关键词，多关键词格式为 keyword1,keywords2,...
description: PHP设计模式——工厂模式笔记
date: 2016/03/10 18:24:25 
---
从某些角度来讲，如何选择设计模式取决于希望能够改变什么
如果实例化的子类可能变化，就要用工厂方法模式。
一个类无法预计它要创建的对象数目，所以不希望类与它要创建的类紧密绑定时可以采用工厂模式。

自动加载
loader.php

``` php
<?php
//loader.php
class Loader {
	public static function load($class) {
		$file = $class . '.class.php';
		if (is_file ( $file )) {
			include ($file);
		}
	}
}
spl_autoload_register(array('Loader','load'));

```

工厂抽象类
FactoryInterface.class.php
``` php
<?php
abstract class FactoryInterface {
	protected abstract function factoryMethod();
	public function startFactory() {
		$mfg = $this->factoryMethod ();
		return $mfg;
	}
}
```  

``` php
<?php
require ('./loader.php');
class TextFactory extends FactoryInterface {
	protected function factoryMethod() {
		$product = new TextProduct ();
		$product->run ();
	}
}
``` 

``` php
<?php
require('./loader.php');
class GraphicFactory extends FactoryInterface{
	protected function factoryMethod(){
		$product = new GraphicProduct();
		$product->run();
	}
}
$graphic=new GraphicFactory();
$graphic=$graphic->startFactory();
```
Product类
``` php
<?php
class GraphicProduct{
	public function __construct(){
		echo "Graphic Product init\n";
	}
	public function run(){
		echo "run\n";
	}
}
``` 

``` php
<?php
class TextProduct{
	public function __construct(){
		echo "TextProduct init\n";
	}
	public function run(){
		echo "run\n";
	}
}

``` 

运行：
``` php
$graphic=new GraphicFactory();
$graphic=$graphic->startFactory();

``` 

结果：
``` bash
Graphic Product init
run
``` 
