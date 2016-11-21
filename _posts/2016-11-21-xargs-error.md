---
layout: post
title: 报错“Try `mv --help' for more information./”
categories: [blog ]
tags: [Film, Talk, ]
description: 测试环境的添加
---	   

# #最近在解决一个简单问题的时候，遇见一问题。

今天我想删除一些特定的日志文件，如找出/ceshi1目录下面名字是以".log"结尾的，时间是7天以前的，大于1M的文件。

大家都知道，搞运维的，不能随便用“rm”命令，so，我的思路很清晰，将符合这些个条件的日志“mv”到另一个目录下面。

我的脑海中直接出现了两个命令，一个是“xargs” 一个是“-exec”。

<pre>
 mv `find /tmp/oldboy -type f -name "*.log" -mtime +7 -size +1M|xargs ls` /tmp/
find /tmp/oldboy -type f -name "*.log" -mtime +7 -size +1M -exec mv {} /tmp/ \;
</pre>

是的，就是这么简单。

但是，当我开始使用的时候，我的整个人都不好了。是的，你们猜对了，第一个命令就报错了。

# #错误信息如下：

<pre>
[root@momoda ceshi1]# mv  `find /ceshi1 -type f -name "*.log" -mtime +7 -size +1M|xargs ls` /backup/
ls: invalid option -- '6'
Try `ls --help' for more information.
mv: missing destination file operand after `/backup/'
Try `mv --help' for more information.
</pre>

看到以上报错，我开始思考人生，搞运维这么久，他居然提示我查看“ls”命令和“mv”命令的帮助。

是的，没错，暴脾气的我妥协了。

我查看了ls的帮助，google了ls的用法。查完后，我整个人憔悴了，命令、语法没毛病呀！于是乎，我测试了第二条命令，成功了。

# #倔强的我开始查看这两条命令的区别：

![](http://p1.bpimg.com/567571/af28bc35ce397cd1.png)

通过命令查看符合命令的文件，发现使用管道（xargs）命令的方法无法查看。同事提示“无效的选择-- 6”

我开始查看带有6的文件。并做了测试。

![](http://i1.piimg.com/567571/cabea5afee2e5c09.png)

so,anyway!这两条命令都是正确的，但是生成的文件名有问题。

欢迎大家记住这个报错~~~ 

2016-11-21.北京的初雪~~