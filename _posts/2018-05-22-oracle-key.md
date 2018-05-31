---
layout: post
title:  "mybatis注解方式返回自增主键-oracle"
categories: java
tags: java
author: Init
---

* content
{:toc}

> oracle数据库，使用mybatis注解方式返回自增主键






### 新建自增序列
``` sh
create sequence DM_MANAGEMENT_USER_SEQ
start with 1
increment by 1
minvalue 1 
nomaxvalue 

```

### UserMapper.java
``` java
// 使用@SelectKey
@SelectKey(statement="SELECT DM_MANAGEMENT_USER_SEQ.nextval AS ID FROM DUAL", keyProperty="managementUser.id", before=true, resultType=Integer.class)
@InsertProvider(type = UserProvider.class, method = "insertData")
boolean insertData(@Param("managementUser") ManagementUser managementUser);
```

### UserProvider
``` java
public String insertData(@Param("managementUser") ManagementUser managementUser) {
    SQL sql = new SQL();
    sql.INSERT_INTO("DM_MANAGEMENT_USER");
    // 之前用的这种方式插入自增主键
    // sql.VALUES("ID","DM_MANAGEMENT_USER_SEQ.nextval");
    
    // 现在用实体中的id
    sql.VALUES("ID","#{managementUser.id}");
    if (!Objects.equals(managementUser.getAccount(), "") && managementUser.getAccount() != null) {
        sql.VALUES("ACCOUNT","#{managementUser.account}");
    }

    if (!Objects.equals(managementUser.getPassword(), "") && managementUser.getPassword() != null) {
        sql.VALUES("PASSWORD","#{managementUser.password}");
    }

    return sql.toString();
}
```

### getId
``` java
在添加后只需要getId就能得到插入后用户的ID
managementUser.getId()
```

