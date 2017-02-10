---
layout: post
title: Python基础day3   ##页面显示标题名
categories: [blog ]
tags: [Film, Talk, ]
description: 学习阶段
---	   
	   

# Python基础第三章

# 1.文件处理

<pre>
文件句柄 = file('文件路径'，'模式')

注：Python中打开文件有两种方式，open(...)和file(...)本质上前者在内不会调用后者来进行文件操作，推荐使用open。
</pre>

## 1.1 打开文件

<pre>
打开文件模式有:
f = open('db','r')  # 只读模式,{默认}
f = open('db','w')  # 只写模式,{不可读;不存在则创建;存在则删除内容;}
f = open('db','a')  # 追加模式,{可读;不存在则创建;存在则只追加内容;}
f = open('db','x')  # 文件存在,则报错;不存在,则创建并写内容


"+"表示可以同时读写某个文件:
f = open('db','r+')  # 可读写文件,{可读;可写;可追加}
f = open('db','w+')  # 写读。
f = open('db','a+')  # 同a。


"U"表示在读取时，可以将\r\n自动转换成\n (与r或r+模式同使用)
f = open('db','rU')
f = open('db','r+U')


"b"表示处理二进制文件(如ftp上传ISO镜像，Linux忽略，windows处理二进制文件时需标注)
f = open('db','rb')  # 二进制只读
f = open('db','wb')  # 二进制只写
f = open('db','ab')  # 二进制追加


f = open('db','r')
data = f.read()
print(data,type(data))
f.close()
</pre>

## 1.2 操作文件

<pre>
f = open('db','r+',encoding="utf-8")
data = f.read(1) # 如果打开模式无b,则read,按照字符读取
print(f.tell())  # tell当前指针所在的位置(字节)
f.seek(f.tell()) # 调整当前指着你的位置(字节)
f.write("777")   # 当前指针位置开始覆盖
print(data)      # 打印输出
f.close()        # 关闭当前文件


通过源码查看功能
read()      # 无参数,读全部;有参数,b字节,无b按字符
tell()      # 获取当前指针位置(字节)
seek()      # 指针跳转到指定位置(字节)
write()     # 写数据,b:字节,无b:字符
close()     # 关闭文件 
fileno()    # 文件描述符
flush()     # 刷新文件内部缓冲区
readline()  # 仅读取一行
truncate()  # 截取,指针位置后的清空
</pre>

## 1.3 for循环文件对象

<pre>
f = open(“”)
for line in f:
    print(line)
</pre>

## 1.4 文件修改

<pre>
# db文件里面有"xuliangwei"字符串

f = open("db","r",encoding="utf-8")
f_new = open("db.bak","w",encoding="utf-8")

for line in f:
    if "xuliangwei" in line:
        line = line.replace("xuliangwei","xuliangwei.com") 
    f_new.write(line)
f.close()
f_new.close()
</pre>

## 1.5 关闭文件

<pre>
f.close()  #直接close文件

避免打开文件后忘记关闭，统一通过管理上下文,即:
with open('db1','r') as f1, open("db2",'w' )as f2:
    pass
</pre>

# python字符编码转换

asiica 不支持中文
utf-8 一个汉子:三个字节
gbk 一个汉子:二个字节


Python3中默认字符编码是unicode

<pre>
# Author:he li
#-*- coding:utf-8 -*-

msg = "何力"

print(msg.encode('utf-8'))   # b'\xe4\xbd\x95\xe5\x8a\x9b'
print(msg.encode('GBK'))   # b'\xba\xce\xc1\xa6'
print(msg.encode('ASCII'))    #执行结果报错，ASCII不支持中文。

</pre>

## Python2.*中默认字符编码是ASSCI

在文件中制定编码为UTF-8，但是UTF-8如果你想转GBK的话是不能直接转，需要UBicode做一个转换点，如下图：

![](http://i1.piimg.com/567571/5220e457987e1fa9.png)