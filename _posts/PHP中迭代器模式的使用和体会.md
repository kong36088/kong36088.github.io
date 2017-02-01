title: 一个使用的分页方法
categories: PHP##分类
tags: [PHP,迭代器]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,迭代器##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 迭代器模式提供一种访问一个容器对象中的各个元素，而又不暴露其内部细节的方法。在应用中，我们时常会遇到各种变量代码，foreach在很多代码处都可以见到，使用迭代器可以对不同数据结构的集合封装，外部只需调用迭代器提供的接口即可，提高了应用的可扩展性。
date: 2015/11/16 14:24:25 
---
迭代器模式提供一种访问一个容器对象中的各个元素，而又不暴露其内部细节的方法。在应用中，我们时常会遇到各种变量代码，foreach在很多代码处都可以见到，使用迭代器可以对不同数据结构的集合封装，外部只需调用迭代器提供的接口即可，提高了应用的可扩展性。

迭代器的使用可以遍历容器内的数据，是一种十分之实用的方法。
``` php
class travel implements \Iterator { // 继承迭代器接口
	protected $index = 0;
	protected $example = array ();
	public function __construct() {
		$this->index = 0; // 初始化index，迭代器初始化
		$connect = mysqli_connect ( 'localhost', 'root', 'root', 'sphr' ) or die ( 'can not connect' );
		mysqli_query ( $connect, 'set names utf8' ); // 设置utf8编码模式，防止乱码
		$result = mysqli_query ( $connect, 'SELECT * FROM sp_candidate' );
		$this->example = mysqli_fetch_all ( $result, MYSQLI_ASSOC );
	}
	public function rewind() {
		$this->index = 0;
	}
	public function current() {
		return $this->example [$this->index];
	}
	public function key() {
		return $this->index;
	}
	public function next() {
		$this->index ++;
	}
	public function valid() {
		return isset ( $this->example [$this->index] );
	}
}
``` 
迭代器的接口模式为：
``` php
class myIterator implements Iterator {
	private $position = 0;
	private $array = array ();
	public function __construct() {
		$this->position = 0;
	}
	function rewind() {
		$this->position = 0;
	}
	function current() {
		return $this->array [$this->position];
	}
	function key() {
		return $this->position;
	}
	function next() {
		++ $this->position;
	}
	function valid() {
		return isset ( $this->array [$this->position] );
	}
}

$it = new myIterator ();

foreach ( $it as $key => $value ) {
	var_dump ( $key, $value );
	echo "\n";
}
``` 