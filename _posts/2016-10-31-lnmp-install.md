---
layout: post
title: lnmp环境的搭建
categories: [blog ]
tags: [Film, Talk, ]
description: 测试环境的添加
---

# #前言

由于公司的业务扩张，领导以购买2台服务器，并打算将云上的一台web服务转移到传统的实体机上。于是乎，我将环境在测试机上搭建了2遍，确认无误后，编辑成文档。
在这里我将lnmp的搭建流程和搭建中遇到的故障做进一步的说明。
这里，我将是在一台全新的测试环境上搭建。

## 路径说明：

* 1、	安装包存放目录： /app/tool/
* 2、	软件安装目录：/app/soft/
* 3、	项目存放目录：/app/www/
* 4、	启动路径：
	- a)	mysql：/etc/init.d/mysqld {start|stop|restart}
    - b)	php：/app/soft/php5.6.16/sbin/php-fpm
    - c)	nginx：/app/soft/nginx/sbin/nginx -t && /app/soft/nginx/sbin/nginx -s reload
  
<pre>
mkdir -p /app/tool/
mkdir -p /app/soft/
mkdir /app/www/
</pre>

## MySQL的安装：

> 将MySQL-5.5.49的二进制安装包上传到/app/tool/目录下面

<pre>
groupadd mysql
useradd mysql -g mysql -M -s /sbin/nologin
mkdir -p /app/tool/
cd /app/tool/
tar xf mysql-5.5.49-linux2.6-x86_64.tar.gz 
mv mysql-5.5.49-linux2.6-x86_64 /app/soft/mysql-5.5.49
ln -s /app/soft/mysql-5.5.49/ /app/soft/mysql
/app/soft/mysql/scripts/mysql_install_db --basedir=/app/soft/mysql/ --datadir=/app/soft/mysql/data/ --user=mysql
\cp /app/soft/mysql/support-files/my-small.cnf /etc/my.cnf 
sed -i 's#/usr/local/mysql#/app/soft/mysql#g' /app/soft/mysql/bin/mysqld_safe
echo "PATH=/app/soft/mysql/bin/:$PATH" >>/etc/profile
. /etc/profile
cp /app/soft/mysql/support-files/mysql.server /etc/init.d/mysqld
sed -i 's#/usr/local/mysql#/app/soft/mysql#g' /etc/init.d/mysqld
chmod +x /etc/init.d/mysqld
killall mysqld
killall mysqld
/etc/init.d/mysqld start
lsof -i :3306
chkconfig mysqld on
chkconfig --list|grep mysqld
</pre>

> 至此，MySQL安装完成。

## nginx的安装

> nginx使用的是一键安装脚本：安装的nginx的版本为nginx-1.9.9

<pre>
mkdir -p /server/scripts/
cd /server/scripts/
</pre>

> ##编辑nginx.sh脚本--nginx的安装脚本

<pre>
vim nginx.sh
#!/bin/sh
####################################################
#This is a shell scripts to one-key install for lnmp  
####################################################
#######################start########################
cd /app/tool &&\
[ -f nginx-1.9.9.tar.gz ] || wget http://nginx.org/download/nginx-1.9.9.tar.gz
[ -f pcre-8.30.tar.gz ] || wget http://sourceforge.net/projects/pcre/files/pcre/8.30/pcre-8.30.tar.gz/download
yum install cc+ gcc -y
####################install libpcre################
tar zxf pcre-8.30.tar.gz
cd ./pcre-8.30 &&\
./configure
make && make install
cd .. &&\
rm -rf ./pcre-8.30
#####################install nginx#################
cd /app/tool && tar zxf nginx-1.9.9.tar.gz
cd ./nginx-1.9.9
mkdir /app/soft/nginx-1.9.9
useradd nginx -s /sbin/nologin -M
yum install openssl* gd-devel -y
./configure --user=nginx --group=nginx --prefix=/app/soft/nginx-1.9.9 --with-http_image_filter_module --with-http_stub_status_module --with-http_ssl_module
make && make install
#######################test install#################
ln -s /app/soft/nginx-1.9.9/ /app/soft/nginx
cd /app/soft
if [ $? -eq 0 ];then
  echo "/usr/local/lib" >> /etc/ld.so.conf
  ldconfig
  /app/soft/nginx-1.9.9/sbin/nginx
  if [ $? -eq 0 ];then
     echo "service nginx start"
     echo  `lsof -i :80`
  fi
fi
</pre>

## 安装php

>php使用的是编译安装，安装的版本是php-5.6.16版本。

