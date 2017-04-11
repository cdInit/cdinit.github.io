---
layout: post
title:  "MONGODB安装"
categories: mongodb
tags: mongodb
author: Init
---

* content
{:toc}

Linux下安装mongdb步骤



## 下载安装包
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel62-3.0.1.tgz 

## 解压
tar -zxvf mongodb-linux-x86_64-rhel62-3.0.1.tgz

## 将解压文件放入安装目录
mv mongodb-linux-x86_64-rhel62-3.0.1 /usr/local/mongodb  

## 新建mongodb数据文件存放目录
mkdir -p /app/mongodb/db  

## 新建log文件存放目录
mkdir -p /app/mongodb/logs  

## 修改配置文件

cd /usr/local/mongodb/bin  

新建配置文件，mongodb支持把参数写进配置文件，执行：

vi mongodb.conf  

加入内容如下： 
dbpath = /app/mongodb/db #数据文件存放目录  
  
logpath = /app/mongodb/logs/mongodb.log #日志文件存放目录  
  
port = 27017  #端口  
  
fork = true  #以守护程序的方式启用，即在后台运行  
  
nohttpinterface = true  

## 启动Mongo程序，使用配置文件mongodb.conf定义的参数启动 
/usr/local/mongodb/bin/mongod --config /usr/local/mongodb/bin/mongodb.conf

## 测试
测试是否安装成功

## 配置开机自动启动mongodb 
vi /etc/rc.d/rc.local 
在文件中加入： 
/usr/local/mongodb/bin/mongod --config /usr/local/mongodb/bin/mongodb.conf  

注意：默认mongodb的数据目录对应的是/data/db下面。日志目录对于到/data/logs/mongodb.log，如果是目录结构有调整需要重新指定配置的路径

在安装过程中注意几个文件和文件目录的建立，没有建立config中的目录启动时候会报错


