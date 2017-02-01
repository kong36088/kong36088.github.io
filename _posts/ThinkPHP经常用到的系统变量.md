title: ThinkPHP经常用到的系统变量
categories: PHP##分类
tags: [ThinkPHP,PHP]##标签，多标签格式为 [tag1,tag2,...]
keywords: ThinkPHP,PHP##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 个人关于分页的一个封装，感觉非常使用的PHP分页方法
date: 2015/11/16 19:24:25 
---
原文地址：[http://www.jb51.net/article/47642.htm](http://www.jb51.net/article/47642.htm)

***
（1）系统变量：在模板中输出系统变量：包括server、env、session、post、get、request、cookie
{$Think.server.script_name} // 输出$_SERVER变量
{$Think.session.session_id|md5} // 输出$_SESSION变量
{$Think.get.pageNumber} // 输出$_GET变量
{$Think.cookie.name}  // 输出$_COOKIE变量

以上方式还可以写成：
{$_SERVER.script_name} // 输出$_SERVER变量
{$_SESSION.session_id|md5} // 输出$_SESSION变量
{$_GET.pageNumber} // 输出$_GET变量
{$_COOKIE.name}  // 输出$_COOKIE变量

系统常量 ：使用$Think.const 输出
注意：server、cookie、config不区分大小写，但是变量区分大小写。例如：
{$Think.server.script_name}和{$Think.SERVER.script_name}等效
SESSION 、COOKIE还支持二维数组的输出

例如：
{$Think.CONFIG.user.user_name}
{$Think.session.user.user_name}
系统不支持三维以上的数组输出。

（2）语言变量：输出项目的当前语言定义值

{$Think.lang.page_error}
{$Think.const.MODULE_NAME}

或者直接使用
{$Think.MODULE_NAME}

（3）特殊变量 ：由ThinkPHP系统内部定义的常量

{$Think.version}  //版本
{$Think.now} //现在时间
{$Think.template|basename} //模板页面
{$Think.LDELIM} //模板标签起始符号
{$Think.RDELIM} //模板标签结束符号
（4）配置参数 ：输出项目的配置参数值

{$Think.config.db_charset}

输出的值和 C('db_charset') 的结果是一样的。

***
THINK_PATH // ThinkPHP 系统目录
APP_PATH // 当前项目目录
APP_NAME // 当前项目名称
MODULE_NAME //当前模块名称
ACTION_NAME // 当前操作名称
TMPL_PATH // 项目模版目录
LIB_PATH // 项目类库目录
CACHE_PATH // 项目模版缓存目录
CONFIG_PATH //项目配置文件目录
LOG_PATH // 项目日志文件目录
LANG_PATH // 项目语言文件目录
TEMP_PATH //项目临时文件目录
PLUGIN_PATH // 项目插件文件目录
VENDOR_PATH // 第三方类库目录
DATA_PATH // 项目数据文件目录
IS_APACHE // 是否属于 Apache
IS_IIS //是否属于 IIS
IS_WIN //是否属于Windows 环境
IS_LINUX //是否属于 Linux 环境
IS_FREEBSD //是否属于 FreeBsd 环境
NOW_TIME // 当前时间戳
MEMORY_LIMIT_ON // 是否有内存使用限制
OUTPUT_GZIP_ON // 是否开启输出压缩
MAGIC_QUOTES_GPC // MAGIC_QUOTES_GPC
THINK_VERSION //ThinkPHP 版本号
LANG_SET // 浏览器语言
TEMPLATE_NAME //当前模版名称
TEMPLATE_PATH //当前模版路径
__ROOT__ // 网站根目录地址
__APP__ // 当前项目（入口文件）地址
__URL__ // 当前模块地址
__ACTION__ // 当前操作地址
__SELF__ // 当前 URL 地址
TMPL_FILE_NAME //当前操作的默认模版名（含路径）
WEB_PUBLIC_URL //网站公共目录
APP_PUBLIC_URL //项目公共模版目录
***
__ROOT__ // 网站根目录地址
__APP__ // 当前项目（入口文件）地址
__URL__ // 当前模块地址
__ACTION__ // 当前操作地址
__SELF__ // 当前 URL 地址
__PUBLIC__ // 网站公共目录
../Public (不区分大小写) // 项目公共模版目录
注：当我们使用常量时，在模板被加载后在浏览器查看源码，我们观察某些使用了常量的URL，会发现一个现象，看不到服务器的ip地址，URL是从项
目名开始的，那为什么能正确访问对应的控制器呢？实际上这是浏览器给我们开了一个玩笑，当我们将鼠标移动到该URL上，单击右键，复制源码中的
URL，粘贴到别的地方，服务器的ip就会显示出来了，可见服务器ip是被包含进了该URL中使用的常量的。
***
在项目文件夹 （如：Home） 中的Common文件夹下新建common.php
加入如下语句：
define('XXX', XXX); //第一个参数是常量名，第二个参数是常量值