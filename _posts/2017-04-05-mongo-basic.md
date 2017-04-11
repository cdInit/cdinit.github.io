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

### DB

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
> db.copyDatabase("mydb", "temp", "127.0.0.1");将本机的mydb的数据复制到temp数据库中

* 修复当前数据库
> db.repairDatabase();

* 查看当前使用的数据库
> db.getName();
  > db; db和getName方法是一样的效果，都可以查询当前使用的数据库

* 显示当前db状态
> db.stats();

* 当前db版本
> db.version();

* 查看当前db的链接机器地址
> db.getMongo();

### Collection聚集集合

* 创建一个聚集集合（table）
> db.createCollection(“collName”, {size: 20, capped: 5, max: 100});

* 得到指定名称的聚集集合（table）
> db.getCollection("account");

* 得到当前db的所有聚集集合
> db.getCollectionNames();

* 显示当前db所有聚集索引的状态
> db.printCollectionStats();

### 用户相关

* 添加一个用户
> db.addUser("name");
  > db.addUser("userName", "pwd123", true); 添加用户、设置密码、是否只读

* 数据库认证、安全模式
> db.auth("userName", "123123");

* 显示当前所有用户
> show users;

* 删除用户
> db.removeUser("userName");

### 其他

* 查询之前的错误信息
> db.getPrevError();

* 清除错误记录
> db.resetError();

 

## 查看聚集集合基本信息

* 查看帮助
> db.yourColl.help();

* 查询当前集合的数据条数
> db.yourColl.count();

* 查看数据空间大小
> db.userInfo.dataSize();

* 得到当前聚集集合所在的db
> db.userInfo.getDB();

* 得到当前聚集的状态
> db.userInfo.stats();

* 得到聚集集合总大小
> db.userInfo.totalSize();

* 聚集集合储存空间大小
> db.userInfo.storageSize();

* Shard版本信息
> db.userInfo.getShardVersion()

* 聚集集合重命名
> db.userInfo.renameCollection("users"); 将userInfo重命名为users

* 删除当前聚集集合
> db.userInfo.drop();

## 聚集集合查询

* 查询所有记录

``` sh

db.userInfo.find();

相当于：select* from userInfo;

默认每页显示20条记录，当显示不下的情况下，可以用it迭代命令查询下一页数据。注意：键入it命令不能带“；”

但是你可以设置每页显示数据的大小，用DBQuery.shellBatchSize= 50;这样每页就显示50条记录了。

```
 
* 查询去掉后的当前聚集集合中的某列的重复数据

``` sh

db.userInfo.distinct("name");

会过滤掉name中的相同数据

相当于：select distict name from userInfo;

```

* 查询age = 22的记录

``` sh

db.userInfo.find({"age": 22});

相当于： select * from userInfo where age = 22;

```
 
* 查询age > 22的记录

``` sh

db.userInfo.find({age: {$gt: 22}});

相当于：select * from userInfo where age >22;

```

* 查询age < 22的记录

``` sh

db.userInfo.find({age: {$lt: 22}});

相当于：select * from userInfo where age <22;

```

* 查询age >= 25的记录

``` sh

db.userInfo.find({age: {$gte: 25}});

相当于：select * from userInfo where age >= 25;

```

* 查询age <= 25的记录

``` sh

db.userInfo.find({age: {$lte: 25}});

```

* 查询age >= 23 并且 age <= 26

``` sh

db.userInfo.find({age: {$gte: 23, $lte: 26}});

```

* 查询name中包含 mongo的数据

``` sh

db.userInfo.find({name: /mongo/});

//相当于%%

select * from userInfo where name like ‘%mongo%’;

```
 
* 查询name中以mongo开头的

``` sh

db.userInfo.find({name: /^mongo/});

select * from userInfo where name like ‘mongo%’;

```

* 查询指定列name、age数据

``` sh

db.userInfo.find({}, {name: 1, age: 1});

相当于：select name, age from userInfo;

当然name也可以用true或false,当用ture的情况下河name:1效果一样，如果用false就是排除name，显示name以外的列信息。

```
 
