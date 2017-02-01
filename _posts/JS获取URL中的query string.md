title: JS获取URL中的query string
categories: javascript##分类
tags: [javascript]##标签，多标签格式为 [tag1,tag2,...]
keywords: javascript##文章关键词，多关键词格式为 keyword1,keywords2,...
description: JQ获取query string
date: 2016/03/30 14:24:25 
---
``` javascript
function GetRequest() {
	var url = location.search; //获取url中"?"符后的字串
	var theRequest = new Object();
	if (url.indexOf("?") != -1) {
		var str = url.substr(1);
		strs = str.split("&");
		for(var i = 0; i < strs.length; i ++) {
			theRequest[strs[i].split("=")[0]] = unescape(strs[i].split("=")[1]);
		}
	}
	return theRequest;
}
var Request = new Object();
Request = GetRequest();
// var 参数1,参数2,参数3,参数N;
// 参数1 = Request['参数1'];
// 参数2 = Request['参数2'];
// 参数3 = Request['参数3'];
// 参数N = Request['参数N'];
``` 