---
layout: post
title: linux主机系统优化
categories: [blog ]
tags: [Film, Talk, ]
description: 安全性能
---	   

# 前言：

在使用服务器的过程中，发现连接数达到默认的最大值，这时需要调整最大链接数limit，但是，这个配置需要重启才能生效，影响业务。所以，童鞋们最好在服务器配置的过程中有做好一系列相应的优化，防止以后的不必要麻烦。

** 1、配置阿里云yum，具体修改方法vim /etc/yum.repos.d/CentOS-Base.repo **

wget -O /etc/yum.repos.d/CentOS-Base.repo

http://mirrors.aliyun.com/repo/Centos-6.repo
 
**2、修改开机远程登录端口，具体的修改方法vim /etc/ssh/sshd_conf **

修改端口 Port 22

禁止root登陆 PermitRootLogin no

不允许空密码 PermitEmptyPasswords no

不使用dns  useDNs no

GSSAPIAuthentication no  【GSSAPIAuthentication  当这个参数开启（ GSSAPIAuthentication  yes ）的时候，通过SSH登陆服务器时候会有些会很慢，但有的服务器又不慢，这个问题是什么造成的

**3、关闭selinux**

vi /etc/sysconfig/selinux sed -i “s/SELINUX=enforcing/SELINUX=disabled/g” 

临时生效 setenforce 0，永久生效souce /etc/sysconfig/selinux

**4、关闭iptables**

**5、最小开机启动服务**

 常见最小服务sshd rsyslog network crond sysstat(检测性能工具)
用chkconfig --list|grep -vE “sshd|network|crond”||awk ‘{print “chkconfig” $1 “off”}’

chkconfig --list|grep -vE “sshd|network|crond”|sed  -r “/s(.*)/ chkconfig \1 off/g”|bash

**6、文件描述符ulimit进程占用的文件描述符**

echo '*               -       nofile          65535 ' >>/etc/security/limits.conf 
想要永久生效ulimit -FHn 65535加入到开机启动/etc/rc.d

**7最小化原则 用户权限最小化 目录权限最小化 命令最小化 服务安装包最小化 普通用户管理，禁止root用户登陆**

**8.内核优化 vim /etc/sysctl **

**9、禁止ping echo "net.ipv4.icmp_echo_ignore_all=1" >> /etc/sysctl.conf**

echo 1>/proc/sys/net/ipv4/icmp_echo_ignore_all

**10、清除开机提示 > /etc/issue**

**11、清除历史记录 HISIZE=5 **

HIFILESIZE=5

永久生效vim /etc/profile  HIFILESIZE=10 

**12、锁定特殊文件**

chattr +i /etc/passwd /etc/shadow /etc/group /etc/gshadow /etc/init

**13、配置ntp服务器**

 echo “*/5 * * * * /usr/sbin/ntpdate time.nist.gov >/dev/null 2>&1” >>/var/spool/cron/root

**14、sudo控制管理权限 锁定root用户，通过visudo 或者 /etc/sudoer**

**15、定义中文字符集**

LANG=zh_CN 临时生效

vim /etc/sysconfig/i18n 永久生效



