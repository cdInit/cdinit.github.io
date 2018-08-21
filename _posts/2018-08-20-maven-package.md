---
layout: post
title:  "通过maven配置不同的资源文件打包"
categories: java
tags: java
author: Init
---

* content
{:toc}

通过maven配置不同的资源文件打包






### POM文件

```

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>...</groupId>
    <artifactId>edas</artifactId>
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>
    <name>CDRCB DataMarket Schedule Maven WebApp</name>
    <url>http://maven.apache.org</url>

    <properties>
        <spring.version>4.2.3.RELEASE</spring.version>
    </properties>


    <dependencies>
        ...
    </dependencies>

    <build>
        <finalName>edas</finalName>
        <plugins>
            <!--mybatis 逆向工程插件-->
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.2</version>
                <configuration>
                    <verbose>true</verbose>
                    <overwrite>true</overwrite>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>

        <resources>
            <resource>
                <!--把相对应的独特资源(dev,sit,uat)声明-->
                <directory>src/main/resources.${deploy.type}</directory>
                <excludes>
                    <exclude>*.jsp</exclude>
                </excludes>
            </resource>
            <resource>
                <!--声明公共资源-->
                <directory>src/main/resources</directory>
            </resource>
        </resources>
    </build>

    <!--分别设置开发，银行环境-->
    <profiles>
        <profile>
            <id>dev</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <deploy.type>dev</deploy.type>
            </properties>
        </profile>
        <profile>
            <id>uat</id>
            <properties>
                <deploy.type>uat</deploy.type>
            </properties>
        </profile>
        <profile>
            <id>sit</id>
            <properties>
                <deploy.type>sit</deploy.type>
            </properties>
        </profile>
    </profiles>

</project>

```

### 目录结构

```

目录结构：
-- src
   -- main
   -- java
   -- resource
   -- resource.dev
   -- resource.sit
   -- resource.uat
   
```

### 利用idea打包

maven projects 中选择对应的环境，clean --> package