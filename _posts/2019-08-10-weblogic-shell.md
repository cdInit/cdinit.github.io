---
layout: post
title:  "weblogic-shell更新应用简单版"
categories: java
tags: java
author: Init
---

* content
{:toc}

这个也就是重新部署应用,
首先保证你的weblogic和你的应用是启动状态，并且已经释放配置





``` sh
export CLASSPATH=/usr/java/1.8.0_111/lib/tools.jar:/home/ssyj/Oracle/Middleware/Oracle_Home/wlserver/server/lib/weblogic_sp.jar:/home/ssyj/Oracle/Middleware/Oracle_Home/wlserver/server/lib/weblogic.jar:/home/ssyj/Oracle/Middleware/Oracle_Home/wlserver/server/lib/webservices.jar:/home/ssyj/Oracle/Middleware/Oracle_Home/wlserver/../oracle_common/modules/org.apache.ant_1.7.1/lib/ant-all.jar:/home/ssyj/Oracle/Middleware/Oracle_Home/wlserver/../oracle_common/modules/net.sf.antcontrib_1.1.0.0_1-0b2/lib/ant-contrib.jar:/home/ssyj/Oracle/Middleware/Oracle_Home/wlserver/modules/features/oracle.wls.common.nodemanager_1.0.0.0.jar:.:/usr/java/1.8.0_111/lib/dt.jar:/usr/java/1.8.0_111/lib/tools.jar

cd /home/ssyj/Oracle/Middleware/Oracle_Home/wlserver/server/lib

java weblogic.Deployer -adminurl t3://192.168.134.166:7001 -user weblogic -password weblogic123  -name session -targets sysmanm  -redeploy
```

参考：
# [使用Linux脚本更新Weblogic部署的应用程序](https://blog.csdn.net/yulei_qq/article/details/53318355)
# [jiaozhu](https://github.com/jiaozhu)/**[autoDeploy](https://github.com/jiaozhu/autoDeploy)**
