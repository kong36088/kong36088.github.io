title: JS刷新页面的几种方式
categories: javascript##分类
tags: [javascript]##标签，多标签格式为 [tag1,tag2,...]
keywords: javascript,刷新页面##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 学习笔记
date: 2016/03/30 14:24:25 
---
``` javascript
history.go(0)
``` 

``` javascript
location.reload()
``` 
``` javascript 
location=location 
``` 
``` javascript
location.assign(location) 
``` 
``` javascript
document.execCommand('Refresh') 
``` 
``` javascript
window.navigate(location) 
``` 
``` javascript
location.replace(location) 
``` 
``` javascript
document.URL=location.href
``` 