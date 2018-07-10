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

### demo地址

[https://github.com/cdInit/dubbo-demo](https://github.com/cdInit/dubbo-demo)


