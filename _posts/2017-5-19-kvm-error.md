---
layout: post
title: kvm虚拟机启动报错
categories: [blog ]
tags: [Film, Talk, ]
description: 测试环境的添加
---	   
	   

# 前言：

公司的测试及环境上搭建了关于kvm的测试机。由此公司所在区域出现断电，直接导致我的测试环境全部异常关闭。

# 故障：

在vnc上链接，但是出现报错：

![](http://xiaojinggang.github.io/17-5-19/80768672-file_1495175945435_15c90.png)

出现这个问题时，按要求输入密码后，也能进入到操作系统的命令行，只是对任何文件没有写权限。

要恢复操作系统我们可以这样做：

	e2fsck -y /dev/sda3
	
	PS：此处的/dev/sda3路径是上图中的标注下划线的路径地址。



> this is so easy    

