---
layout: post
title:  "spring中init-method和destroy-method配置"
categories: java
tags: java
author: Init
---

* content
{:toc}

spring中init-method和destroy-method配置






### XML配置方式

``` java
//Person类

public class Person {
	private int i = 0;
 
	public Person(){
		System.out.println("实例化一个对象");
	}
	
	public void init(){
		System.out.println("调用初始化方法....");
	}
	
	public void destory222(){
		System.out.println("调用销毁化方法....");
	}
}

``` 

``` java

applicationContext.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://www.springframework.org/schema/beans 
							http://www.springframework.org/schema/beans/spring-beans-2.5.xsd">
	<!-- 配置初始化方法和销毁方法，但是如果要销毁方法生效scope="singleton" -->				
	<bean id="person" class="com.xxc.initAndDestory.domain.Person" scope="singleton" lazy-init="false" init-method="init" destroy-method="destory"></bean>
</beans>

```

``` java

测试类：
public class Test {
	public static void main(String[] args) {
		//如果要调用销毁方法必须用子类来声明,而不是ApplicationContext,
		//因为ApplicationContext没有close()
		ClassPathXmlApplicationContext ac = new ClassPathXmlApplicationContext("com/xxc/initAndDestory/applicationContext.xml");
		Person p1 = (Person)ac.getBean("person");
		Person p2 = (Person)ac.getBean("person");
		ac.close();
	}
}

```


### 注解方式

如果用注解方式配置：

``` java

applicationContext.xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	   xmlns:context="http://www.springframework.org/schema/context" 
	   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	   xsi:schemaLocation="http://www.springframework.org/schema/beans   
                            http://www.springframework.org/schema/beans/spring-beans-2.5.xsd  
                            http://www.springframework.org/schema/context   
                            http://www.springframework.org/schema/context/spring-context-2.5.xsd">  
							
	<context:annotation-config/> 
							
	<!-- scope默认是 prototype:getBean()一次创建一个实例-->
	<bean id="person" class="com.xxc.initAndDestory.domain.Person"></bean>
</beans>

```

``` java

//Person类
public class Person {
	private int i = 0;
 
	public Person(){
		System.out.println("实例化一个对象");
	}
	@PostConstruct //初始化方法的注解方式  等同与init-method=init
	public void init(){
		System.out.println("调用初始化方法....");
	}
	@PreDestroy	//销毁方法的注解方式  等同于destory-method=destory222
	public void destory(){
		System.out.println("调用销毁化方法....");
	}
}

```

也可以在类中直接注解:

``` java

@Component
public class GetBatchDataTask {
    @PostConstruct
	public void init(){
		System.out.println("调用初始化方法....");
	}
	
	@PreDestroy
	public void destory(){
		System.out.println("调用销毁化方法....");
	}
	
	public void test(){
	    System.out.println(".....");
	}
}

```

### 参考

> http://blog.csdn.net/world_java/article/details/10171383

> https://yq.aliyun.com/articles/66657