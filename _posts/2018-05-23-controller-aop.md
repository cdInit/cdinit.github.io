---
layout: post
title:  "springMVC开启AOP注解切controller"
categories: java
tags: java
author: Init
---

* content
{:toc}

> springMVC开启AOP注解切controller





### 开启aop自动扫描
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">

    <context:component-scan base-package="com.cdrcb.dmschedule.controller"/>

    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/static"/>
        <property name="suffix" value=".html"/>
    </bean>

    <mvc:annotation-driven />

    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
    <!--拦截器-->
    <!--<mvc:interceptors>-->

        <!--<mvc:interceptor>-->
            <!--&lt;!&ndash; 用户认证拦截 &ndash;&gt;-->
            <!--<mvc:mapping path="/**" />-->
            <!--<bean class="com.cdrcb.dmschedule.interceptor.LoginInterceptor"></bean>-->
        <!--</mvc:interceptor>-->
        <!--<mvc:interceptor>-->
            <!--&lt;!&ndash; 授权拦截 &ndash;&gt;-->
            <!--<mvc:mapping path="/**" />-->
            <!--<mvc:exclude-mapping path="/auth/login" />-->
            <!--<mvc:exclude-mapping path="/auth/logout" />-->
            <!--<bean class="com.cdrcb.dmschedule.interceptor.PermissionInterceptor"></bean>-->
        <!--</mvc:interceptor>-->

    <!--</mvc:interceptors>-->

</beans>
```

一定要在springmvc.xml中配置，在spring.xml中配置是不生效的，因为是controller的切面

具体的原因参见 [Spring和SpringMVC父子容器关系初窥](https://www.cnblogs.com/hafiz/p/5875740.html)

### 实现切面
```
package com.cdrcb.dmschedule.aspect;

import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;
@Aspect
@Component
public class ManagementLogAspect {

    @Before("execution(* com.cdrcb.dmschedule.controller.PermisionListTest.*(..))")
    public void before(){
        System.out.println("aspect ... Before...");
    }
}


```