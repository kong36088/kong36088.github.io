title: 在JS中对异步数据请求进行封装
categories: javascript##分类
tags: [javascript]##标签，多标签格式为 [tag1,tag2,...]
keywords: javascript##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 对异步数据处理的封装，更加规范代码并且提高代码复用性
date: 2016/05/23 14:24:25 
---
# 前戏介绍
jQuery.ajax返回的是jqXHR对象，它是浏览器原生XMLHttpRequest对象的一个超集，并实现了Promise接口。使它拥有了Promise的所有属性，方法和行为。
为了让回调函数名字统一，便于`$.ajax`中使用，jqXHR也提供了`.error()`，`.success()`，`.complete()`
但是由于版本的升级相应的`.fail()`，`.done()`，`.always()`代替了前三个方法，使用方式和解释并没有什么区别。

`jqXHR.fail(function(jqXHR, textStatus, errorThrown) {});`
一种可供选择的请求失败时调用的回调选项构造函数，`.fail()`方法取代了的过时的`.error()`方法。
`jqXHR.done(function(data, textStatus, jqXHR) {});`
一种可供选择的请求成功时调用的回调选项构造函数，`.done()`方法取代了过时的`.success()`方法。
`jqXHR.always(function(data|jqXHR, textStatus, jqXHR|errorThrown) {});`
一种可供选择的请求结束时调用的回调选项构造函数，`.always()`方法代替了过时的`.complete()`方法,
当请求成功时，该函数的参数与`.done()`的参数一致；当请求失败时，该函数的参数与`.fail()`的参数一致。

# 过程介绍

封装成两个文件，分别为逻辑层和视图层

<!--more-->

逻辑层

``` javascript
$(function () {
    http = {
        httpGet: function (url, data) {
            return $.ajax({
                type: "GET",
                headers: {'X-CSRF-TOKEN': csrf_token},
                url: url,
                async: true
            });
        },
        httpPost: function (url, data) {
            return $.ajax({
                type: "POST",
                headers: {'X-CSRF-TOKEN': csrf_token},
                url: url,
                data: data,
                async: true
            });
        }, httpPut: function (url, data) {
            return $.ajax({
                type: "PUT",
                headers: {'X-CSRF-TOKEN': csrf_token},
                url: url,
                data: data,
                async: true
            });
        }, httpDelete: function (url, data) {
            return $.ajax({
                type: "DELETE",
                headers: {'X-CSRF-TOKEN': csrf_token},
                url: url,
                data: data,
                async: true
            });
        }
    };
});
```

视图层

``` javascript
$(function () {
    callAjax = function (type, request_url, data, redirect) {
        var xhr;
        $(".loading").css('display', 'block');
        type = type.toLowerCase();
        switch (type) {
            case 'get':
                xhr = http.httpGet(request_url, data);
                break;
            case 'post':
                xhr = http.httpPost(request_url, data);
                break;
            case 'put':
                xhr = http.httpPut(request_url, data);
                break;
            case 'delete':
                xhr = http.httpDelete(request_url, data);
                break;
        }
        xhr.fail(function (jqXHR, textStatus, errorThrown) {
            alert("加载失败，请重试");
        }).done(function (data, textStatus, jqXHR) {
            if (data.status == 1) {
                alert(data.message);
                if (redirect) {
                    window.location.href = redirect;
                }
            } else {
                alert(data.message);
            }
        }).always(function (data, textStatus, errorThrown) {
            $(".loading").css('display', 'none');
        });
    }
});
```

封装代码很好地提高了代码的规范和代码的复用性