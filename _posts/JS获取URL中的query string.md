title: JS获取URL中的query string
categories: javascript##分类
tags: [javascript]##标签，多标签格式为 [tag1,tag2,...]
keywords: javascript##文章关键词，多关键词格式为 keyword1,keywords2,...
description: JQ获取query string
date: 2016/03/30 14:24:25 
---
``` javascript
function getQueryString(key){
    var reg = new     function getQueryString(key){
    var reg = new RegExp("(^|&)"+key+"=([^&]*)(&|$)");
    var result = window.location.search.substr(1).match(reg);
    return result?decodeURIComponent(result[2]):null;
}
//用法：
//getQueryString('arg')
//getQueryString('test')
``` 