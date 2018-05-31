---
layout: post
title:  "weblogic容器下使用websocket小记"
categories: java
tags: java
author: Init
---

* content
{:toc}

> weblogic容器下使用websocket小记，包括需要的包，web.xml的配置（遇到的大坑），java中客户端的实现等





### pom文件

pom文件中添加需要的包

```
<!--websocket-->
<dependency>
    <groupId>javax.websocket</groupId>
    <artifactId>javax.websocket-api</artifactId>
    <scope>provided</scope>
    <version>1.1</version>
</dependency>
<!--websocket单元测试需要-->
<dependency>
    <groupId>org.glassfish.tyrus.bundles</groupId>
    <artifactId>tyrus-standalone-client</artifactId>
    <version>1.9</version>
</dependency>
```

### 修改web.xml

```
<!-- 解决websoket被拦截的问题 -->

<web-app version="3.0"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
         http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath*:config/spring/applicationContext-*.xml</param-value>
    </context-param>

    <context-param>
        <param-name>webAppRootKey</param-name>
        <param-value>Web.root</param-value>
    </context-param>

    <!--修复 websoket 30s 到期的问题-->
    <context-param>
        <param-name>weblogic.websocket.tyrus.session-max-idle-timeout</param-name>
        <param-value>0</param-value>
    </context-param>


    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <listener>
        <listener-class>org.springframework.web.util.WebAppRootListener</listener-class>
    </listener>


    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <!--修复 websoket 被拦截的问题-->
        <async-supported>true</async-supported>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath*:config/spring/springmvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
        <!--修复 websoket 被拦截的问题-->
        <async-supported>true</async-supported>
    </servlet>


    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>


    <servlet-mapping>
        <servlet-name>FileServlet</servlet-name>
        <!--<servlet-name>default</servlet-name>-->
        <url-pattern>*.html</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>FileServlet</servlet-name>
        <!--<servlet-name>default</servlet-name>-->
        <url-pattern>*.jpg</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>FileServlet</servlet-name>
        <!--<servlet-name>default</servlet-name>-->
        <url-pattern>*.png</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>FileServlet</servlet-name>
        <!--<servlet-name>default</servlet-name>-->
        <url-pattern>*.gif</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>FileServlet</servlet-name>
        <!--<servlet-name>default</servlet-name>-->
        <url-pattern>*.js</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>FileServlet</servlet-name>
        <!--<servlet-name>default</servlet-name>-->
        <url-pattern>*.css</url-pattern>
    </servlet-mapping>
</web-app>
```



### 编写NoticeWebsoket

```
import com.alibaba.fastjson.JSONObject;
import com.cdrcb.dmschedule.entity.dto.AuthDto;
import com.cdrcb.dmschedule.entity.po.Notice;
import com.cdrcb.dmschedule.entity.vo.PermissionParamsVo;
import com.cdrcb.dmschedule.manager.impl.AuthManagerImpl;
import com.cdrcb.dmschedule.service.impl.NoticeServiceImpl;
import org.springframework.web.context.ContextLoader;

import javax.websocket.*;
import javax.websocket.server.ServerEndpoint;
import java.io.IOException;
import java.util.*;

@ServerEndpoint("/echo/{fromSys}/{tokenId}")
public class NoticeWebsoket {
    
    private AuthManagerImpl authManager=(AuthManagerImpl) ContextLoader.getCurrentWebApplicationContext().getBean("authManager");
    private NoticeServiceImpl noticeService = (NoticeServiceImpl) ContextLoader.getCurrentWebApplicationContext().getBean("noticeService");

    // 所有用户
    static Map<String,Session> all = new HashMap<String,Session>();

    // 当用户连接时候的逻辑
    @OnOpen
    public void onOpen(Session session) throws IOException {
        List<Notice> responseListData = new ArrayList<Notice>();
        // 获取URL中的参数
        String fromSys = session.getPathParameters().get("fromSys");
        if(Objects.equals("0",fromSys)){
            all.put(tokenId,session);
        }
        ... // 其他逻辑
        // 将消息推送给该用户
        Map<String,Object> map = new HashMap<String,Object>();
        map.put("data",responseListData);
        map.put("noReadNum",noReadNum);
        session.getBasicRemote().sendText(response(map));
    }

    @OnClose
    public void onClose(Session session) {
        String tokenId = session.getPathParameters().get("tokenId");
        all.remove(tokenId);
        dataOutput.remove(tokenId);
        dataSource.remove(tokenId);
        System.out.println("webSocket session closed");
    }
    
    // 当给服务器发送消息时候的逻辑
    @OnMessage
    public void echo(Session session, String message) throws IOException {
        // message {type:1,msg:"xxxx通知xxxx"}
        JSONObject jsonObject = JSONObject.parseObject(message);
        String tokenId = session.getPathParameters().get("tokenId");
        String type = (String) jsonObject.get("type");
        Notice notice = new Notice();
        notice.setType(Integer.parseInt(type));
        notice.setDetail((String) jsonObject.get("msg"));
        notice.setStatus(1);
        notice.setActionUserName(authDto.getName());
        notice.setActionUserId(authDto.getUserId());
        notice.setCreateDate(new Date());
        // 将notice插入数据库
        try{
            noticeService.insertData(notice);
        }catch (Exception e){
            System.out.println(e);
        }

        List<Notice> responseListData = new ArrayList<Notice>();
        Map<String,Object> map = new HashMap<String,Object>();
        responseListData.add(notice);
        Integer noticeId = notice.getId();
        map.put("data",responseListData);

        // 给所有连接的用户推送消息
        while (iteartor.hasNext()) {
            all.get(iteartor.next()).getBasicRemote().sendText(response(map));
        }

    }

    @OnError
    public void onError(Throwable t) {
        t.printStackTrace();
    }


}

```

