---
layout: post
title:  "mongodb update，根据条件修改一条或多条记录"
categories: mongodb
tags: mongodb
author: Init
---

* content
{:toc}

nginx安装： 





## db.table_name.update(where,setNew,issert,multi );
查看tomcat占用情况

使用命令：

ps -aux | grep tomcat

发现并没有8080端口的Tomcat进程。

使用命令：netstat –apn

查看所有的进程和端口使用情况。发现下面的进程列表，其中最后一栏是PID/Program name 



发现8080端口被PID为9658的Java进程占用。

进一步使用命令：ps -aux | grep java，或者直接：ps -aux | grep pid 查看



就可以明确知道8080端口是被哪个程序占用了！然后判断是否使用KILL命令干掉！


方法二：直接使用 netstat   -anp   |   grep  portno
即：netstat –apn | grep 8080