---
layout: post
title:  "weblogic配置Log4j不生效的问题"
categories: java
tags: java
author: Init
---

* content
{:toc}

weblogic配置Log4j不生效的问题

weblogic自带slf4j的模块，与应用中的slf4j冲突，按照官网 https://community.oracle.com/thread/3525130?start=0&tstart=0 的做法，在WEB-INF下放一个名为weblogic.xml的文件





### weblogic.xml

```

<?xml version="1.0" encoding="UTF-8"?>
<weblogic-web-app xmlns="http://www.bea.com/ns/weblogic/90" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                  xsi:schemaLocation="http://www.bea.com/ns/weblogic/90 http://www.bea.com/ns/weblogic/90/weblogic-web-app.xsd">

    <session-descriptor>
        <cookie-name>JSESSIONID</cookie-name>
    </session-descriptor>

    <context-root>/edas</context-root>

    <jsp-descriptor>
        <keepgenerated>true</keepgenerated>
        <page-check-seconds>0</page-check-seconds>
        <precompile>true</precompile>
        <precompile-continue>true</precompile-continue>
    </jsp-descriptor>

    <virtual-directory-mapping>
        <local-path>/static/</local-path>
        <url-pattern>/*</url-pattern>
    </virtual-directory-mapping>

    <container-descriptor>
        <optimistic-serialization>true</optimistic-serialization>
        <resource-reload-check-secs>0</resource-reload-check-secs>
        <prefer-web-inf-classes>false</prefer-web-inf-classes>
        <show-archived-real-path-enabled>true</show-archived-real-path-enabled>
        <prefer-application-packages>
            <package-name>org.slf4j</package-name>
        </prefer-application-packages>
    </container-descriptor>
</weblogic-web-app>
   
```

### log4j.properties

```
#### \u6240\u6709\u65E5\u5FD7 ####
log4j.rootLogger=info, stdout, infofile, errorfile

#### stdout 控制器####
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout.ConversionPattern=[%d] [%p] [%c:%L] : %m%n

#### debug ####
#log4j.appender.debug=org.apache.log4j.DailyRollingFileAppender
#log4j.appender.debug.Threshold=debug
#log4j.appender.debug.File=${user.home}/logs/edas/debug.log
#log4j.appender.debug.Append=true
#log4j.appender.debug.DatePattern='-'yyyy-MM-dd'.log'
#log4j.appender.debug.filter.debugFilter=org.apache.log4j.varia.LevelRangeFilter
#log4j.appender.debug.filter.debugFilter.LevelMin=DEBUG
#log4j.appender.debug.filter.debugFilter.LevelMax=DEBUG
#log4j.appender.debug.layout=org.apache.log4j.PatternLayout
#log4j.appender.debug.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %logger{50} %-5level %msg%n

#### info ####
log4j.logger.infofile=info
log4j.appender.infofile=org.apache.log4j.DailyRollingFileAppender
log4j.appender.infofile.Threshold=INFO
log4j.appender.infofile.File=${user.home}/logs/edas_logs/info.log
log4j.appender.infofile.Append=true
log4j.appender.infofile.DatePattern='-'yyyy-MM-dd'.log'
log4j.appender.infofile.layout=org.apache.log4j.PatternLayout
log4j.appender.infofile.layout.ConversionPattern=[%d] [%p] [%c:%L] : %m%n
log4j.appender.infofile.encoding=UTF-8

#### error ####
log4j.logger.errorfile=error
log4j.appender.errorfile=org.apache.log4j.DailyRollingFileAppender
log4j.appender.errorfile.Threshold=ERROR
log4j.appender.errorfile.File=${user.home}/logs/edas_logs/error.log
log4j.appender.errorfile.Append=true
log4j.appender.errorfile.DatePattern='-'yyyy-MM-dd'.log'
log4j.appender.errorfile.layout=org.apache.log4j.PatternLayout
log4j.appender.errorfile.layout.ConversionPattern=[%d] [%p] [%c:%L] : %m%n
log4j.appender.errorfile.encoding=UTF-8

## 设置特殊文件的日志
log4j.logger.com.cdrcb.dmschedule.service.impl.AfterLoanJobServiceImpl=info, after_loan
log4j.logger.com.cdrcb.dmschedule.service.impl.AfterLoanQueryApiServiceImpl=info, after_loan
log4j.logger.com.cdrcb.dmschedule.task.GetBatchDataTask=info, after_loan
log4j.logger.com.cdrcb.dmschedule.service.impl.AnRongCreditServiceImpl=info, after_loan
log4j.logger.com.cdrcb.dmschedule.controller.AnRongCreditController=info, after_loan


#### after_loan ####
log4j.logger.after_loan=info
log4j.appender.after_loan=org.apache.log4j.DailyRollingFileAppender
log4j.appender.after_loan.Threshold=INFO
log4j.appender.after_loan.File=${user.home}/logs/edas_logs/after_loan_info.log
log4j.appender.after_loan.Append=true
log4j.appender.after_loan.DatePattern='-'yyyy-MM-dd'.log'
log4j.appender.after_loan.layout=org.apache.log4j.PatternLayout
log4j.appender.after_loan.layout.ConversionPattern=[%d] [%p] [%c:%L] : %m%n
log4j.appender.after_loan.encoding=UTF-8

```

### 其他

如果还不行，尝试在web.xml中添加 

```

<context-param>
    <param-name>log4jConfigLocation</param-name>
    <param-value>classpath:log4j.properties</param-value>
</context-param>
<listener>
    <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
</listener>

```