---
layout: post
title:  "spring常用注解驱动开发笔记"
categories: java
tags: java
author: Init
---

* content
{:toc}

spring注解驱动开发笔记

整篇参考 [Spring 注解驱动开发](https://www.cnblogs.com/Grand-Jon/tag/Spring%20%E6%B3%A8%E8%A7%A3%E9%A9%B1%E5%8A%A8%E5%BC%80%E5%8F%91/default.html?page=1)

学习视频 : 尚硅谷 - Spring 注解驱动开发 





### @Configuration & @Bean

``` java

1. 编写Person类 -> Person
...
2. 编写beans.xml -> 将Person注入到容器
/**传统方式
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="person" class="com.cdinit.springannotation.bean.Person">
        <property name="age" value="18"></property>
        <property name="name" value="zhangsan"></property>
    </bean>
</beans>
3. 从容器中获取bean
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("beans.xml");
Person p = (Person) applicationContext.getBean("person");
System.out.println(p);
*/

```

``` java

1.写一个配置类（配置类就等同于配置文件）
2.注册bean
/**注解方式
@Configuration //告诉spring这是一个配置类
public class MainConfig {

    @Bean(name = "person1") // 给容器中注册一个bean;类型为返回值的类型 id默认用方法名作为id
    public Person person(){
        return new Person("lisi",20);
    }
}

3.测试

ApplicationContext applicationContext = new AnnotationConfigApplicationContext(MainConfig.class);
Person p = (Person) applicationContext.getBean("person1");
//        Person p = applicationContext.getBean(Person.class);
System.out.println(p);
*/

```

### @DependsOn

``` java

// 依赖于某个java bean的创建而创建
@DependsOn({"xxxx"})

```

### @ComponentScan

#### 基本用法

``` java

// 包扫描
/**传统方式
xmlns:context="http://www.springframework.org/schema/context"

<context:component-scan base-package="com.cdinit"></context:component-scan>
*/

/**注解方式
在配置类中添加
@ComponentScan(value = "com.cdinit")
*/

@Configuration //告诉spring这是一个配置类
// excludeFilters 排除哪些组件
// includeFilters 只包含哪些组件
@ComponentScan(value = "com.cdinit" ,
    excludeFilters = {
        @ComponentScan.Filter(type = FilterType.ANNOTATION,classes = Service.class)
    },
    includeFilters = {
        @ComponentScan.Filter(type = FilterType.ANNOTATION,classes = RestController.class)
    },
    useDefaultFilters = false
)
public class MainConfig {

    @Bean(name = "person1") // 给容器中注册一个bean;类型为返回值的类型 id默认用方法名作为id
    public Person person(){
        return new Person("lisi",20);
    }
}

```


``` java

// 测试excludeFilters和includeFilters是否生效
public class MainTest {
    public static void main(String[] args) {
//        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("beans.xml");
//        Person p = (Person) applicationContext.getBean("person");
//        System.out.println(p);
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(MainConfig.class);
        for (String s : applicationContext.getBeanDefinitionNames()) {
            System.out.println(s);
        }
    }
}

```
#### @ComponentScan中Filter类型

- ANNOTATION 注解类型
- ASSIGNABLE_TYPE 指定的类型
- ASPECTJ 按照Aspectj的表达式
- REGEX 按照正则表达式
- CUSTOM 自定义规则

``` java
// CUSTOM规则
/**
1.编写MyTypeFilter 并实现 TypeFilter 接口
2.match方法中 实现自定义规则
*/
```

参考 [Spring注解（@Bean、@ComponentScan、自定义TypeFilter）](https://www.jianshu.com/p/279c4b9907e5)

### @Scope 组件作用域

``` java

@Bean(name = "person1") // 给容器中注册一个bean;类型为返回值的类型 id默认用方法名作为id
    // prototype:多实例 (ioc容器启动时候不会创建对象，获取对象的时候创建)
    // singleton:单实例 (ioc容器启动会创建对象)
    // request
    // session
    @Scope("prototype")
    public Person person(){
        System.out.println("add .....");
        return new Person("lisi",20);
    }
}

```

### @Lazy 懒加载

针对单实例的bean，默认在容器启动的时候创建，而懒加载，容器启动不创建对象，第一次获取bean的时候创建

### @Conditional

按照一定的条件进行判断，满足条件给容器中注册bean。spring，spring boot底层很常用

``` java
// 可以放在类上 也可以放在方法上
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Conditional {
    Class<? extends Condition>[] value();
}

```

参考 [Spring注解驱动开发@Conditional按照条件注册bean](https://blog.csdn.net/victoyr/article/details/88621254)

### @Import

给容器中注册组件:

1. 包扫描+组件标注注解（@Controller/@Service/@Repository/@Component）[自己写的类]

2. @Bean[导入的第三方包里面的组件]

3. @Import[快速给容器中导入一个组件]

  - 1. @Import(要导入到容器中的组件)；容器中就会自动注册这个组件，id默认是全类名

  - 2. ImportSelector:返回需要导入的组件的全类名数组；

  - 3. ImportBeanDefinitionRegistrar:手动注册bean到容器中

4. 使用Spring提供的 FactoryBean（工厂Bean）;

  - 1. 默认获取到的是工厂bean调用getObject创建的对象

  - 2. 要获取工厂Bean本身，我们需要给id前面加一个&

       &colorFactoryBean

参考 [SpringBoot给容器中注册组件的四种方法（简易版）](https://blog.csdn.net/qq_41075649/article/details/83345082)

### @PostConstruct & @PreDestroy

> @PostConstruct:初始化Bean(对象创建完成并赋值好）的时候调用  == 传统方式 init-method

> @PreDestroy:容器关闭的时候调用（单实例） == 传统方式destroy-method；

> 多实例容器不会管理bean，容器不会调用销毁

``` java

public class Person {
    private String name;
    private Integer age;

    ...

    @PostConstruct
    public void init(){
        System.out.println("init...");
    }

    @PreDestroy
    public void destroy(){
        System.out.println("destroy...");
    }
}

```

其他方法：

- 通过让Bean实现InitializingBean,DisposableBean

``` java

public class Person implements InitializingBean,DisposableBean {
    ...
}

```

- BeanPostProcessor: bean的后置处理器，在bean初始化前后进行一些处理工作
，
参考[Spring中的后置处理器BeanPostProcessor讲解](https://www.cnblogs.com/sishang/p/6576665.html)


### @PropertySource & @Value

> 使用@PropertySource读取配置文件（最好放在@Configuration标注的类上进行统一管理）

> 使用@Value("${}")取出配置文件的值

### @Autowired & @Qualifier & @Primary

``` java

@Autowired(required = false) // required=false 有则注入，没有就算了
@Qualifier("bookRepository") // 指定装配
private BookRepository bookRepository2;

```

``` java

/**
@Primary 让Spring进行自动装配的时候，默认选择需要装配的bean，也可以继续使用@Qualifier 指定需要装配的bean的名称
@Qualifier 的权重大于 @Primary，如果指定了@Qualifier 则 @Primary失效
*/
@Primary
@Bean(name = "person2")
public Person person2(){
    System.out.println("add .....");
    return new Person("wangwu",25);
}

```

参考[自动装配-@Autowired&@Qualifier&@Primary](https://www.cnblogs.com/Grand-Jon/p/10039340.html)

### @Resource & @Inject

``` java

@Resource(name = "pserson1")
private Person person;

```

参考

[自动装配-@Resource&@Inject](https://www.cnblogs.com/Grand-Jon/p/10040014.html)

[自动装配-方法、构造器位置的自动装配](https://www.cnblogs.com/Grand-Jon/p/10040022.html)

[自动装配-Aware注入Spring底层组件&原理](https://www.cnblogs.com/Grand-Jon/p/10040199.html)

### @Profile

> 自动装配-@Profile根据环境注册bean

#### 注册bean

``` java
// 设置3个bean
@Configuration
@ComponentScan(value = "com.cdinit")
public class MainConfig {
    @Bean("defaultPerson")
    @Profile("default")
    public Person personDefault(){
        Person p = new Person();
        p.setName("defaultPerson");
        p.setAge(00);
        return p;
    }

    @Bean("testPerson")
    @Profile("test")
    public Person personTest(){
        Person p = new Person();
        p.setName("testPerson");
        p.setAge(11);
        return p;
    }

    @Bean("devPerson")
    @Profile("dev")
    public Person personDev(){
        Person p = new Person();
        p.setName("devPerson");
        p.setAge(22);
        return p;
    }

    @Bean("releasePerson")
    @Profile("release")
    public Person personRelease(){
        Person p = new Person();
        p.setName("releasePerson");
        p.setAge(33);
        return p;
    }
}

```

#### 激活环境

``` java

1.在servlet上下文中进行配置（web.xml）
<context-param>  
  <param-name>spring.profiles.active</param-name>  
  <param-value>dev</param-value>  
</context-param>  

2.作为SpringMVC中的DispatcherServlet的初始化参数
<init-param>
    <param-name>spring.profiles.default</param-name>
    <param-value>dev</param-value>
</init-param>

3.spring-junit使用@ActiveProfiles进行激活

4.作为JNDI条目

5.作为环境变量

6.作为JVM的系统属性

7.application.properties配置文件

配置文件中添加：spring.profiles.active=dev

```

参考

[自动装配-@Profile环境搭建](https://www.cnblogs.com/Grand-Jon/p/10041688.html)

[自动装配-@Profile根据环境注册bean](https://www.cnblogs.com/Grand-Jon/p/10046323.html)

[Spring @Profile标签使用](https://blog.csdn.net/wufaliang003/article/details/77662556)

[详解Spring中的Profile](https://www.cnblogs.com/SummerinShire/p/6392242.html)



### @EnableAspectJAutoProxy & @Aspect

参考

[AOP-AOP功能测试](https://www.cnblogs.com/Grand-Jon/p/10047377.html)

[Spring Boot实践——AOP实现](https://www.cnblogs.com/onlymate/p/9605165.html)

---

先要加入依赖

``` xml

<!-- spring boot -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
</dependency>

```

或

``` xml

<!-- spring -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.8.13</version>
</dependency>

```

注意：所以bean必须由spring管理才能使用切面

### @EnableTransactionManagement & @Transactional

@Transactional 表示当前方法是一个事物方法

@EnableTransactionManagement 开启基于注解的事务功能

> spring需要把事务管理器加入到容器中

``` java

@Bean
public DataSource dataSource(){
    ...
    return dataSource;
}

@Bean
public PlatformTransactionManager transactionManager(){
    return new DataSourceTransactionManager(dataSource());
}

```

> spring boot可以直接使用

参考：[Spring boot学习笔记：SpringBoot之事务管理@Transactional](https://blog.csdn.net/eeeeasy/article/details/80834992)

### @EventListener

参考 [spring事件监听（eventListener)](https://www.jianshu.com/p/e2d257ce410d?from=timeline&isappinstalled=0)

---

web相关

### @WebServlet

### @HandlesTypes

参考：[使用Servlet3.0新特性进行web开发小demo](https://blog.csdn.net/zknxx/article/details/78398261)

### @EnableWebMvc

> springmvc定制，xml方式

``` xml
<mvc:annotation-driven/> 
```

> 添加注解，然后extends WebMvcConfigurerAdapter

``` java

@Configuration
@EnableWebMvc
public class WebConfig extends WebMvcConfigurerAdapter {

    // Override configuration methods...

}

```

详细参考：

[@EnableWebMvc](https://www.cnblogs.com/duanxz/p/4875153.html)

[SpringBoot 项目 @EnableWebMvc 注解应用总结](https://blog.csdn.net/testcs_dn/article/details/80249894)

[Spring注解@EnableWebMvc使用坑点解析](https://blog.csdn.net/zxc123e/article/details/84636521)

### servlet3.0-异步请求

> asyncSupported = true 

> AsyncContext asyncContext = req.startAsync();

``` java

@WebServlet(value = "/async", asyncSupported = true)
public class HelloAsyncServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        AsyncContext asyncContext = req.startAsync();
        System.out.println("主线程开始：" + Thread.currentThread() + "start..." + System.currentTimeMillis());
        // 开始异步处理
        asyncContext.start(() -> {
            try {
                System.out.println("子线程开始：" + Thread.currentThread() + "start..." + System.currentTimeMillis());
                sayHello();
                asyncContext.complete();
                // 获取响应
                ServletResponse response = asyncContext.getResponse();
                response.getWriter().write("异步执行完成");
                System.out.println("子线程结束：" + Thread.currentThread() + "end..." + System.currentTimeMillis());
            } catch (InterruptedException | IOException e) {
                e.printStackTrace();
            }
        });
        System.out.println("主线程结束：" + Thread.currentThread() + "end..." + System.currentTimeMillis());

    }

    public void sayHello() throws InterruptedException {
        System.out.println("sayHello运行：" + Thread.currentThread() + "processing..." + System.currentTimeMillis());
        Thread.sleep(3000);
    }
}

```

### springmvc-异步请求

``` java

@Controller
public class AsyncController {
    @RequestMapping("async01")
    @ResponseBody
    public Callable<String> async01() {

        System.out.println("主线程开始..." + Thread.currentThread() + "===》" + System.currentTimeMillis());
        Callable<String> callable = new Callable<String>() {
            public String call() throws Exception {
                System.out.println("子线程开始..." + Thread.currentThread() + "===》" + System.currentTimeMillis());
                Thread.sleep(3000);
                System.out.println("子线程结束..." + Thread.currentThread() + "===》" + System.currentTimeMillis());
                return "Callable<String> async01()";
            }
        };
        System.out.println("主线程结束..." + Thread.currentThread() + "===》" + System.currentTimeMillis());
        return callable;
    }
}

```

参考：

[springmvc-异步请求-返回Callable](https://www.cnblogs.com/Grand-Jon/p/10089387.html)

[springmvc-异步请求-返回DeferredResult队列](https://www.cnblogs.com/Grand-Jon/p/10089391.html)

