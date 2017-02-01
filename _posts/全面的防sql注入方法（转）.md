title: 全面的防sql注入方法（转）
categories: PHP##分类
tags: [PHP,SQL]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,SQL,防注入##文章关键词，多关键词格式为 keyword1,keywords2,...
description: PHP sql防注入
date: 2015/11/20 21:24:25 
---
``` php
function inject_check($sql_str) {
	return eregi ( 'select|insert|and|or|update|delete|\'|\/\*|\*|\.\.\/|\.\/|union|into|load_file|outfile', $sql_str );
}
function verify_id($id = null) {
	if (! $id) {
		exit ( '没有提交参数！' );
	} elseif (inject_check ( $id )) {
		exit ( '提交的参数非法！' );
	} elseif (! is_numeric ( $id )) {
		exit ( '提交的参数非法！' );
	}
	$id = intval ( $id );
	
	return $id;
}
function str_check($str) {
	if (! get_magic_quotes_gpc ()) {
		$str = addslashes ( $str ); // 进行过滤
	}
	$str = str_replace ( "_", "\_", $str );
	$str = str_replace ( "%", "\%", $str );
	
	return $str;
}
function post_check($post) {
	if (! get_magic_quotes_gpc ()) {
		$post = addslashes ( $post );
	}
	$post = str_replace ( "_", "\_", $post );
	$post = str_replace ( "%", "\%", $post );
	$post = nl2br ( $post );
	$post = htmlspecialchars ( $post );
	
	return $post;
}
``` 

转自http://www.phpddt.com/php/228.html