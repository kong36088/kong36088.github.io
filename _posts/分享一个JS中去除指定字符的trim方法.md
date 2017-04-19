title: 分享一个JS中去除指定字符的trim方法
categories: javascript##分类
tags: [trim,javascript]##标签，多标签格式为 [tag1,tag2,...]
keywords: trim,javascript##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 一个非常实用的去除首位字符的方法
date: 2016/05/08 14:24:25 
---
# 说明
第一个参数char指定要去除的字符
第二个参数type指定左边或右边

# 代码
``` javascript
String.prototype.trim = function (char, type) {
    if (char) {
        if (type == 'left') {
            return this.replace(new RegExp('^\\'+char+'+', 'g'), '');
        } else if (type == 'right') {
            return this.replace(new RegExp('\\'+char+'+$', 'g'), '');
        }
        return this.replace(new RegExp('^\\'+char+'+|\\'+char+'+$', 'g'), '');
    }
    return this.replace(/^\s+|\s+$/g, '');
};


// 去除字符串首尾的全部空白
var str = ' Ruchee ';
console.log('xxx' + str.trim() + 'xxx');  // xxxRucheexxx


// 去除字符串左侧空白
str = ' Ruchee ';
console.log('xxx' + str.trim(' ', 'left') + 'xxx');  // xxxRuchee xxx

<!--more-->

// 去除字符串右侧空白
str = ' Ruchee ';
console.log('xxx' + str.trim(' ', 'right') + 'xxx');  // xxx Rucheexxx


// 去除字符串两侧指定字符
str = '/Ruchee/';
console.log(str.trim('/'));  // Ruchee


// 去除字符串左侧指定字符
str = '/Ruchee/';
console.log(str.trim('/', 'left'));  // Ruchee/


// 去除字符串右侧指定字符
str = '/Ruchee/';
console.log(str.trim('/', 'right'));  // /Ruchee
```

参考自：https://segmentfault.com/a/1190000002438098