---
layout: post
title: dd;打开"/dev/cdrom" 失败;找不到介质
categories: [blog ]
tags: [Film, Talk, ]
description: 对于初学者的使用流程图
--- 


# #前言：最近在做kvm虚拟化，在制作过程中因为疏忽遇见个小问题，特拿出来警醒自己。

# #在我挂载镜像到虚拟机的时候，报如下错误了：

<pre>
[root@localhost ~]# dd if=/dev/cdrom of=/opt/CentOS-7.1.iso
dd: 打开"/dev/cdrom" 失败: 找不到介质
</pre>

# #首先我们需要检测虚拟机上时候有镜像存在,其次查看镜像是否开启。

![](http://p1.bqimg.com/567571/f6d5e4e7d6ddab38.png)

##　如此这般，问题就解决了！

![](http://p1.bqimg.com/567571/19598c0fdde3db4b.png)

> 祝好运！