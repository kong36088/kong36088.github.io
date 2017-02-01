title: PHP中循环foreach小笔记
categories: PHP##分类
tags: [PHP]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,循环##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 在coding中遇到的一个小坑
date: 2016/04/04 14:24:25 
---
foreach会保存数组当前的状态，改变$arr对于$v并没有影响
即，在下面例子foreach当中，$arr的值会被复制到内存当中，供循环使用，改变$arr的值不会影响$v
（注：在$arr中已赋值二维数组）
``` php
//PHP会复制$arr的值到内存中
foreach ($arr as $k1 => $v1) {
    $arr['id']=1;
    $parent_id = $v1['id'];
    echo $parent_id;
}
//输出值：5049484746454443424140393837369143419242913828332318121732227271162621311615351025302015432
``` 

如果对$v使用引用，即$v引用$arr中的对应的每一个值，结果将会不同
``` php
foreach ($arr as $key => &$v1) {
    $arr[$key]['id'] = 1;
    $parent_id = $v1['id'];
    echo $parent_id;
}
//输出：11111111111111111111111111111111111111111111111111
``` 