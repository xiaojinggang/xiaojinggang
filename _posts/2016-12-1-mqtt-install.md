---
layout: post
title: centos7环境安装mqtt
categories: [blog ]
tags: [Film, Talk, ]
description: 测试环境的添加
---	   

# #前言：

公司的业务需要MQtt的支持，所以需要搭建服务。

mqtt(消息队列遥测传输)是IBM开发的一个即时通讯协议，该协议至迟所有的平台，被用来当作传感器和致动器(比如通过Weiiter让房屋联网)的通信协议。

mqtt的应用：
国内很多企业都广泛使用MQTT作为Android手机客户端与服务器端推送消息的协议。
手机客户端中均有使用到mqtt作为消息推送消息。

# 安装流程：

## 添加yum源

<pre>
# cd /etc/yum.repos.d/

# vim mosquitto.repo
[home_oojah_mqtt]
name=mqtt (CentOS_CentOS-7)
type=rpm-md
baseurl=http://download.opensuse.org/repositories/home:/oojah:/mqtt/CentOS_CentOS-7/
gpgcheck=1
gpgkey=http://download.opensuse.org/repositories/home:/oojah:/mqtt/CentOS_CentOS-7/repodata/repomd.xml.key
enabled=1
</pre>

## 下载应用插件和服务软件

<pre>
# sudo yum search all mosquitto 
# sudo yum install mosquitto mosquitto-clients libmosquitto-devel libmosquittopp-devel python-mosquitto -y
</pre>

## 安装完成之后，所有配置文件会被放置于/etc/mosquitto/ 目录下，期中最重要的就是mosquitto的配置文件

<pre>
# vim /etc/mosquitto/conf.d/myconf.conf
password_file /etc/yum.repos.d/passwd
allow_anonymous false
</pre>

## 命令生成用户和密码，系统会让输入两遍。

<pre>
# sudo mosquitto_passwd -c passwd user_name 
</pre>
需要注意：username是需要输入的用户名

## 查看生成的秘钥

<pre>
cat /etc/yum.repos.d/passwd
</pre>

## 将服务设置成开机自启动服务

<pre>
chkconfig mosquitto on
</pre>

## 启动服务

<pre>
/etc/init.d/mosquitto start
</pre>







> now, this ok! please fly with me ~ ~ ~
