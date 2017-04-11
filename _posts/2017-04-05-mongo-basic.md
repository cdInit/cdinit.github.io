---
layout: post
title:  "MONGODB基本命令"
categories: mongodb
tags: mongodb
author: Init
---

* content
{:toc}

成功启动MongoDB后，再打开一个命令行窗口输入mongo，就可以进行数据库的一些操作。
输入help可以看到基本操作命令
db.help()                    help on db methods 显示数据库操作命令，里面有很多的命令
db.mycoll.help()             help on collection methods 显示集合操作命令，同样有很多的命令，mycoll指的是当前数据库下，一个叫mycoll的集合，并非真正意义上的命令 
sh.help()                    sharding helpers
rs.help()                    replica set helpers
help admin                   administrative help
help connect                 connecting to a db help
help keys                    key shortcuts
help misc                    misc things to know
help mr                      mapreduce

show dbs                     show database names 显示数据库列表
show collections             show collections in current database 显示当前数据库中的集合（类似关系数据库中的表） 
show users                   show users in current database 显示用户
show profile                 show most recent system.profile entries with time >= 1ms
show logs                    show the accessible logger names
show log [name]              prints out the last segment of log in memory, 'global' is default
use <db_name>                set current database 切换当前数据库，这和MS-SQL里面的意思一样 
db.foo.find()                list objects in collection foo 对于当前数据库中的foo集合进行数据查找（由于没有条件，会列出所有数据）
db.foo.find( { a : 1 } )     list objects in foo where a == 1 对于当前数据库中的foo集合进行查找，条件是数据中有一个属性叫a，且a的值为1 
it                           result of the last line evaluated; use to further iterate
DBQuery.shellBatchSize = x   set default number of items to display on shell
exit                         quit the mongo shell

MongoDB没有创建数据库的命令，但有类似的命令。

如：如果你想创建一个"test"的数据库，先运行use test命令，之后就做一些操作（如：db.createCollection('user')）,这样就可以创建一个名叫"test"的数据库并创建user表。





## 数据库常用命令

* Help查看命令提示
> help

  > db.help();

  > db.yourColl.help();

  > db.youColl.find().help();

  > rs.help();

* 切换/创建数据库
> use yourDB;  当创建一个集合(table)的时候会自动创建当前数据库

* 查询所有数据库
> show dbs

* 删除当前使用数据库
> db.dropDatabase();

* 从指定主机上克隆数据库
> db.cloneDatabase(“127.0.0.1”); 将指定机器上的数据库的数据克隆到当前数据库

* 从指定的机器上复制指定数据库数据到某个数据库
> 

## mongdb学习地址
[菜鸟教程学习mongdb](http://www.runoob.com/mongodb/mongodb-tutorial.html)
