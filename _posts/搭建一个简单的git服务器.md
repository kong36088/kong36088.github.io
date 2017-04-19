title: 搭建一个简单的git服务器
categories: Linux##分类
tags: [Linux]##标签，多标签格式为 [tag1,tag2,...]
keywords: Linux##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 搭建一个简单的git服务器的总结
date: 2016/02/15 14:24:25 
---
在远程仓库一节中，我们讲了远程仓库实际上和本地仓库没啥不同，纯粹为了7x24小时开机并交换大家的修改。

GitHub就是一个免费托管开源代码的远程仓库。但是对于某些视源代码如生命的商业公司来说，既不想公开源代码，又舍不得给GitHub交保护费，那就只能自己搭建一台Git服务器作为私有仓库使用。

搭建Git服务器需要准备一台运行Linux的机器，强烈推荐用Ubuntu或Debian，这样，通过几条简单的apt命令就可以完成安装。

假设你已经有sudo权限的用户账号，下面，正式开始安装。

>git server配置安装

第一步，安装git：
``` bash
$ sudo apt-get install git
``` 
第二步，创建一个git用户，用来运行git服务：
``` bash
$ sudo adduser git
``` 
第三步，创建证书登录：

<!--more-->

收集所有需要登录的用户的公钥，就是他们自己的id_rsa.pub文件，把所有公钥导入到/home/git/.ssh/authorized_keys文件里，一行一个。

第四步，初始化Git仓库：

先选定一个目录作为Git仓库，假定是/srv/sample.git，在/srv目录下输入命令：
``` bash
$ sudo git init --bare sample.git
``` 
Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以.git结尾。然后，把owner改为git：
``` bash
$ sudo chown -R git:git sample.git
``` 
第五步，禁用shell登录：

出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑/etc/passwd文件完成。找到类似下面的一行：
``` bash
git:x:1001:1001:,,,:/home/git:/bin/bash
``` 
改为：
``` bash
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
``` 
这样，git用户可以正常通过ssh使用git，但无法登录shell，因为我们为git用户指定的git-shell每次一登录就自动退出。

第六步，克隆远程仓库：

现在，可以通过git clone命令克隆远程仓库了，在各自的电脑上运行：
``` bash
$ git clone git@server:/srv/sample.git
Cloning into 'sample'...
warning: You appear to have cloned an empty repository.
``` 
剩下的就是普通的git操作了，如果对git操作有陌生的可以参考[这里](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)。

如果团队很小，把每个人的公钥收集起来放到服务器的/home/git/.ssh/authorized_keys文件里就是可行的。如果团队有几百号人，就没法这么玩了，这时，可以用Gitosis来管理公钥。

>Gitosis配置相关：

一、架设步骤
1. 下载并安装python setuptools
``` bash
sudo apt-get install python-setuptools
``` 
2. 下载并安装gitosis
``` bash
cd ~/src
git clone https://github.com/res0nat0r/gitosis.git
cd gitosis
python setup.py install
``` 
3. 添加用户git
``` bash
sudo adduser \
    --system \
    --shell /bin/sh \
    --gecos 'git version control' \
    --group \
    --disabled-password \
    --home /home/git \
    git
``` 
4. 生成本机密钥
切换到个人机，如果已有~/.ssh/id_rsa.pub略过此步
``` bash
ssh-keygen -t rsa
``` 
5. 上传密钥到服务器临时目录
``` bash
scp ~/.ssh/id_rsa.pub 用户名@主机:/tmp
``` 
6. 初使化gitosis
切回到服务器
``` bash
sudo -H -u git gitosis-init < /tmp/id_rsa.pub
``` 
7. 修改post-update权限
``` bash
sudo chmod 755 /home/git/repositories/gitosis-admin.git/hooks/post-update
``` 
8. clone gitosis管理平台
``` bash
git clone git@主机名:gitosis-admin.git
cd gitosis-admin
``` 
9. 安装完成
通过修改gitosis-admin管理gitosis用户权限
添加公密到keydir，添加用户
修改完后commit，push到中服务器即可完成仓库权限的相关操作。


二、实例
1. 用户john添加并发送id_rsa.pub给miao
目标：添加用户 john 和仓库 foo 到gitosis，并和管理员miao合作管理
``` bash
john:~$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/john/.ssh/id_rsa): 
Created directory '/home/john/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/john/.ssh/id_rsa.
Your public key has been saved in /home/john/.ssh/id_rsa.pub.
john:~$ cp /home/john/.ssh/id_rsa.pub /tmp
``` 
2. gitosis管理员miao分配john权限
``` bash
miao:~$ cd ~/projects
git clone git@192.168.1.115:gitosis-admin
cd gitosis-admin
cat gitosis.conf
[gitosis]
[group gitosis-admin]
writable = gitosis-admin
members = miao@u32-192-168-1-110
ls keydir/
miao@u32-192-168-1-110.pub
cp /tmp/id_rsa.pub keydir/john.pub
vi gitosis.conf
[gitosis]
[group gitosis-admin]
writable = gitosis-admin
members = miao@u32-192-168-1-110
[group foo]
writable = foo
members = miao@u32-192-168-1-110 john
git add .
git commit -am "add member john and project foo"
git push
``` 
3. 用户 miao 添加项目foo
``` bash
miao:~$ cd ~/projects
mkdir foo
cd foo
git init
touch hello.txt
git add hello.txt
git commit -am 'first commit'
git remote add origin git@192.168.1.115:foo.git
git push origin master
``` 
4. 用户 john clone Foo并修改hello.txt
``` bash
john:~$ git clone git@192.168.1.115:foo.git
cd foo
ls
date > hello.txt
git commit -am 'add time to hello.txt' && git push
``` 
5. 用户 miao pull Foo
``` bash
miao:~/projects/foo$ vi .git/config
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[remote "origin"]
	url = git@192.168.1.115:foo.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
git pull
``` 


三、常见问题
首先确定 /home/git/repositories/gitosis-admin.git/hooks/post-update 为可执行即属性为 0755
1. git操作需要输入密码
原因
公密未找到
解决
上传id_pub.rsa到keydir并改为'gitosis帐号.pub'形式，如miao.pub。扩展名.pub不可省略
2. ERROR:gitosis.serve.main:Repository read access denied
原因
gitosis.conf中的members与keydir中的用户名不一致，如gitosis中的members = foo@bar，但keydir中的公密名却叫foo.pub
解决
使keydir的名称与gitosis中members所指的名称一致。
改为members = foo 或 公密名称改为foo@bar.pub

参考文章地址：
>http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000
>http://blog.csdn.net/king_sundi/article/details/7065525