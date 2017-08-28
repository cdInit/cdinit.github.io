---
layout: post
title:  "Linux查看程序端口占用情况"
categories: linux
tags: linux
author: Init
---

* content
{:toc}

Linux查看程序端口占用情况
ps -aux | grep tomcat





## 查看tomcat占用情况

使用命令：

``` sh
ps -aux | grep tomcat

或者

ps -aux | grep pid
```

## 查看Tomcat进程

使用命令：

``` sh
netstat –apn
```

查看所有的进程和端口使用情况。


## 其他方式
直接使用 netstat   -anp   |   grep  portno
即：

``` sh
netstat –apn | grep 8080
```