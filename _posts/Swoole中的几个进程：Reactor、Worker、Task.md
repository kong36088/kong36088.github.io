title: Swoole中的几个进程：Reactor、Worker、Task
categories: Linux##分类
tags: [Swoole,PHP]##标签，多标签格式为 [tag1,tag2,...]
keywords: Swoole,PHP##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 利用Swoole进行socket开发时遇到的线程进程问题，关于Swoole进程分类的介绍
date: 2016/08/28 14:24:25 
---

# Swoole的进程

Swoole中有几种进程，分别是：**Manager、Reactor线程、Worker、TaskWoker**
根据官方文档的介绍，分别介绍

## Manager进程
swoole中worker/task进程都是由Manager进程Fork并管理的。
子进程结束运行时，manager进程负责回收此子进程，避免成为僵尸进程。并创建新的子进程
服务器关闭时，manager进程将发送信号给所有子进程，通知子进程关闭服务
服务器reload时，manager进程会逐个关闭/重启子进程

## Reactor线程

负责维护客户端机器的TCP连接、处理网络IO、收发数据
完全是异步非阻塞的模式
全部为C代码，除Start/Shudown事件回调外，不执行任何PHP代码
将TCP客户端发来的数据缓冲、拼接、拆分成完整的一个请求数据包
Reactor以多线程的方式运行

<!--more-->

## Worker进程

接受由Reactor线程投递的请求数据包，并执行PHP回调函数处理数据
生成响应数据并发给Reactor线程，由Reactor线程发送给TCP客户端
可以是异步非阻塞模式，也可以是同步阻塞模式
Worker以多进程的方式运行
当一个Worker进程被成功创建后，会调用onWorkerStart回调，随后进入事件循环等待数据。当通过回调函数接收到数据后，开始处理数据。如果处理数据过程中出现严重错误导致进程退出，或者Worker进程处理的总请求数达到指定上限，则Worker进程调用onWorkerStop回调并结束进程。

## Task进程

接受由Worker进程通过swoole_server->task/taskwait方法投递的任务
处理任务，并将结果数据返回给Worker进程
完全是同步阻塞模式
Task以多进程的方式运行

## Task、Worker、Reator的关系

可以理解为reactor就是nginx，worker就是php-fpm。reactor线程异步并行地处理网络请求，然后再转发给worker进程中去处理。reactor和worker间通过IPC方式通信。
swoole的reactor，worker，task_worker之间可以紧密的结合起来，提供更高级的使用方式。

一个更通俗的比喻，假设Server就是一个工厂，那reactor就是销售，帮你接项目订单。而worker就是工人，当销售接到订单后，worker去工作生产出客户要的东西。而task_worker可以理解为行政人员，可以帮助worker干些杂事，让worker专心工作。

> 底层会为Worker进程、Task进程分配一个唯一的ID
> 不同的task/worker进程之间可以通过sendMessage接口进行通信

![Swoole进程模型](/uploads/Swoole进程模型.png)

# 生命周期

## 程序全局期

在swoole_server->start之前就创建好的对象，我们称之为程序全局生命周期。这些变量在程序启动后就会一直存在，直到整个程序结束运行才会销毁。

有一些服务器程序可能会连续运行数月甚至数年才会关闭/重启，那么程序全局期的对象在这段时间持续驻留在内存中的。

由于swoole是多进程的，所以程序全局对象在代码中仅是可读的
程序全局对象所在用的内存是共享的，不会额外占用内存

## 进程全局期

swoole拥有进程生命周期控制的机制，一个worker子进程处理的请求数超过max_request配置后，就会自动销毁。worker进程启动后创建的对象（onWorkerStart中创建的对象），在这个子进程存活周期之内，是常驻内存的。onConnect/onReceive/onClose 中都可以去访问它。

进程全局对象所在用的内存是在当前子进程内存堆的，并非共享内存。对此对象的修改仅在当前worker进程中有效
进程期include/require的文件，在reload后就会重新加载

## 会话期

会话期是在onConnect后创建，或者在第一次onReceive时创建，onClose时销毁。一个客户端连接进入后，创建的对象会常驻内存，直到此客户端离开才会销毁。

在LAMP中，一个客户端浏览器访问多次网站，就可以理解为会话期。但传统PHP程序，并不能感知到。只有单次访问时使用session_start，访问$_SESSION全局变量才能得到会话期的一些信息。

swoole中会话期的对象直接是常驻内存，不需要session_start之类操作。可以直接访问对象，并执行对象的方法。

## 请求期

请求期就是指一个完整的请求发来，也就是onReceive收到请求开始处理，直到返回结果发送response。这个周期所创建的对象，会在请求完成后销毁。

swoole中请求期对象与普通PHP程序中的对象就是一样的。请求到来时创建，请求结束后销毁。

# 参考
[swoole_server中对象的4层生命周期](http://wiki.swoole.com/wiki/page/354.html)