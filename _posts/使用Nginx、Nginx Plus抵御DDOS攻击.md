title: 使用Nginx、Nginx Plus抵御DDOS攻击
categories: Linux##分类
tags: [Linux,Nginx]##标签，多标签格式为 [tag1,tag2,...]
keywords: Nginx,DDOS##文章关键词，多关键词格式为 keyword1,keywords2,...
description: Nginx使用经验（转）
date: 2016/03/02 14:24:25 
---
DDOS是一种通过大流量的请求对目标进行轰炸式访问，导致提供服务的服务器资源耗尽进而无法继续提供服务的攻击手段。

一般情况下，攻击者通过大量请求与连接使服务器处于饱和状态，以至于无法接受新的请求或变得很慢。

# 应用层DDOS攻击的特征

应用层（七层/HTTP层）DDOS攻击通常由木马程序发起，其可以通过设计更好的利用目标系统的脆弱点。例如，对于无法处理大量并发请求的系统，仅仅通过建立大量的连接，并周期性的发出少量数据包来保持会话就可以耗尽系统的资源，使其无法接受新的连接请求达到DDOS的目的。其他还有采用发送大量连接请求发送大数据包的请求进行攻击的形式。因为攻击是由木马程序发起，攻击者可以在很短时间内快速建立大量的连接，并发出大量的请求。

以下是一些DDOS的特证，我们可以据此特征来抵抗DDOS（包括但不限于）：
攻击经常来源于一些相对固定的IP或IP段，每个IP都有远大于真实用户的连接数和请求数。
备注：这并不表明这种请求都是代表着DDOS攻击。在很多使用NAT的网络架构中，很多的客户端使用网关的IP地址访问公网资源。但是，即便如此，这样的请求数和连接数也会远少于DDOS攻击。
因为攻击是由木马发出且目的是使服务器超负荷，请求的频率会远远超过正常人的请求。
User-Agent通常是一个非标准的值
Referer有时是一个容易联想到攻击的值

# 使用Nginx、Nginx Plus抵抗DDOS攻击

结合上面提到的DDOS攻击的特征，Nginx、Nginx Plus有很多的特性可以用来有效的防御DDOS攻击，可以从调整入口访问流量和控制反向代理到后端服务器的流量两个方面来达到抵御DDOS攻击的目的。

# 限制请求速度

设置Nginx、Nginx Plus的连接请求在一个真实用户请求的合理范围内。比如，如果你觉得一个正常用户每两秒可以请求一次登录页面，你就可以设置Nginx每两秒钟接收一个客户端IP的请求（大约等同于每分钟30个请求）。

``` bash
limit_req_zone $binary_remote_addr zone=one:10m rate=30r/m;
server {
...
location /login.html {
    limit_req zone=one;
...
}
}
``` 

`limit_req_zone`命令设置了一个叫one的共享内存区来存储请求状态的特定键值，在上面的例子中是客户端IP($binary_remote_addr)。location块中的`limit_req`通过引用one共享内存区来实现限制访问/login.html的目的。

# 限制连接数量

设置Nginx、Nginx Plus的连接数在一个真实用户请求的合理范围内。比如，你可以设置每个客户端IP连接/store不可以超过10个。

``` bash
limit_conn_zone $binary_remote_addr zone=addr:10m;
server {
...
location /store/ {
    limit_conn addr 10;
    ...
}
}
``` 

`limit_conn_zone`命令设置了一个叫addr的共享内存区来存储特定键值的状态，在上面的例子中是客户端IP（ $binary_remote_addr）。location块中`limit_conn`通过引用addr共享内存区来限制到/store/的最大连接数为10。

# 关闭慢连接

有一些DDOS攻击，比如Slowlris，是通过建立大量的连接并周期性的发送一些数据包保持会话来达到攻击目的，这种周期通常会低于正常的请求。这种情况我们可以通过关闭慢连接来抵御攻击。

`client_body_timeout`命令用来定义读取客户端请求的超时时间，`client_header_timeout`命令用来定于读取客户端请求头的超时时间。这两个参数的默认值都是60s，我们可以通过下面的命令将他们设置为5s：

``` bash
server {
client_body_timeout 5s;
client_header_timeout 5s;
...
}
``` 

# 设置IP黑名单

如果确定攻击来源于某些IP地址，我们可以将其加入黑名单，Nginx就不会再接受他们的请求。比如，你已经确定攻击来自于从123.123.123.1到123.123.123.16的一段IP地址，你可以这样设置：

``` bash
location / {
deny 123.123.123.0/28;
...
}
``` 


或者你确定攻击来源于123.123.123.3、123.123.123.5、123.123.123.7几个IP，可以这样设置：

location / {
deny 123.123.123.3;
deny 123.123.123.5;
deny 123.123.123.7;
...
}

