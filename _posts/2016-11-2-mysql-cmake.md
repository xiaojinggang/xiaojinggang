---
layout: post
title: mysql的编译安装
categories: [blog ]
tags: [Film, Talk, ]
description: 测试环境的添加
---	   
	  
# #前言

之前在lnmp环境中写了mysql的二进制安装，现在写一份mysql的编译安装，这里提一下二进制安装和编译安装的区别。

二进制安装包，里面的文件的路径已经设定好了。 编译安装包，你可以自己设定自己的安装启动路径。

下面来说一下mysql的编译安装。（下面以一台全新的服务器上的环境为例）

我安装的是mysql-5.5.25.tar.gz 

已经实现将MySQL和cmake的tar包已经上传到/app/tool/

# #首先安装mysql所需要的依赖包

<pre>
yum install ncurses-devel libaio-devel -y
yum install gcc-c++ -y
</pre>

# #安装编译mysql需要的软件

<pre>
cd /app/tool/
tar zxvf cmake-2.8.8.tar.gz
cd cmake-2.8.8
yum install gcc-c++ -y
./configure 
gmake
gmake install
</pre>

### #如果有童鞋./configure的时候出现如下错误时，是因为你所搭建的环境中缺乏C语言编译器，你需要yum install -y gcc-c++

![error图](http://7xrn7f.com1.z0.glb.clouddn.com/16-11-2/61350535.jpg)

# #开始安装MySQL

<pre>
useradd mysql -s /sbin/nologin -M
tar zxf mysql-5.5.25.tar.gz 
cd mysql-5.5.25
cmake . -DCMAKE_INSTALL_PREFIX=/application/mysql-5.5.25 \
-DMYSQL_DATADIR=/application/mysql-5.5.25/data \
-DMYSQL_UNIX_ADDR=/application/mysql-5.5.25/tmp/mysql.sock \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DEXTRA_CHARSETS=gbk,gb2312,utf8,ascii \
-DENABLED_LOCAL_INFILE=ON \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_FEDERATED_STORAGE_ENGINE=1 \
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
-DWITHOUT_EXAMPLE_STORAGE_ENGINE=1 \
-DWITHOUT_PARTITION_STORAGE_ENGINE=1 \
-DWITH_FAST_MUTEXES=1 \
-DWITH_ZLIB=bundled \
-DENABLED_LOCAL_INFILE=1 \
-DWITH_READLINE=1 \
-DWITH_EMBEDDED_SERVER=1 \
-DWITH_DEBUG=0
make
make install
ln -s /application/mysql-5.5.25/ /application/mysql
</pre>

# #配置MySQL的启动路径

<pre>
mkdir -p /data/3306/data/
yum install tree -y
/application/mysql/scripts/mysql_install_db --basedir=/application/mysql/ --datadir=/application/mysql/data/ --user=mysql
 ##如果在这个环节出现了/application/mysql/scripts/mysql_install_db 不存在的报错时，重新对MySQL进行编译安装。
cp /application/mysql/support-files/my-small.cnf /etc/my.cnf
cp /application/mysql/support-files/mysql.server /etc/init.d/mysqld
chmod +x /etc/init.d/mysqld
/etc/init.d/mysqld start
</pre>

至此，MySQL的编译安装完成。