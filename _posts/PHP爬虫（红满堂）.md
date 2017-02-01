title: PHP爬虫（红满堂）
categories: PHP##分类
tags: [PHP,爬虫]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,爬虫##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 红满堂所有帖子+URL的爬虫
date: 2015/11/16 20:24:25 
---
红满堂链接：http://hometown.scau.edu.cn/bbs/forum.php

献上源码（爬虫的关键是正则表达式的编写），按板块分类出文章标题和URL地址：
``` php
// 红满堂爬虫
class Crwaler {
	private $url;
	private $times = 1;
	// 初始化，获得URL,红满堂域名
	function __construct($url) {
		$this->url = $url;
	}
	// 获取内容
	function get_content($url) {
		$handle = fopen ( "$url", "r" );
		if ($handle) {
			$content = stream_get_contents ( $handle );
			return $content;
		} else {
			die ( 'get_content_1 error' );
		}
	}
	// 获取首页url，获取各个模板块的URL
	function get_url_1() {
		$match = '/(href=[\"]{0,1})(forum\.php\?mod=forumdisplay[^\"]*)/';
		$content = $this->get_content ( $this->url );
		if (preg_match_all ( $match, $content, $results )) {
			foreach ( $results [2] as $value ) {
				$result [] = $value;
			}
			// 去重
			$result = array_unique ( $result );
			
			foreach ( $result as $value ) {
				$url_total [] = 'http://hometown.scau.edu.cn/bbs/' . $value;
			}
			return $url_total;
		} else {
			die ( 'get_url_1 error' );
		}
	}
	// 获得各版标题
	function get_title($url) {
		$match = '/class=\"xs2\">[^<]*<a[^>]*>([^<]*)/';
		$content = $this->get_content ( $url );
		if (preg_match ( $match, $content, $title )) {
			// var_dump ( $title [1] );
			return $title [1];
		} else {
			die ( 'get_title error' );
		}
	}
	
	// 获取版内信息，获得单一页数内的URL和标题
	function get_information($content) {
		$match = '/href=\"(forum\.php\?mod=viewthread[^\"]*)\".*(?:class=\"s xst\">)([^<]*).*/';
		// $match='/.*(?:class=\"s xst\")/';
		if (preg_match_all ( $match, $content, $results )) {
			// var_dump ( $results );
			for($i = 0, $num = count ( $results [1] ); $i < $num; $i ++) {
				$results [1] [$i] = str_replace ( ';', '&', $results [1] [$i] );
				$results [1] [$i] = str_replace ( '&amp', '', $results [1] [$i] );
				$result [] = '链接： http://hometown.scau.edu.cn/bbs/' . $results [1] [$i] . ' 标题：' . $results [2] [$i] . PHP_EOL;
			}
			return $result;
		} else {
			die ( 'get_information error' );
		}
	}
	// 获取页码，获取模版内的页数
	function get_page($url) {
		$match = '/<span title=\"共[^0-9]*([0-9]+)/';
		$content = $this->get_content ( $url );
		if (preg_match ( $match, $content, $page )) {
			// var_dump($page);
			$page = $page [1];
			if ($page > 1500) {
				$page = 1;
			}
			return $page;
		} else {
			$page = 1;
			return $page;
		}
	}
	// 将内容写入TXT
	function write($info, $title) {
		$title = $title . '.txt';
		file_put_contents ( $title, $info, FILE_APPEND );
	}
	// 获取版面内的content，并且遍历所有页数，获得标题和URL
	function get() {
		$sign = 1;
		$url_total = $this->get_url_1 ();
		foreach ( $url_total as $url ) {
			$title = $this->get_title ( $url );
			$page_num = $this->get_page ( $url );
			for($flag = 1; $flag <= $page_num; $flag ++) {
				$url_n = $url . '&page=' . $flag;
				$content = $this->get_content ( $url_n );
				$result = $this->get_information ( $content );
				var_dump ( $flag );
				$sign ++;
				$this->write ( $result, $title );
			}
		}
		echo "succeed";
	}
}
$crawler = new Crwaler ( 'http://hometown.scau.edu.cn/bbs/forum.php' );
$crawler->get ();
``` 

效果图：
![效果](http://cl.ly/image/1I1C1B1L243d/QQ%E6%88%AA%E5%9B%BE20150505222642.png)