# 设置IP白名单

如果你的网站仅允许特定的IP或IP段访问，你可以结合使用allow和deny命令来限制仅允许你指定的IP地址访问你的网站。如下，你可以设置仅允许192.168.1.0段的内网用户访问：

``` bash
location / {
allow 192.168.1.0/24;
deny all;
...
}
``` 

deny命令会拒绝除了allow指定的IP段之外的所有其他IP的访问请求。

# 使用缓存进行流量削峰

通过打开Nginx的缓存功能并设置特定的缓存参数，可以削减来自攻击的流量，同时也可以减轻对后端服务器的请求压力。以下是一些有用的设置：
`proxy_cache_use_stale `的updating参数告诉Nginx什么时候该更新所缓存的对象。只需要到后端的一个更新请求，在缓存有效期间客户端对该对象的请求都无需访问后端服务器。当通过对一个文件的频繁请求来实施攻击时，缓存功能可极大的降低到后端服务器的请求。
`proxy_cache_key `命令定义的键值通常包含一些内嵌的变量（默认的键值$scheme$proxy_host$request_uri包含了三个变量）。如果键值包含`$query_string`变量，当攻击的请求字符串是随机的时候就会给Nginx代理过重的缓存负担，因此我们建议一般情况下不要包含`$query_string`变量。

# 屏蔽特定的请求

可以设置Nginx、Nginx Plus屏蔽一些类型的请求：
针对特定URL的请求
针对不是常见的User-Agent的请求
针对Referer头中包含可以联想到攻击的值的请求
针对其他请求头中包含可以联想到攻击的值的请求

比如，如果你判定攻击是针对一个特定的URL：/foo.php，我们就可以屏蔽到这个页面的请求：

``` bash
location /foo.php {
deny all;
}
``` 

或者你判定攻击请求的User-Agent中包含foo或bar，我们也可以屏蔽这些请求：

``` bash
location / {
if ($http_user_agent ~* foo|bar) {
    return 403;
}
...
}
``` 

http_name变量引用一个请求头，上述例子中是User-Agent头。可以针对其他的http头使用类似的方法来识别攻击。

# 限制到后端服务器的连接数

一个Nginx、Nginx Plus实例可以处理比后端服务器多的多的并发请求。在Nginx Plus中，你可以限制到每一个后端服务器的连接数，比如可以设置Nginx Plus与website upstream中的每个后端服务器建立的连接数不得超过200个：

``` bash
upstream website {
server 192.168.100.1:80 max_conns=200;
server 192.168.100.2:80 max_conns=200;
queue 10 timeout=30s;
}
``` 

`max_conns`参数可以针对每一个后端服务器设置Nginx Plus可以与之建立的最大连接数。`queue`命令设置了当每个后端服务器都达到最大连接数后的队列大小，`timeout`参数指定了请求在队列中的保留时间。

# 处理特定类型的攻击

有一种攻击是发送包含特别大的值的请求头，引起服务器端缓冲区溢出。Nginx、Nginx Plus针对这种攻击类型的防御，可以参考[Using NGINX and NGINX Plus to Protect Against CVE-2015-1635](http://nginx.com/blog/nginx-protect-cve-2015-1635/?_ga=1.14368116.2137319792.1439284699)

# 优化Nginx性能

DDOS攻击通常会带来高的负载压力，可以通过一些调优参数，提高Nginx、Nginx Plus处理性能，硬抗DDOS攻击，详细参考：[Tuning NGINX for Performance](http://nginx.com/blog/tuning-nginx/?_ga=1.48422373.2137319792.1439284699)

# 识别DDOS攻击

到目前为止，我们都是集中在如何是用Nginx、Nginx Plus来减轻DDOS攻击带来的影响。如何才能让Nginx、Nginx Plus帮助我们识别DDOS攻击呢？`Nginx Plus Status module`提供了到后端服务器流量的详细统计，可以用来识别异常的流量。Nginx Plus提供一个当前服务状态的仪表盘页面，同时也可以在自定义系统或其他第三方系统中通过API的方式获取这些统计信息，并根据历史趋势分析识别非正常的流量进而发出告警。

# 总结

Nginx和Nginx Plus可以作为抵御DDOS攻击的一个有力手段，而且Nginx Plus中提供了一些附加的特性来更好的抵御DDOS攻击并且当攻击发生时及时的识别到。


原文地址：
http://mp.weixin.qq.com/s?__biz=MzA3MzYwNjQ3NA==&amp;mid=208998983&amp;idx=1&amp;sn=57c74bef6c19227660236fff74557c50&amp;scene=1&amp;srcid=1016w7ZDYGI9DVkRNtr8fNis&amp;from=groupmessage&amp;isappinstalled=0#wechat_redirect
译者：
陈洋 (运维帮)