<pre>
###安装依赖环境包
useradd www -s /sbin/nologin -M
yum -y install curl-devel
yum -y install libxslt-deve
yum -y install gcc gcc-c++ autoconf libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel libidn libidn-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients openldap-servers libevent libevent-devel
yum install zlib libxml libjpeg freetype libpng gd curl libiconv mysql-devel zlib-devel libxml2-devel libjpeg-devel freetype-devel libpng-devel gd-devel curl-devel libxslt* -y
yum -y install libxslt-deve
cd /app/tool/
yum install zlib-devel libxml2-devel libjpeg-devel libiconv-devel -y
yum install freetype-devel libpng-devel gd-devel curl-devel libxslt-devel –y
yum install zlib-devel libxml2-devel libjpeg-devel libiconv-devel –y
yum install freetype-devel libpng-devel gd-devel curl-devel libxslt-devel -y
#安装libiconv-devel
cd /app/tool/
wget http://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.14.tar.gz
tar zxf libiconv-1.14.tar.gz
cd libiconv-1.14
./configure --prefix=/usr/local/libiconv
 cd srclib/
sed -ir -e '/gets is a security/d' ./stdio.in.h
cd ../
make
make install
cd ../
#安装libmcrypt库
wget http://sourceforge.net/projects/mcrypt/files/Libmcrypt/2.5.8/libmcrypt-2.5.8.tar.gz
tar zxf libmcrypt-2.5.8.tar.gz 
cd libmcrypt-2.5.8
./configure
make
make install
sleep 2
/sbin/ldconfig
cd libltdl/
./configure --enable-ltdl-install
make
make install
cd ../../

yum install mhash mhash-devel -y
echo "/usr/local/lib" >>/etc/ld.so.conf
rm -f /usr/lib/libmcrypt.*
rm -f /usr/lib/libmhash*
ln -s /usr/local/lib/libmcrypt.la /usr/lib/libmcrypt.la
ln -s /usr/local/lib/libmcrypt.so /usr/lib/libmcrypt.so
ln -s /usr/local/lib/libmcrypt.so.4 /usr/lib/libmcrypt.so.4
ln -s /usr/local/lib/libmcrypt.so.4.4.8 /usr/lib/libmcrypt.so.4.4.8
ln -s /usr/local/lib/libmhash.a /usr/lib/libmhash.a
ln -s /usr/local/lib/libmhash.la /usr/lib/libmhash.la
ln -s /usr/local/lib/libmhash.so /usr/lib/libmhash.so
ln -s /usr/local/lib/libmhash.so.2 /usr/lib/libmhash.so.2
ln -s /usr/local/lib/libmhash.so.2.0.1 /usr/lib/libmhash.so.2.0.1
ln -s /usr/local/bin/libmcrypt-config /usr/bin/libmcrypt-config

###安装php
cd /app/tool/
wget http://cn2.php.net/distributions/php-5.6.16.tar.gz
tar zxf php-5.6.16.tar.gz
cd php-5.6.16
./configure --prefix=/app/soft/php5.6.16 --with-mysql=mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --with-iconv-dir=/usr/local/libiconv --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib --with-libxml-dir=/usr --enable-xml --disable-rpath --enable-safe-mode --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --with-curlwrappers --enable-mbregex --enable-fpm --enable-mbstring --with-mcrypt --with-gd --enable-gd-native-ttf --with-openssl --with-mhash --enable-pcntl --enable-sockets --with-xmlrpc --enable-soap --enable-short-tags --enable-zend-multibyte --enable-static --with-xsl --with-fpm-user=www --with-fpm-group=www --enable-ftp --enable-opcache=no
make
make install
cp php.ini-development /app/soft/php5.6.16/lib/php.ini
cp /app/soft/php5.6.16/etc/php-fpm.conf.default /app/soft/php5.6.16/etc/php-fpm.conf
cp -R ./sapi/fpm/php-fpm /app/soft/php5.6.16/sbin/php-fpm
/app/soft/php5.6.16/sbin/php-fpm
lsof -i :9000

</pre>
至此，php环境安装完成

修改配置文件，即可。

## 此处需要提出我在安装的时候遇见的问题：

当安装插件 libiconv-devel的时候
出现如下错误：
![](http://7xrn7f.com1.z0.glb.clouddn.com/16-10-31/21295374.jpg)

解决方案：

<pre>
cd ./srclib/
sed -ir -e '/gets is a security/d' ./stdio.in.h
cd ../
make
make install
</pre>

故障解决  重新编译！！
