---
layout: post
title: centos7的网卡名字换成eth0
categories: [blog ]
tags: [Film, Talk, ]
description: 测试环境的添加
---	   

# #前言：

最近在做KVM，觉得自动生成的网卡名有点太麻烦，决定将网卡换回成熟悉的eth0。

# #首先，将网卡名称给换了

![](http://ww2.sinaimg.cn/large/0060lm7Tgw1fb44tp2x9uj30md0d6whf.jpg)


# #然后编辑/etc/default/grub，并在配置文件中添加“net.ifnames=0 biosdevname=0”的内核参数。

![](http://ww1.sinaimg.cn/large/0060lm7Tgw1fb44walt8gj30nc07pacv.jpg)
 
# #运行命令来重新生成grub配置并更新内核参数

<pre>
grub2-mkconfig -o /boot/grub2/grub.cfg
</pre>

# #重启系统网卡名称就会改变

>自此，结束。