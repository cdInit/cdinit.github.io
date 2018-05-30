---
layout: post
title:  "Spring-@Value用法笔记"
categories: java
tags: java
author: Init
---

* content
{:toc}

> Spring-@Value用法笔记，spring支持使用@Value注解的方式来读取properties文件中的配置值






spring支持使用@Value注解的方式来读取properties文件中的配置值

## 两种使用方法

```
1、@Value("#{configProperties['key']}")

2、@Value("${key}"
```

## 配置

### @Value("#{configProperties['key']}")的使用

#### 配置文件：

```
配置方法1：  
<bean id="configProperties" class="org.springframework.beans.factory.config.PropertiesFactoryBean">  
    <property name="locations">  
        <list>  
            <value>classpath:value.properties</value>  
        </list>  
    </property>  
</bean>  

配置方法2：  
<util:properties id="configProperties" location="classpath:value.properties"></util:properties>  
注：配置1和配置2等价，这种方法需要util标签，要引入util的xsd：
http://www.springframework.org/schema/util

http://www.springframework.org/schema/util/spring-util-3.0.xsd"
```

```
//value.properties
key=1  
ValueDemo.java
```

```
@Component  
public class ValueDemo {  
    @Value("#{configProperties['key']}")  
    private String value;  
  
    public String getValue() {  
        return value;  
    }  
}  
```
### @Value("${key}")使用

#### 配置文件

```
<bean id="configProperties" class="org.springframework.beans.factory.config.PropertiesFactoryBean">  
    <property name="locations">  
        <list>  
            <value>classpath:value.properties</value>  
        </list>  
    </property>  
</bean>  

<bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PreferencesPlaceholderConfigurer">  
    <property name="properties" ref="configProperties"/>  
</bean>  
```

#### 直接指定配置文件，完整的配置：
```
<bean id="appProperty"  
          class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">  
    <property name="locations">  
        <array>  
            <value>classpath:value.properties</value>  
        </array>  
    </property>  
</bean>  
```

```
// ValueDemo.java
@Component  
public class ValueDemo {  
    @Value("${key}")  
    private String value; 
    public String getValue() {  
        return value;  
    }  
}  
```

### 使用自动扫描

```
<context:property-placeholder location="classpath:config/jdbc.properties"/>

//如果有多个配置文件,多个文件之间以“,”分隔，如下：

<context:property-placeholderlocation="classpath:db.properties,classpath:monitor.properties" />
```

然后使用 @Value 获取属性值

### 使用@PropertySource

```
@Component
@PropertySource(value = {"classpath:test1.properties", "classpath:test2.properties"})
public class Configs {

    @Value("${test1.test}")
    public String apiKeyId;

    @Value("${test2.test}")
    public String secretApiKey;

    public String getApiKeyId() {
        return apiKeyId;
    }

    public String getSecretApiKey() {
        return secretApiKey;
    }
}

```

这里需要注意这个Bean一定要给spring管理，否则即使加了@PropertySource注解，仍然无法获取到配置文件中的值。

### 给静态变量赋值

```

@Component
@PropertySource("classpath:websoket.properties")
public class NoticeWebsoketUtils {
    private static String webSocketServer;
    @Value(value = "${websocketServer}")
    public void setWebSocketServer(String url) {
        NoticeWebsoketUtils.webSocketServer = url;
    }
    public static void sendMsgToWebSocketServer(String token,String msg){
        // {"type":"1","msg":"Client test!"}
        WebSocketContainer container = ContainerProvider.getWebSocketContainer();
        Session session = null;
        try {
            session = container.connectToServer(NoticeWebsoketClient.class,
                    new URI(webSocketServer + token));
            session.getBasicRemote().sendText(msg);
        } catch (Exception e) {
            System.out.println(e);
        } finally {
            try {
                session.close();
            } catch (IOException e) {

            }
        }
    }
}
```