* 查询指定列name、age数据, age > 25

``` sh

db.userInfo.find({age: {$gt: 25}}, {name: 1, age: 1});

相当于：select name, age from userInfo where age >25;

```

* 按照年龄排序

``` sh

升序：db.userInfo.find().sort({age: 1});

降序：db.userInfo.find().sort({age: -1});

```

* 查询name = zhangsan, age = 22的数据

``` sh

db.userInfo.find({name: 'zhangsan', age: 22});

相当于：select * from userInfo where name = ‘zhangsan’ and age = ‘22’;

```

* 查询前5条数据

``` sh

db.userInfo.find().limit(5);

相当于：selecttop 5 * from userInfo;

```

* 查询10条以后的数据

``` sh

db.userInfo.find().skip(10);

相当于：select * from userInfo where id not in (

select top 10 * from userInfo

);

```

* 查询在5-10之间的数据

``` sh

db.userInfo.find().limit(10).skip(5);

可用于分页，limit是pageSize，skip是第几页*pageSize

```

* or与 查询

``` sh

db.userInfo.find({$or: [{age: 22}, {age: 25}]});

相当于：select * from userInfo where age = 22 or age = 25;

```

* 查询第一条数据

``` sh

db.userInfo.findOne();

相当于：select top 1 * from userInfo;

db.userInfo.find().limit(1);

```

* 查询某个结果集的记录条数

``` sh

db.userInfo.find({age: {$gte: 25}}).count();

相当于：select count(*) from userInfo where age >= 25;

如果要返回限制之后的记录数量，要使用count(true)或者count(非0) 

db.users.find().skip(10).limit(5).count(true);

```

* 按照某列进行排序

``` sh

db.userInfo.find({sex: {$exists: true}}).count();

相当于：select count(sex) from userInfo;

```

## 索引

1. 创建索引
> db.userInfo.ensureIndex({name: 1});
  > db.userInfo.ensureIndex({name: 1, ts: -1});

2. 查询当前聚集集合所有索引
> db.userInfo.getIndexes();

3. 查看总索引记录大小
> db.userInfo.totalIndexSize();

4. 读取当前集合的所有index信息
> db.users.reIndex();

5. 删除指定索引
> db.users.dropIndex("name_1");

6. 删除所有索引索引
> db.users.dropIndexes();

## 修改、添加、 删除集合数据

* 添加insert() 或 save() 

``` sh

db.users.save({name: ‘zhangsan’, age: 25, sex: true});
db.users.insert({name: ‘zhangsan’, age: 25, sex: true});
添加的数据的数据列，没有固定，根据添加的数据为准

```

* 修改

``` sh

db.collection.update(criteria, objNew, upsert, multi )

criteria:update的查询条件，类似sql update查询内where后面的

objNew:update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的。

upsert : 如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。

multi : mongodb默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。

```

``` sh
db.users.update({age: 25}, {$set: {name: 'changeName'}}, false, true);

相当于：update users set name = ‘changeName’ where age = 25; 

db.users.update({name: 'Lisi'}, {$inc: {age: 50}}, false, true);

相当于：update users set age = age + 50 where name = ‘Lisi’; 

db.users.update({name: 'Lisi'}, {$inc: {age: 50}, $set: {name: 'hoho'}}, false, true);

相当于：update users set age = age + 50, name = ‘hoho’ where name = ‘Lisi’;

```

* 删除

``` sh

db.users.remove({age: 132});

```

* 查询修改删除

``` sh

db.users.findAndModify({

    query: {age: {$gte: 25}},

    sort: {age: -1},

    update: {$set: {name: 'a2'}, $inc: {age: 2}},

    remove: true

});

 

db.runCommand({ findandmodify : "users",

    query: {age: {$gte: 25}},

    sort: {age: -1},

    update: {$set: {name: 'a2'}, $inc: {age: 2}},

    remove: true

});

```

## mongdb学习地址
[菜鸟教程学习mongdb](http://www.runoob.com/mongodb/mongodb-tutorial.html)
