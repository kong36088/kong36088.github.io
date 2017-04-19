title: python百度图片爬虫
categories: Python##分类
tags: [Python,爬虫]##标签，多标签格式为 [tag1,tag2,...]
keywords: Python,爬虫##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 百度图片爬虫第一作
date: 2016/02/06 14:24:25 
---


花了一晚时间写了一个百度图片的爬虫，可以搜索任意关键字
python2.7对于中文的支持实在是十分之蛋疼
对于出现异常的情况处理的并不是很完美，等待后续处理
getImagesURL(max_number, word)

max_number是要抓取图片的数量，word是搜索的关键字

<!--more-->

``` python
#!/usr/bin/env python
# -*- coding:utf-8 -*-

import os
import urllib
import json
from urllib import quote
import socket 
import sys
reload(sys)
sys.setdefaultencoding('utf-8')

#设置超时
socket.setdefaulttimeout(10)
counter=1
# 获取图片url内容等 
def getImagesURL(max_number, word='美女'):
    search = quote(word)
    pn = 0
    while pn <= max_number:
        global counter
        url = 'http://image.baidu.com/search/avatarjson?tn=resultjsonavatarnew&ie=utf-8&word=' + search + '&cg=girl&pn=' + str(pn) + '&rn=60&itg=0&z=0&fr=&width=&height=&lm=-1&ic=0&s=0&st=-1&gsm=1e0000001e'
        page = urllib.urlopen(url)
        data = page.read()
        data = json.loads(data)
        saveImage(data, word)
        pn += 60
        
# 保存图片        
def saveImage(json, word):  
    global counter
    if not os.path.exists("./" + word):
        os.mkdir("./" + word)
    #判断名字是否重复
    index=len(os.listdir('./'+word))
    if os.path.exists("./"+word+"/"+str(counter)+".jpeg"):
            counter=index
    check='<html><body><h1>403 Forbidden</h1>'
    for info in json['imgs']:
        urllib.urlretrieve(info['objURL'], './' + word + '/' + str(counter) +'.jpeg')
        #判断防爬虫 <html><body><h1>403 Forbidden</h1>
        fp=open("./"+word+"/"+str(counter)+".jpeg")
        if not cmp(fp.readline(),'<html><body><h1>403 Forbidden</h1>\n'):
            os.remove("./"+word+"/"+str(counter)+".jpeg")
            counter-=1
        fp.close()
        counter += 1
        print "小黄图+1，已经保存"+str(counter-1)+"张小黄图\n"
    return counter

#获取后缀名
def getFix(name):
    return name[name.find('.'):]
#获取前缀
def getPrefix(name):
    return name[:name.find('.')]


#print getFix('123.txt')
#getImagesURL(390,'美女')
getImagesURL(240,'二次元')
#getImagesURL(360,'帅哥')
``` 
# 效果图
![效果图](/uploads/python图片爬虫截图.png)