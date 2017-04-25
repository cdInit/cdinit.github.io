---
layout: post
title:  "Mongodb中update操作"
categories: mongodb
tags: mongodb
author: Init
---

* content
{:toc}

MongoDB中文档存入数据库后用update方法更新，update方法有两个参数
update(args1,args2)
args1是指查询文档的条件；
args2是指对查询到的文档进行什么样的修改；





## 文档替换

``` sh
> db.test.find()
{ "_id" : ObjectId("58f6de33882977fedfd4a6af"), "name" : "zhang3" }
{ "_id" : ObjectId("58f6de58882977fedfd4a6b0"), "name" : "test", "id" : 111 }
{ "_id" : ObjectId("58f6de59882977fedfd4a6b1"), "name" : "zhang3", "id" : 111 }

> db.test.update({"name":"zhang3","id":111},{"id":222})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

> db.test.find()
{ "_id" : ObjectId("58f6de33882977fedfd4a6af"), "name" : "zhang3" }
{ "_id" : ObjectId("58f6de58882977fedfd4a6b0"), "name" : "test", "id" : 111 }
{ "_id" : ObjectId("58f6de59882977fedfd4a6b1"), "id" : 222 }


```

观察结果，文档替换将满足条件的行的记录修改成了 {"id":222} ，而不是只更新了一个字段

## 修改器替换（局部替换）

``` sh

> db.test.find()
{ "_id" : ObjectId("58f6de33882977fedfd4a6af"), "name" : "zhang3" }
{ "_id" : ObjectId("58f6de58882977fedfd4a6b0"), "name" : "test", "id" : 111 }
{ "_id" : ObjectId("58f6de59882977fedfd4a6b1"), "id" : 222, "name" : "lisi" }

> db.test.update({"id":222},{$set:{"name":"lisssi"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.test.find()
{ "_id" : ObjectId("58f6de33882977fedfd4a6af"), "name" : "zhang3" }
{ "_id" : ObjectId("58f6de58882977fedfd4a6b0"), "name" : "test", "id" : 111 }
{ "_id" : ObjectId("58f6de59882977fedfd4a6b1"), "id" : 222, "name" : "lisssi" }

```

使用$set更新某个字段。

## mongdb学习地址
[菜鸟教程学习mongdb-update](http://www.runoob.com/mongodb/mongodb-update.html)
