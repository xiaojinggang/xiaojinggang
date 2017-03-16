---
layout: post
title: Jenkins忘记登录密码
categories: [blog ]
tags: [Film, Talk, ]
description: 测试环境的添加
---	   
	   

# 前言 

之前在测试环境下搭建了Jenkins，但是一直将这个项目给搁浅了，最近领导说要开始做持续集成。于是我就开始重新梳理以前的东西。当登录的时候，突然忘记了登录密码，这下小弟就很头疼的去回忆，去猜想，去排列组合测试。就在小弟一心沉浸在自己的思路中的时候，这时，高人出现。高人用他纤细的手指给我指指点点的几下，小弟幡然大悟。最后成功的免密码登录了。再次给做个记录，防止小弟大脑抽搐再出现此故障......

## 查看jenkins目录下的config.xml文件

在文件中找到如下图中标记的内容，将true改为false，并且删除用黄线标记的三行文件内容
`</authorizationStrategy>......</authorizationStrategy>`

![](http://p1.bqimg.com/567571/dd3039f5ba53be7f.png)

## 重启jenkins，然后再在网页中打开，此时，你就看不到login的界面了，呈现在你眼前的是登陆后的界面了。 ( •̀ ω •́ )y

至此，结束。

> this is so easy    

