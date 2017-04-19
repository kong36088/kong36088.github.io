title: 关于python爬虫的深坑：requests抛出异常Max retries exceeded with url
categories: Python##分类
tags: [Python]##标签，多标签格式为 [tag1,tag2,...]
keywords: Python##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 在写知乎爬虫中遇到的一些坑
date: 2016/10/29 14:24:25 
---

``` bash
requests.packages.urllib3.exceptions.MaxRetryError: HTTPSConnectionPool(host='www.zhihu.com', port=443): Max retries exceeded with url: /people/claire-98-77/about (Caused by NewConnectionError('<requests.packages.urllib3.connection.VerifiedHTTPSConnection object at 0x7f2ee28c6588>: Failed to establish a new connection: [Errno -3] Temporary failure in name resolution',))
``` 

在调试的过程中，不断抛出该错误，在google查了很久，总结如下：
http的连接数超过最大限制，默认的情况下连接是Keep-alive的，所以这就导致了服务器保持了太多连接而不能再新建连接
解决方案：

解决一：
error trace is misleading it should be something like "No connection could be made because the target machine actively refused it".

因为目标地址拒绝连接而产生的异常
``` python
try:
    page1 = requests.get(ap)
except requests.exceptions.ConnectionError:
    r.status_code = "Connection refused"
 ``` 

解决二：
<!--more-->

产生的连接数过多而导致`Max retries exceeded`
在header中不使用持久连接
`'Connection': 'close'`
或
`requests.adapters.DEFAULT_RETRIES = 5`

解决三：
其实这个异常也有另一种可能性：
在使用`requests`库的时候，当连接失败时会自动发生重连，导致重连次数过多而引发异常
通常对于这种情况选择无视或跳过即可



闲话~

在python3中redis取出的值是以b'.......'的形式
可以像`redis_con.rpop("user_queue").decode('utf-8')`这样转换一下