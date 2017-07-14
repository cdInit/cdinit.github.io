---
layout: post
title:  "mongodb update，根据条件修改一条或多条记录"
categories: mongodb
tags: mongodb
author: Init
---

* content
{:toc}

mongodb update 的用法2： 





## db.table_name.update(where,setNew,issert,multi );

db.table_name.update(where,setNew,issert,multi ); 

参数解释: 

where:类似于sql中的update 语句where后边的查询条件 

setNew:类似于sql中update语句中set后边的部分，也就是你要更新的部分 

upsert:如果要更新的那条记录没有找到，是否插入一条新纪录，默认为false不插入，true为插入 

multi :是否更新满足条件的多条的记录，false：只更新第一条，true:更新多条，默认为false 


示例： 


> db.users.update({"state":0},{$set:{"addUser":"","nickName":"","area":""}},false,true); 

相当于：  

> update users set addUser="",nickName="",area="" where state=0; 

更新表users中所有state=0的记录 

