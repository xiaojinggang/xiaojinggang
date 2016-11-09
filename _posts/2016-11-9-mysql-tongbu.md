---
layout: post
title: 数据库的主从
categories: [blog ]
tags: [Film, Talk, ]
description: 测试环境的添加
---	 

# #前言：

之前已经做过了MySQL的编译安装。现在童鞋们，砸门在一起做一个MySQL主从同步的搭建。

我的安装环境：
需要安装的打包文件放在 /app/tool/目录下面
安装路径 /application/mysql

> now, follow me 开整....

# 数据库编译安装

上传cmake-2.8.8.tar.gz      mysql-5.5.25.tar.gz

## 安装编译MySQL过程中需要的依赖包

<pre>
yum install ncurses-devel libaio-devel -y
yum install gcc-c++ -y
</pre>

## 安装编译安装需要的软件

<pre>
cd /app/tool/
tar zxvf cmake-2.8.8.tar.gz
cd cmake-2.8.8
./configure 
gmake
gmake install
</pre>

## 开始安装MySQL，在主服务器上搭建MySQL。
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
echo 'PATH="/application/mysql/bin/:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin"'>>/etc/profile
. /etc/profile

</pre>


## 配置MySQL初始数据，并启动。

<pre>
/application/mysql/scripts/mysql_install_db --basedir=/application/mysql/ --datadir=/application/mysql/data/ --user=mysql
##如果在这个环节出现了/application/mysql/scripts/mysql_install_db 不存在的报错时，重新进行编译安装。
cp /application/mysql/support-files/my-small.cnf /etc/my.cnf
cp /application/mysql/support-files/mysql.server /etc/init.d/mysqld
chmod +x /etc/init.d/mysqld
/etc/init.d/mysqld start
</pre>

至此，MySQL编译安装完成。

## 搭建从库

按照主库的流程安装从库。

# 配置主从关系

## 设置好my.cnf的配置文件

### 主数据库的配置文件：

<pre>
[root@db01 ~]# cat /etc/my.cnf

[client]
port		= 3306
socket		= /application/mysql-5.5.25/tmp/mysql.sock


[mysqld]
port		= 3306
socket		= /application/mysql-5.5.25/tmp/mysql.sock
skip-external-locking
key_buffer_size = 384M
max_allowed_packet = 1M
table_open_cache = 512
sort_buffer_size = 2M
read_buffer_size = 2M
read_rnd_buffer_size = 8M
myisam_sort_buffer_size = 64M
thread_cache_size = 8
query_cache_size = 32M
log-slow-queries=/data/mysqldata/slowquery.log

long_query_time=1
thread_concurrency = 8


log-bin=mysql-bin
server-id	= 1
expire-logs-days = 7
relay-log-space-limit           = 16G
slave-skip-errors = 1032,1062,126,1114,1146,1048,1396,2003 


[mysqldump]
quick
max_allowed_packet = 16M

[mysql]
no-auto-rehash

[myisamchk]
key_buffer_size = 8M
sort_buffer_size = 8M
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout

</pre>

### 从数据库的配置文件:

<pre>
[root@db02 ~]# cat /etc/my.cnf
[client]
port		= 3306
socket		= /application/mysql-5.5.25/tmp/mysql.sock


[mysqld]
port		= 3306
socket		= /application/mysql-5.5.25/tmp/mysql.sock
skip-external-locking
key_buffer_size = 384M
max_allowed_packet = 1M
table_open_cache = 512
sort_buffer_size = 2M
read_buffer_size = 2M
read_rnd_buffer_size = 8M
myisam_sort_buffer_size = 64M
thread_cache_size = 8
query_cache_size = 32M
log-slow-queries=/data/mysqldata/slowquery.log

long_query_time=1
thread_concurrency = 8


#log-bin=mysql-bin
server-id	= 2
slave-skip-errors = 1032,1062,126,1114,1146,1048,1396




[mysqldump]
quick
max_allowed_packet = 16M

[mysql]
no-auto-rehash

[myisamchk]
key_buffer_size = 8M
sort_buffer_size = 8M
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout

</pre>

## 进入到主数据库

<pre>
mysql
grant replication slave on *.* to sutongwang@'192.168.199.%' identified by '123456';
flush privileges;
</pre>

## 锁表导出数据

<pre>
flush table with read lock;
\q
mysqldump -uroot -A -B --events |gzip >/server/backup/repl_bak_$(date +%F).sql.gz
</pre>

## 将导出的数据传到从库上面去并解压导入到数据库中
<pre>
scp -a /server/backup/repl_bak_$(date +%F).sql.gz root@192.168.199.160:/server/backup/
mysql < /server/backup/repl_bak_$(date +%F).sql.gz
</pre>

## 进入到从库

<pre>
mysql
stop slave;
CHANGE MASTER TO  
MASTER_HOST='192.168.199.159', 
MASTER_PORT=3306,
MASTER_USER='rep', 
MASTER_PASSWORD='oldboy123', 
MASTER_LOG_FILE='mysql-bin.000010',
MASTER_LOG_POS=245;
start slave;
show slave status\G;
#             Slave_IO_Running: Yes
#             Slave_SQL_Running: Yes
#             Seconds_Behind_Master: 0
##出现以上数据，同步成功。
</pre>

> now 童鞋们  数据同步完成啦。




