---
layout: post
title:  "dubbo简单demo搭建"
categories: java
tags: java
author: Init
---

* content
{:toc}

> 关于dubbo的介绍网上已经有很多了，这里把自己搭建的dubbo的demo记录一下而已





### demo项目结构

> 使用maven管理项目

结构 | 类型
---|---
dubbo-parent |pom
dubbo-dao | jar
dubbo-interface | jar
dubbo-pojo | jar
dubbo-service | war
dubbo-web | war

### 搭建zookeeper服务器

在linux服务器搭建zookeeper服务器

### 注意事项

1. 注意各模块之间的依赖
2. 注意@Service导入的包应该是dubbo的包
3. 配置文件：web配置springmvc.xml，在web.xml中引入；service配置spring和mybatis，在web.xml中引入。
4. 项目中有些多余的包没有删除
5. 如果把dao层的配置文件放在dubbo-dao里面，项目不能注入dao层的bean，所有放在了service层
6. [dubbo-admin](https://github.com/apache/incubator-dubbo-ops) 可以实时查看服务提供方和消费者

PS:7.11

今天看见classpath和classpath*的区别，[Spring配置中的"classpath:"与"classpath*:"的区别研究（转）](https://www.cnblogs.com/EasonJim/p/6709314.html)

```
程序部署到tomcat后，src目录下的配置文件会和class文件一样，自动copy到应用的WEB-INF/classes目录下；classpath:

与classpath*:的区别在于，前者只会从第一个classpath中加载，而 后者会从所有的classpath中加载。

如果要加载的资源，不在当前ClassLoader的路径里，那么用classpath:前缀是找不到的，这种情况下就需要使用classpath*:前缀。

```

也就是说dao层的配置文件放在dubbo-dao里面，只需要在service的web.xml的classpath修改为classpath*就行了，那么在启动

dubbo-service的时候就会从dubbo-dao.jar里面把配置文件加载出来了，也就不会出现项目不能注入dao层的bean的问题了。

### demo地址

[https://github.com/cdInit/dubbo-demo](https://github.com/cdInit/dubbo-demo)