### java中给websoket发送消息

主要涉及到NoticeWebsoketClient的编写，原理就是java生成一个websoket客户端来向服务端发起消息


```
// NoticeWebsoketClient

package com.cdrcb.dmschedule.websocket;

import javax.websocket.*;

@ClientEndpoint
public class NoticeWebsoketClient {
 
    @OnOpen
    public void onOpen(Session session) {
    }
 
    @OnMessage
    public void onMessage(String message) {
        System.out.println("Client onMessage: " + message);
    }
 
    @OnClose
    public void onClose() {
    }
 
}


```

### 单元测试

客户端连接服务端的单元测试

```
import javax.websocket.Session;
@Test
public void testMessage(){
    WebSocketContainer container = ContainerProvider.getWebSocketContainer();
    Session session = null;
    try {
        session = container.connectToServer(NoticeWebsoketClient.class,
                new URI("ws://localhost:7001/edas/echo/1/token"));
        session.isOpen();
        session.getBasicRemote().sendText("{\"type\":\"1\",\"msg\":\"Client test!\"}");
    } catch (Exception e) {
        System.out.println(e);
    } finally {
        try {
            session.close();
        } catch (IOException e) {

        }
    }

}

```

### 网页中的应用

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
<head>
    <meta charset="UTF-8">
    <script src="https://cdn.bootcss.com/jquery/2.2.2/jquery.js"></script>

<body>
<textarea id="a" rows="50" cols="100"></textarea>
<br />
t:<input type="text" id="t" size="20" />
<br />
m:<input type="text" id="m" size="50" />
<input type="button" value="Send" onClick="send();" />
<br>
<input type="text" id="token" size="50" />
<input id="openBtn" type="button" value="Open..."/>

</body>
<script>

    var ws;
    function send() {
        console.log('send' + m.value)
        var send = {
            type: t.value,
            msg: m.value
        }
        ws.send(JSON.stringify(send));
    };

    $('#openBtn').click(function(){
        var token = $('#token').val();
        ws = new WebSocket("ws://localhost:7001/edas/echo/0/" + token);
        ws.onopen = function() {
            console.log("open");
        };
        ws.onmessage = function(evt) {
            var d = evt.data;
            console.log(evt);
            if (d != null) {
                $("#a").val($("#a").val() + d + "\n")
            }
        };
        ws.onclose = function(evt) {
            console.log("WebSocketClosed!");
        };
        ws.onerror = function(evt) {
            console.log("WebSocketError!");
        };
    })

    function open() {
        return

    }
</script>
</html>

```

### 在websoket中无法注入的问题

在配置文件中添加bean

```
<!--这两个bean写到这儿websoket才能取到-->
<bean id="authManager" class="com.cdrcb.dmschedule.manager.impl.AuthManagerImpl"></bean>
<bean id="noticeService" class="com.cdrcb.dmschedule.service.impl.NoticeServiceImpl"></bean>
```

### 其他

[官方文档参考](https://docs.oracle.com/javaee/7/api/javax/websocket/server/PathParam.html)

[在tomcat容器下还可以使用spring-websoket但是部署到weblogic下报错](https://www.cnblogs.com/3dianpomian/p/5902084.html)

[spring-websoket参考案例](https://blog.csdn.net/u014520745/article/details/62046396)

