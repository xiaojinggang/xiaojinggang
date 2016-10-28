---
layout: post
title: fastdfs环境的搭建
categories: [blog ]
tags: [Film, Talk, ]
description: 测试环境的添加
---

<center>
# FASTDFS的搭建 #
</center>

## FastDFS简介：

fastDFS是一款开源的轻量级分布式文件系统。

### 特性：

* 纯C实现，支持Linux、FreeBSD等unix系统
* 类googleFS，不是通用的文件系统，只能通过专有API访问，目前提供了C，java，PHP API
* 为互联网应用量身定做，追求高性能和高扩展性
* FastDFS可以看作是基于文件的key value pair 存储系统，是分布式文件存储服务

FastDFS 适合4K<文件<500M大小的文件存储

FastDFS架构模型：

![](http://p1.bpimg.com/567571/639db67aebb0d8c5.png)

tracker server：跟踪服务器，主要做调度工作，在访问上器负载均衡的作用。在内存中记录及群众group和storage server的状态信息，是连接client和storage server的枢纽。
因为相关信息全部在内存中，tracker server的性能非常高，一个较大的集群（比如加上百个group）中有3台救足够了。

storage server：存储服务器，文件和文件属性（meta data）都保存在存储服务器上。

![](http://p1.bpimg.com/567571/cbf9c4f4e6262385.png)

上传文件时，文件ID由storage server生成并返回给client
文件ID包含了组名盒文件名，storage server可以直接根据文件名定位到文件。

![](http://p1.bpimg.com/567571/4db8e646d05db286.png)

注：目录下不要有过多的文件，如果文件过多，则需要新建目录，否则，当ls时，会遍历整个目录，对服务器造成压力，进而影响性能，影响用户体验

![](http://p1.bpimg.com/567571/bf6f7b07dc1648b0.png)

## #安装（所有节点都需要按照如下步骤进行安装）

### 1、安装libfastcommon步骤

<pre>
cd /usr/local/src/
wget https://github.com/happyfish100/libfastcommon/archive/V1.0.7.tar.gz
tar xf V1.0.7.tar.gz
cd libfastcommon-1.0.7/
./make.sh
./make.sh install
cd ../
</pre>

### 2、安装FastDFS

<pre>
wget https://github.com/happyfish100/fastdfs/archive/V5.05.tar.gz
tar xf V5.05.tar.gz
cd fastdfs-5.05/
./make.sh
./make.sh install
</pre>

### 3、修改启动脚本

<pre>
cd /etc/fdfs/
ls -l /etc/init.d/fdfs*
whereis fdfs_trackerd
# fdfs_trackerd: /usr/bin/fdfs_trackerd
sed -i 's#/usr/local/bin#/usr/bin#g'  /etc/init.d/fdfs_storaged
sed -i 's#/usr/local/bin#/usr/bin#g'  /etc/init.d/fdfs_trackerd
</pre>

### 4、创建存储目录和跟踪目录

<pre>
mkdir /data/fdfs_tracker -p
mkdir /data/fdfs_storage -p
</pre>

### 5、拷贝新的配置文件

<pre>
cp storage.conf.sample storage.conf
cp tracker.conf.sample  tracker.conf
</pre>

### 6、修改tracker.conf配置文件

<pre>
vim tracker.conf
base_path=/data/fdfs_tracker

# the method of selecting group to upload files               ###文件上传方式
# 0: round robin                                      ###轮询                   
# 1: specify group                                     ###指定组
# 2: load balance, select the max free space group to upload file  ###负载均衡，选择剩余存储空间最大的机器存储，为默认选项
store_lookup=2
  
# which storage server to upload file                      ###选择哪个存储server上传文件
# 0: round robin (default)                              ###轮询
# 1: the first server order by ip address                   ###指定IP
# 2: the first server order by priority (the minimal)          ###权重
store_server=0                                      ###默认为0
</pre>

### 7、修改storage.conf配置文件
<pre>
vim storage.conf
base_path=/data/fdfs_storage/base
store_path0=/data/fdfs_storage/store
tracker_server=192.168.56.33:22122
tracker_server=192.168.56.34:22122     ##该示例为多个server端
</pre>

### 8、查看并对比默认配置文件及修改后的配置文件
<pre>
[root@web fdfs]# grep '^[a-z]' tracker.conf
disabled=false	
bind_addr=
port=22122
connect_timeout=30
network_timeout=60
base_path=/data/fdfs_tracker
max_connections=256
accept_threads=1
work_threads=4
store_lookup=2
store_group=group2
store_server=0
store_path=0
download_server=0
reserved_storage_space = 10%
log_level=info
run_by_group=
run_by_user=
allow_hosts=*
sync_log_buff_interval = 10
check_active_interval = 120
thread_stack_size = 64KB
storage_ip_changed_auto_adjust = true
storage_sync_file_max_delay = 86400
storage_sync_file_max_time = 300
use_trunk_file = false 
slot_min_size = 256
slot_max_size = 16MB
trunk_file_size = 64MB
trunk_create_file_advance = false
trunk_create_file_time_base = 02:00
trunk_create_file_interval = 86400
trunk_create_file_space_threshold = 20G
trunk_init_check_occupying = false
trunk_init_reload_from_binlog = false
trunk_compress_binlog_min_interval = 0
use_storage_id = false
storage_ids_filename = storage_ids.conf
id_type_in_filename = ip
store_slave_file_use_link = false
rotate_error_log = false
error_log_rotate_time=00:00
rotate_error_log_size = 0
log_file_keep_days = 0
use_connection_pool = false
connection_pool_max_idle_time = 3600
http.server_port=8080
http.check_alive_interval=30
http.check_alive_type=tcp
http.check_alive_uri=/status.html

[root@web fdfs]# grep '^[a-z]' storage.conf
disabled=false
group_name=group1
bind_addr=
client_bind=true
port=23000
connect_timeout=30
network_timeout=60
heart_beat_interval=30
stat_report_interval=60
base_path=/data/fdfs_storage/base
max_connections=256
buff_size = 256KB
accept_threads=1
work_threads=4
disk_rw_separated = true
disk_reader_threads = 1
disk_writer_threads = 1
sync_wait_msec=50
sync_interval=0
sync_start_time=00:00
sync_end_time=23:59
write_mark_file_freq=500
store_path_count=1
store_path0=/data/fdfs_storage/store
subdir_count_per_path=256
tracker_server=192.168.56.138:22122
tracker_server=192.168.56.130:22122
log_level=info
run_by_group=
run_by_user=
allow_hosts=*
file_distribute_path_mode=0
file_distribute_rotate_count=100
fsync_after_written_bytes=0
sync_log_buff_interval=10
sync_binlog_buff_interval=10
sync_stat_file_interval=300
thread_stack_size=512KB
upload_priority=10
if_alias_prefix=
check_file_duplicate=0
file_signature_method=hash
key_namespace=FastDFS
keep_alive=0
use_access_log = false
rotate_access_log = false
access_log_rotate_time=00:00
rotate_error_log = false
error_log_rotate_time=00:00
rotate_access_log_size = 0
rotate_error_log_size = 0
log_file_keep_days = 0
file_sync_skip_invalid_record=false
use_connection_pool = false
connection_pool_max_idle_time = 3600
http.domain_name=
http.server_port=8888

[root@web fdfs]# diff storage.conf storage.conf.sample 
41c41
< base_path=/data/fdfs_storage/base
---
> base_path=/home/yuqing/fastdfs
109c109
< store_path0=/data/fdfs_storage/store
---
> store_path0=/home/yuqing/fastdfs
118,119c118
< tracker_server=192.168.56.138:22122
< tracker_server=192.168.56.130:22122
---
> tracker_server=192.168.209.121:22122

[root@web fdfs]# diff tracker.conf tracker.conf.sample 
22c22
< base_path=/data/fdfs_tracker
---
> base_path=/home/yuqing/fastdfs
</pre>

### 9、启动服务

<pre>
[root@shop fdfs]# /etc/init.d/fdfs_trackerd start
Starting FastDFS tracker server:    

[root@shop fdfs]# cd /data/fdfs_storage/
[root@shop fdfs_storage]# mkdir base store
[root@shop fdfs]# /etc/init.d/fdfs_storaged start
Starting FastDFS storage server:

[root@web fdfs]# netstat -lntup|grep 22122
tcp        0      0 0.0.0.0:22122               0.0.0.0:*                   LISTEN      48120/fdfs_trackerd 
[root@web fdfs]# netstat -lntup|grep 23000
tcp        0      0 0.0.0.0:23000               0.0.0.0:*                   LISTEN      48141/fdfs_storaged
</pre>

### 10、生成并配置client.cinf
<pre>
[root@web logs]# cd /etc/fdfs/
[root@web fdfs]# ls
client.conf.sample  storage.conf  storage.conf.sample  tracker.conf  tracker.conf.sample
[root@web fdfs]# ll
total 36
-rw-r--r-- 1 root root 1461 Jul 20 07:52 client.conf.sample
-rw-r--r-- 1 root root 7871 Jul 20 08:46 storage.conf
-rw-r--r-- 1 root root 7829 Jul 20 07:52 storage.conf.sample
-rw-r--r-- 1 root root 7100 Jul 20 08:38 tracker.conf
-rw-r--r-- 1 root root 7102 Jul 20 07:52 tracker.conf.sample
[root@web fdfs]# cp client.conf.sample client.conf
[root@web fdfs]# vim client.conf
base_path=/tmp/
tracker_server=192.168.56.138:22122
tracker_server=192.168.56.130:22122

[root@web fdfs]# diff client.conf client.conf.sample 
10c10
< base_path=/tmp/
---
> base_path=/home/yuqing/fastdfs
14,15c14
< tracker_server=192.168.56.138:22122
< tracker_server=192.168.56.130:22122
---
> tracker_server=192.168.0.197:22122
</pre>

### 11、上传、下载文件测试

<pre>
[root@web fdfs]# fdfs_upload_file /etc/fdfs/client.conf /etc/passwd          上 传 命 令   配 置 文 件    文 件
group1/M00/00/00/wKg4glePdJeAHOrgAAAEeGoQmdM9366986        上 传 完 成 后 生 成 的 新 文 件
[root@web fdfs]# fdfs_download_file /etc/fdfs/client.conf group1/M00/00/00/wKg4glePdJeAHOrgAAAEeGoQmdM9366986   下 载 命 令  配 置 文 件  生 成 的 新 上 传 的文 件
[root@web fdfs]# ls  
client.conf         storage.conf         tracker.conf         wKg4glePdJeAHOrgAAAEeGoQmdM9366986
client.conf.sample  storage.conf.sample  tracker.conf.sample
[root@web fdfs]# md5sum wKg4glePdJeAHOrgAAAEeGoQmdM9366986    通 过 使 用 md5 值 比 对 新 生 成 的 文 件 及 源 文 件 
907ac7438b30f1cd514768e722d5fb59  wKg4glePdJeAHOrgAAAEeGoQmdM9366986
[root@web fdfs]# md5sum /etc/passwd    
907ac7438b30f1cd514768e722d5fb59  /etc/passwd 
</pre>  

> 在另一台存储服务器查看文件过程如下

<pre>
[root@shop fdfs]# cd /data/fdfs_storage/store/
[root@shop store]# cd data/
[root@shop data]# cd 00
[root@shop 00]# cd 00
[root@shop 00]# ls
wKg4glePdJeAHOrgAAAEeGoQmdM9366986
[root@shop 00]# cat wKg4glePdJeAHOrgAAAEeGoQmdM9366986 
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
uucp:x:10:14:uucp:/var/spool/uucp:/sbin/nologin
…….
……..
[root@shop 00]# md5sum wKg4glePdJeAHOrgAAAEeGoQmdM9366986 
907ac7438b30f1cd514768e722d5fb59  wKg4glePdJeAHOrgAAAEeGoQmdM9366986

[root@web fdfs]# fdfs_file_info /etc/fdfs/client.conf group1/M00/00/00/wKg4glePdJeAHOrgAAAEeGoQmdM9366986    ### 查 看 源 文 件 属 性，需 要 全 路 径
source storage id: 0                                                                                     
source ip address: 192.168.56.130                                                                         ### 源 IP
file create timestamp: 2016-07-20 08:54:47                                                                  ### 上 传 时 间
file size: 1144                                                                                         ### 文 件 大 小
file crc32: 1779472851 (0x6A1099D3)                                                                     

[root@shop fdfs]# fdfs_delete_file /etc/fdfs/client.conf group1/M00/00/00/wKg4glee9w-EMctrAAAAAJ8Gt-U875.txt    ### 删 除 文 件
[root@shop fdfs]# ls
client.conf         storage.conf         tracker.conf         wKg4glee9w-EMctrAAAAAJ8Gt-U875.txt
client.conf.sample  storage.conf.sample  tracker.conf.sample  wKg4gleeyD6AHzZWAAAAJ58Gt-U662.txt
[root@shop fdfs]# fdfs_file_info /etc/fdfs/client.conf group1/M00/00/00/wKg4glee9w-EMctrAAAAAJ8Gt-U875.txt     ### 查 看 文 件
[2016-08-01 15:25:29] ERROR - file: tracker_proto.c, line: 48, server: 192.168.56.130:23000, response status 2 != 0     #### 因 为 已 经 删 除 ，所 以 报 错 没 有 这 个 文 件
query file info fail, error no: 2, error info: No such file or directory
</pre>

> 通过追加的方式上传文件示例

<pre>
[root@shop fdfs]# vim /root/test2.txt

The txt is create by litao

[root@shop fdfs]# fdfs_append_file /etc/fdfs/client.conf group1/M00/00/00/wKg4glee9w-EMctrAAAAAJ8Gt-U875.txt /root/test2.txt    
[root@shop fdfs]# fdfs_download_file /etc/fdfs/client.conf group1/M00/00/00/wKg4glee9w-EMctrAAAAAJ8Gt-U875.txt

[root@shop fdfs]# cat wKg4glee9w-EMctrAAAAAJ8Gt-U875.txt
This file is used to test the fastdfs.
The txt is create by litao    
</pre>

> 可以做成PHP的扩展之后，用PHP直接调用，详情点击
[小精钢跳板](http://www.it165.net/admin/html/201308/1628.html)

>步骤如下：

<pre>
cd /usr/local/src/fastdfs-5.05/php_client/
/usr/local/php/bin/phpize 
./configure --with-php-config=/usr/local/php/bin/php-config 
echo $?
make && make install
cat fastdfs_client.ini >> /usr/local/php/lib/php.ini 
/usr/local/php/bin/php -m | grep fastdfs_client  
</pre>

> WEB页面展示分布式存储步骤，详情请点击
[小精钢跳板](https://github.com/happyfish100/fastdfs-nginx-module/blob/master/INSTALL)

<pre>
cd /usr/local/src/nginx-1.9.13/src/
git clone https://github.com/happyfish100/fastdfs-nginx-module.git
useradd -s /sbin/nologin -M www
cd /usr/local/src/nginx-1.9.13
./configure --prefix=/usr/local/nginx-1.9.13/ --user=www --group=www --with-http_ssl_module --with-http_stub_status_module --add-module=/usr/local/src/nginx-1.9.13/src/fastdfs-nginx-module/src
make && make install
ln -s /usr/local/src/nginx-1.9.13 /usr/local/nginx
cd /usr/local/src/nginx-1.9.13/src/fastdfs-nginx-module/src
ll
cp mod_fastdfs.conf /etc/fdfs/
cd /usr/local/src/fastdfs-5.05/conf/
cp anti-steal.jpg mime.types http.conf /etc/fdfs/
cd /usr/local/nginx/conf/
vim nginx.conf
location /group1/M00 {
            root   /data/fdfs_storage/store;
            ngx_fastdfs_module;
        }	
</pre>

>　把这段代码写到server标签内

<pre>
vim mod_fastdfs.conf
tracker_server=192.168.56.130:22122
tracker_server=192.168.56.138:22122
url_have_group_name = true
store_path0=/data/fdfs_storage/store
</pre>