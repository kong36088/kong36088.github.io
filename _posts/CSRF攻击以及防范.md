title: CSRF攻击以及防范
categories: PHP##分类
tags: [PHP]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,CSRF##文章关键词，多关键词格式为 keyword1,keywords2,...
description: CSRF攻击以及防范
date: 2016/03/29 14:24:25 
---
# CSRF简介

CSRF一种网站的攻击方式，黑客利用网站对用户的信任进行伪造请求进而发出非用户发出的请求
简而言之，伪造用户的身份发出表单请求

下面看一个例子：
一个网站用户Bob可能正在浏览聊天论坛，而同时另一个用户Alice也在此论坛中，并且后者刚刚发布了一个具有Bob银行链接的图片消息。设想一下，Alice编写了一个在Bob的银行站点上进行取款的form提交的链接，并将此链接作为图片src。如果Bob的银行在cookie中保存他的授权信息，并且此cookie没有过期，那么当Bob的浏览器尝试装载图片时将提交这个取款form和他的cookie，这样在没经Bob同意的情况下便授权了这次事务。

![CSRF攻击图例](/uploads/CSRF.jpg)

可见这对于用户或系统都是严重的破坏，造成用户利益损失，账户被盗等。

# 几种CSRF的防范方法

### 一、 在表单中加入一个cookie的Hash值
``` php
$value="someValueHere";
setcookie("cookie",$value,time()+3600);
``` 

``` html
<input type="hidden" name="hash" value="<?php echo md5($_COOKIE('cookie'));" />
``` 

服务端的验证
```php
$hash=md5($_COOKIE('cookie'));
if($hash==$_POST['hash']){
	//验证通过
}else{
	//验证不通过
}
``` 

### 二、 验证码
在表单域中加入验证码可以杜绝csrf攻击的可能
但是这个对于用户体验并不是那么好

### 三、 加入一个csrf token
``` html
<input type="hidden" name="csrf_token" value="<?php echo $_SESSION['STOKEN_NAME'];?>">
``` 

``` php
$pToken = "";
if($_SESSION[STOKEN_NAME]  == $pToken){
	//没有值，赋新值
	$_SESSION[STOKEN_NAME] = gen_token();
}    
else{
	//继续使用旧的值
}

function gen_token() {
	//这里我是贪方便，实际上单使用Rand()得出的随机数作为令牌，也是不安全的。
	//这个可以参考我写的Findbugs笔记中的《Random object created and used only once》
	$token = md5(uniqid(rand(), true));
	return $token;
}
``` 

最后对token进行验证

```php
$token=$_POST['csrf_token'];
if($token==$_SESSION('STOKEN_NAME')){
	//验证通过
}else{
	//验证不通过
}
``` 

# 参考

> http://www.cnblogs.com/hyddd/archive/2009/04/09/1432744.html
> http://baike.baidu.com/link?url=IhevG_bwOLXJBLKcOTo-tn19lfk6P70IwOdAJW9FDLMTzSoJumMZr58OwJr20YhpNYcGm9dX-UH3FHzyI0cQr_