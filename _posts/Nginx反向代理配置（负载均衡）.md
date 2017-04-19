title: Nginx反向代理配置（负载均衡）
categories: Linux##分类
tags: [Linux,Nginx]##标签，多标签格式为 [tag1,tag2,...]
keywords: 负载均衡,Nginx##文章关键词，多关键词格式为 keyword1,keywords2,...
description: Nginx配置负载均衡相关笔记
date: 2016/03/10 14:24:25 
---
> 负载均衡分配

根据ip轮询：
``` bash
upstream backend {
	server 192.168.102.143;
	server 192.168.102.144;
}
``` 

根据ip的hash值分配（轮询）：
``` bash
upstream backend {
    ip_hash;
	server 192.168.102.143;
	server 192.168.102.144;
}
``` 

根据权值分配：
``` bash
upstream hello{
    server 192.168.102.143 weight=1;
    server 192.168.102.144 weight=1;            
}
``` 
最少连接数：
``` bash
upstream hello{
	least_conn;
    server 192.168.102.143;
    server 192.168.102.144;            
}
``` 

<!--more-->

> 完整文件的配置

``` bash
#负责压缩数据流
worker_processes  1;
events {
    worker_connections  1024;
}

gzip              on;  
gzip_min_length   1000;  
gzip_types        text/plain text/css application/x-javascript;

#设定负载均衡的服务器列表
upstream backend {
	server 192.168.102.143;
	server 192.168.102.144;
}
server {
    #侦听的80端口
    listen       80;
    server_name  localhost;
    #设定查看Nginx状态的地址
    location /nginxstatus{
         stub_status on;
         access_log on;
         auth_basic "nginxstatus";
         auth_basic_user_file htpasswd;
    }
    #匹配以jsp结尾的，tomcat的网页文件是以jsp结尾
    location / {
        index index.jsp;
        proxy_pass   http://backend;    #在这里设置一个代理，和upstream的名字一样
        #以下是一些反向代理的配置可删除
        proxy_redirect             off; 
        #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
        proxy_set_header           Host $host; 
        proxy_set_header           X-Real-IP $remote_addr; 
        proxy_set_header           X-Forwarded-For $proxy_add_x_forwarded_for; 
        client_max_body_size       10m; #允许客户端请求的最大单文件字节数
        client_body_buffer_size    128k; #缓冲区代理缓冲用户端请求的最大字节数
        proxy_connect_timeout      300; #nginx跟后端服务器连接超时时间(代理连接超时)
        proxy_send_timeout         300; #后端服务器数据回传时间(代理发送超时)
        proxy_read_timeout         300; #连接成功后，后端服务器响应时间(代理接收超时)
        proxy_buffer_size          4k; #设置代理服务器（nginx）保存用户头信息的缓冲区大小
        proxy_buffers              4 32k; #proxy_buffers缓冲区，网页平均在32k以下的话，这样设置
        proxy_busy_buffers_size    64k; #高负荷下缓冲大小（proxy_buffers*2）
        proxy_temp_file_write_size 64k; #设定缓存文件夹大小，大于这个值，将从upstream服务器传
    }
}
``` 

配置完成后，重启nginx：

``` bash
service nginx restart
``` 

参考文章：
http://www.cnblogs.com/sixiweb/p/3988805.html
http://www.jb51.net/article/60523.htm
