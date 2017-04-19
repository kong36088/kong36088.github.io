title: PHP爬虫（信软官网）
categories: PHP##分类
tags: [PHP,爬虫]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,爬虫##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 数信软官网新闻+url的爬虫
date: 2015/11/16 14:24:25 
---
信软官网：[点我](http://info.scau.edu.cn)

源码：

<!--more-->

``` php
// 爬虫
class Crawler {
	private $crawler_url;
	function __construct($url) {
		$this->crawler_url = $url;
	}
	function GetUrlContent() {
		$url = $this->crawler_url;
		$handle = fopen ( "$url", "r" );
		if ($handle) {
			$content = stream_get_contents ( $handle, 1024 * 2048 );
			return $content;
		} else {
			return false;
		}
	}
	function get_url_result() {
		$match = '/href=[\'\"]{0,1}\/news-[0-9]*\.asp\" title=[\'\"][^a-zA-Z]*\"/';
		$content = $this->GetUrlContent ();
		if (preg_match_all ( $match, $content, $result )) {
			return $result;
		} else {
			return false;
		}
	}
	function deal_result() {
		$result = $this->get_url_result ();
		foreach ( $result as $list ) {
			$list = str_replace ( 'href=', "http://info.scau.edu.cn", $list );
			$list = str_replace ( '"', '', $list );
			$results [] = str_replace ( 'title=', ' ', $list );
		}
		return $results;
	}
	function write() {
		$result = $this->deal_result ();
		$fp = fopen ( "list.txt", "w" );
		foreach ( $result as $lists ) {
			foreach ( $lists as $list ) {
				fputs ( $fp, $list . "\r\n" );
			}
		}
		fclose ( $fp );
		echo "succeed";
	}
}

$crawler = new Crawler ( "http://info.scau.edu.cn" );
$crawler->write ();
``` 