title: thinkPHP验证码的几个要点
categories: PHP##分类
tags: [ThinkPHP,验证码]##标签，多标签格式为 [tag1,tag2,...]
keywords: ThinkPHP,验证码##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 个人的感受：关于PHP验证码需要注意的几个地方
date: 2015/11/16 14:24:25 
---
在使用验证码之前，首先要确保服务器端的php装有GD2的拓展库
``` php
class IndexController extends Controller {
	function index() {
		$this->display ();
	}
	function log() {
		$code = I ( 'post.code', '' ); // 获取POST得到的验证码信息
		$Verify = new ThinkVerify ();
		if (! $this->check_verify ( $code )) {
			echo "succeed";
		} else {
			echo "failed";
		}
	}
	function verify() { // 生成验证码，可以在HTML中直接访问该控制器获得
		$Verify = new ThinkVerify ();
		$Verify->entry ();
	}
	protected function check_verify($code, $id = "") { // 一个简单的检测验证码的函数
		$verify = new ThinkVerify ();
		return $verify->check ( $code, $id );
	}
}
``` 
在对应的模板文件：目录下新建文件Index_index.html，内容如下：
``` html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta name="viewport" content="width=device-width, initial-scale=1">
	<title>HCI官方网站</title>
</head>
<body>
	<form action="!:U('Home/Index/log')!" method="post" >
		<h2>简易后台登录系统</h2>
		验证码：<input type="code" name="code" />
		<img src="/index.php/Home/Index/verify" id="code" onclick="this.src=this.src+'?'+Math.random()"/>
		<br />
		<input type="submit" value="登录"/>
	</form>
</body>
</html>
``` 