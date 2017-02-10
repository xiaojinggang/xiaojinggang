---
layout: post
title: 一次/var 分区磁盘空间不足导致的报警
categories: [blog ]
tags: [Film, Talk, ]
description: 测试环境的添加
---	   
	   

# #前言

  监控服务器报警，报警内容是磁盘空间不足。

# #处理过程

## 定位故障

通过登陆服务器查看，显示/var 分区的磁盘使用率为81%.达到了触发报警的阈值。

通过查看，定位到clientmqueue文件，该文件占用了分区的大量的磁盘空间

## 分析故障原因

/var/spool/clientmqueue目录文件是收集cron执行后的输出内容。

由于长时间没有清理，导致存储的信息量过大，占用磁盘空间。

## 解决过程

1、清空该目录文件。

<pre>
   cd /var/spool/clientmqueue
   ls |xargs rm -f
</pre>
	
2、将定时任务反馈的信息定义到/dev/null中。
	
	在每个定时任务后面添加 &>/dev/null 

