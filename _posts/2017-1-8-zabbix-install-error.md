---
layout: post
title: about always-populate-raw-post-data must be set -1 和 gettext模块的添加
categories: [blog ]
tags: [Film, Talk, ]
description: 测试环境的添加
---	   

# #前言：

公司要做监控系统，由于机器的缺乏，于是在官网的环境基础上搭建了zabbix。在安装好zabbix后，进入配置界面的时候，出现了两个状况。

1、"always_populate_raw_post_data"	on	off	Fail

2、"PHP gettext"                     off     warning

# #解决问题一："always_populate_raw_post_data"	on	off	Fail

## 解决方法：

找到CFrontendSetup.php这个文件，添加$current = -1;  如下图：
![](http://p1.bpimg.com/567571/5f1fd1352258ed7b.png)


# #解决问题二

![](http://p1.bpimg.com/567571/6d8de8bcbe3b0c91.png)

## 解决方法：

对php进行重新编译安装或者在php源码包中对gettext模块做添加，由于我上面的环境正在运行，所以放弃重新安装。下面我们来聊聊怎么添加gettext模块。

1、进入到php的源码包

<pre>
cd /app/tool/php-5.6.16/ext/      ##你会发现有一个gettext模块
cd gettext
</pre>

2、执行命令：注意路径的不同

<pre>
/app/soft/php5.6.16/bin/phpize   ##注意该出是你的php的安装路径
./configure --with-php-config=/app/soft/php5.6.16/bin/php-config 
make && make install
</pre>

3、在php.ini中添加gettext.so

extension = "gettext.so"
![](http://i1.piimg.com/567571/dfd1fd8d89a3a4da.png)

重启php即可！


