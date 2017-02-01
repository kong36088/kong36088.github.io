title: Centos7下搭建gogs
categories: gogs##分类
tags: [gogs,Linux]##标签，多标签格式为 [tag1,tag2,...]
keywords: gogs,Linux##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 个人搭建笔记
date: 2016/05/24 14:24:25 
---

# 新建用户
Gogs 默认以 git 用户运行。
运行 `sudo adduser git` 新建好 git 用户。
`su git` 以 `git` 用户登录，到 git 用户的主目录中新建好 `.ssh` 文件夹。
完成

# 下载二进制代码

在 [这里](https://gogs.io/docs/installation/install_from_binary) 可以根据系统下载二进制代码
之后解压到任意地方，在这里我选择解压到`/home/git` 下
``` bash
$ ls /home/git/gogs/
$ custom  data  gogs  LICENSE  log  public  README.md  README_ZH.md  scripts  templates
$ #这里给把gogs目录权限赋予git用户
$ sudo chown -R git:git /home/git/gogs/
$ sudo chmod -R 760 /home/git/gogs/
```

# 运行安装

首先安装数据库，这里我们安装mariadb，在centos7中mariadb代替了mysql
``` bash
sudo yum update
yum -y install mariadb
```
安装好数据库后我们进行安装gogs
``` bash
$ mysql -u root -p
> # （输入密码）
> create user 'gogs'@'localhost' identified by '密码';
> grant all privileges on gogs.* to 'gogs'@'localhost';
> flush privileges;
> exit;
```
这里配置好数据库后即可开始安装gogs啦
``` bash
$ cd /home/git/gogs/
$ ./gogs web
```
到gogs解压目录下，`./gogs web`开启gogs
默认端口是3000，也可以通过`./gogos web -port 3301`来改变端口至3301
访问`localhost:3000/install`进行安装
在这里要注意对gogs相应目录赋予git用户权限，根据缺省设置，这里我这么配置
``` bash
$ mkdir /home/git/gogs-repositories/
$ sudo chown -R git:git ./gogs-repositories/
$ sudo chmod -R 760 ./gogs-repositories/
```
安装完成后即可访问啦

# gogs守护进程启动

## gogs中的app.ini配置

首先配置 gogs/custom/conf/app.ini
根据本地环境进行相应配置
以下是我的配置文件
``` bash
APP_NAME = Gogs: Go Git Service
RUN_USER = git
RUN_MODE = prod

[database]
DB_TYPE = mysql
HOST = 127.0.0.1:3306
NAME = gogs
USER = root
PASSWD = root
SSL_MODE = disable
PATH = data/gogs.db

[repository]
ROOT = /home/git/gogs-repositories

[server]
DOMAIN = 115.159.146.149
HTTP_PORT = 3000
ROOT_URL = http://115.159.146.149:3000/
DISABLE_SSH = false
SSH_PORT = 22
OFFLINE_MODE = false

[mailer]
ENABLED = false

[service]
REGISTER_EMAIL_CONFIRM = false
ENABLE_NOTIFY_MAIL = false
DISABLE_REGISTRATION = false
ENABLE_CAPTCHA = true
REQUIRE_SIGNIN_VIEW = false

[picture]
DISABLE_GRAVATAR = false

[session]
PROVIDER = file

[log]
MODE = file

```
* RUN_USER 默认是 git，指定 Gogs 以哪个用户运行
* ROOT 所有仓库的存储根路径
* PROTOCOL 如果你使用 nginx 反代的话请使用 http，如果直接裸跑对外服务的话随意
* DOMAIN 域名。会影响 SSH clone 地址
* ROOT_URL 完整的根路径，会影响访问时页面上链接的指向，以及 HTTP clone 的地址
* HTTP_ADDR 监听地址，使用 nginx 的话建议 127.0.0.1，否则 0.0.0.0 也可以
* HTTP_PORT 监听端口，默认 3000
* INSTALL_LOCK 锁定安装页面
* Mailer 相关的选项

## systemd服务配置

在 GitHub 上的 Gogs 仓库有一个 [systemd服务模版文件](https://github.com/gogits/gogs/blob/master/scripts/systemd/gogs.service) 

更新 `User`、`Group`、`WorkingDirectory`、`ExecStart` 和 `Environment` 为相对应的值。其中 `WorkingDirectory` 为`Gogs`实际安装路径根目录。
[可选] 如果您 Gogs 安装示例使用 MySQL/MariaDB、PostgreSQL、Redis 或 memcached，请去掉相应 After 属性的注释。
完成修改后，将文件保存至 `/etc/systemd/system/gogs.service`，然后通过 `sudo systemctl enable gogs` 命令激活，最后执行 `sudo systemd start gogs`启动。

通过 sudo systemd status gogs -l 或 sudo journalctl -b -u gogs 可以查看 Gogs 的运行状态。

这是我的`/etc/systemd/system/gogs.service`配置文件
``` bash
[Unit]
Description=Gogs (Go Git Service)
After=syslog.target
After=network.target
After=mariadb.service
#After=postgresql.service
#After=memcached.service
#After=redis.service

[Service]
# Modify these two values and uncomment them if you have
# repos with lots of files and get an HTTP error 500 because
# of that
###
#LimitMEMLOCK=infinity
#LimitNOFILE=65535
Type=simple
User=git
Group=git
WorkingDirectory=/home/git/gogs
ExecStart=/home/git/gogs/gogs web
Restart=always
Environment=USER=git HOME=/home/git

[Install]
WantedBy=multi-user.target
```
大功告成

## 另一种比较暴力的守护进程启动方式

切换到gogs所在目录，执行`nohup ./gogs web &`
``` bash
$ cd /home/git/gogs
$ nohup ./gogs web &
```

# 参考

参考文章：https://mynook.info/blog/post/host-your-own-git-server-using-gogs