title: PHP分离curl header和body
categories: PHP##分类
tags: [PHP,curl]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,curl##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 分离curl返回数据的header和body的方法
date: 2015/11/28 15:24:25 
---
``` php
$header = array (
	"Content-Type:application/json;encoding=utf-8" 
);
$ch = curl_init ();
curl_setopt ( $ch, CURLOPT_URL, $uri );
curl_setopt ( $ch, CURLOPT_POST, 1 );
curl_setopt ( $ch, CURLOPT_HEADER, $header );
curl_setopt ( $ch, CURLOPT_RETURNTRANSFER, 1 );
curl_setopt ( $ch, CURLOPT_POSTFIELDS, $data );
$response = curl_exec ( $ch );
$http_status = curl_getinfo ( $ch, CURLINFO_HTTP_CODE );
curl_close ( $ch );
print_r($response);
``` 
返回结果如下

>HTTP/1.1 200 OK Content-Type: application/json;encoding=utf-8 Access-Control-Allow-Origin: * Set-Cookie: key1=value1 Set-Cookie: key2=value2 {"header":{"handler_id":3001,"command_id":3004,"terminal":1001,"version":0,"reserved":0},"data":{"service_types":[{"name":"A号：收款、退费、科研经费入账","id":1,"count":0},{"name":"B号：请款、内部转账、出国业务咨询及报销","id":2,"count":0},{"name":"C号：报销（含劳务收入酬金报销）","id":3,"count":0},{"name":"D号：零余额优先报账","id":4,"count":0}],"code":1,"msgCode":23000,"msg":"获得排队业务清单请求成功"}}

<!--more-->

要获取分离body和header的数据可以用以下方法：
``` php
if (curl_getinfo($ch, CURLINFO_HTTP_CODE) == '200') {//判断是否成功获取数据
    $headerSize = curl_getinfo($ch, CURLINFO_HEADER_SIZE);
    $header = substr($response, 0, $headerSize);
    $body = substr($response, $headerSize);
}
``` 
或者（个人比较偏好第二种）
``` php
if (curl_getinfo($ch, CURLINFO_HTTP_CODE) == '200') {//判断是否成功获取数据
    list($header, $body) = explode("\r\n\r\n", response, 2);
}
``` 


处理结果如下（body部分）：

{
    "header": {
        "handler_id": 3001,
        "command_id": 3004,
        "terminal": 1001,
        "version": 0,
        "reserved": 0
    },
    "data": {
        "service_types": [
            {
                "name": "A号：收款、退费、科研经费入账",
                "id": 1,
                "count": 0
            },
            {
                "name": "B号：请款、内部转账、出国业务咨询及报销",
                "id": 2,
                "count": 0
            },
            {
                "name": "C号：报销（含劳务收入酬金报销）",
                "id": 3,
                "count": 0
            },
            {
                "name": "D号：零余额优先报账",
                "id": 4,
                "count": 0
            }
        ],
        "code": 1,
        "msgCode": 23000,
        "msg": "获得排队业务清单请求成功"
    }
}