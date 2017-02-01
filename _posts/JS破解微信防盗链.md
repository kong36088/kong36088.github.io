title: JS破解微信防盗链
categories: javascript##分类
tags: [javascript,微信]##标签，多标签格式为 [tag1,tag2,...]
keywords: javascript,微信##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 2015年微信公众号的图片加入了防盗链功能，使得不能直接用<img src="" />这种方式引用图片，但是针对如此问题，研究了一个下午，尝试过php的curl进行访问，但是却是失败告终，最终找到了解决方案：
date: 2015/11/16 14:24:25 
---
2015年微信公众号的图片加入了防盗链功能，使得不能直接用<img src="" />这种方式引用图片，但是针对如此问题，研究了一个下午，尝试过php的curl进行访问，但是却是失败告终，最终找到了解决方案：

``` javascript
function showImg( url ,frameid) { //url是要访问的连接，frameid是一个独立的frameid
//frameid可以自行传入
// var frameid = 'frameimg' + Math.random();
window.img = '<img id="img" src=\''+url+'?'+Math.random()+'\' height="500px" width="500px" /><script>window.onload = function() { parent.document.getElementById(\''+frameid+'\').height = document.getElementById(\'img\').height+\'px\'; }<'+'/script>';
// document.write('<iframe id="'+frameid+'" src="javascript:parent.img;" frameBorder="0" scrolling="no" width="100%"></iframe>');
}
//通过以下方法调用
showImg(result.img,result.id);
$('#content').append('<iframe id="'+result.id+'" src="javascript:parent.img;" frameBorder="0" scrolling="no" width="100%" height="100%"></iframe>');
``` 