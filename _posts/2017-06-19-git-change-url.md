---
layout: post
title: git更换服务端地址
categories: [blog ]
tags: [Film, Talk, ]
description: 测试环境的添加
---	   
	   
# 前言：
	
  公司搬家，网络地址也换了，之前的测试服务器的地址都要更换。IP地址更换后，开发人员的git绑定的服务端地址也要随着改变。就有了现在这篇文字

# 正文：

  我以前的ip地址是192.168.199.252。现在，我的IP地址更换为了10.10.0.22。更换后我再git的命令窗口查看了当前绑定的url地址：
	
	
	$ git remote -v
	origin  ssh://git@192.168.199.252:52113/Tech-PHP/new_jk.git (fetch)
	origin  ssh://git@192.168.199.252:52113/Tech-PHP/new_jk.git (push)
	

通过当前查看到的url修改IP地址，进行重新绑定：

	$ git remote set-url origin ssh://git@10.10.0.24:52113/Tech-PHP/new_jk.git

	修改后在查看绑定的url，发生了变换
	$ git remote -v
	origin  ssh://git@10.10.0.24:52113/Tech-PHP/new_jk.git (fetch)
	origin  ssh://git@10.10.0.24:52113/Tech-PHP/new_jk.git (push)

  至此，修改完成。在提交的时候会让你重新确认一遍。输入yes就可以了。


# 结尾：
	
  让每位有需要的开发人员都对git绑定的url坐下修改，之后就可以正常提交代码了。博文到此结束。

  感谢每位看官，么么哒！！！

  