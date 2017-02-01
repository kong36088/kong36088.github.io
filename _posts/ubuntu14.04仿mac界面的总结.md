title: ubuntu14.04仿mac界面的总结
categories: Linux##分类
tags: [Linux,Unbuntu]##标签，多标签格式为 [tag1,tag2,...]
keywords: Linux,Unbuntu##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 个人关于分页的一个封装，感觉非常使用的PHP分页方法
date: 2015/11/16 20:24:25 
---
最近想着怎么用自己的ubuntu系统装逼成一个mac系统，好提高自己的逼格，于是上网找了下资料，总结如下：

首先先下载一些mac的壁纸，用于装逼：[点击这里](http://drive.noobslab.com/data/Mac-13.10/MBuntu-Wallpapers.zip)

下载好之后可以自定义你的壁纸。

然后安装一个修改主题的工具tweak：
``` bash
sudo apt-get install unity-tweak-tool

sudo add-apt-repository ppa:tualatrix/ppa

sudo apt-get update

sudo apt-get install ubuntu-tweak
``` 
按下win键，搜索tweak打开你刚才安装的软件，效果图如下：

![效果图](http://cl.ly/image/2V2n0u1D2Y02/172851tw17thwawpooawnk.jpeg)

然后敲入下面命令安装主题：
``` bash
sudo add-apt-repository ppa:noobslab/themes

sudo apt-get update

sudo apt-get install mac-ithemes-v3

sudo apt-get install mac-icons-v3
``` 
现在打开刚才安装的工具来选择主题，在GTK主题上选择MBuntu。再本地tab上选择Mbuntu-osx在光标tab上选择Mac-cursors.

再次安装docky，一个轻量级的任务栏软件
``` bash
sudo add-apt-repository ppa:docky-core/ppa

sudo apt-get update

sudo apt-get install docky
``` 
在安装好以后，选择设置：

![选择设置](http://cl.ly/image/3o431L2r2u2r/135747u6lxnr7r8rgxpayi.jpg)

然后

再外观->行为中可以关闭启动器，

![教程](http://cl.ly/image/012g1T2v3I1c/135751dxspqqhu7xu4z7pr.jpg)

之后的桌面基本和mac相似，可以用来装逼

