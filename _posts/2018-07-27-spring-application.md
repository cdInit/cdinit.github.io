---
layout: post
title:  "获取ApplicationContext的工具类"
categories: java
tags: java
author: Init
---

* content
{:toc}

Spring获取ApplicationContext的工具类的几种方式





### （推荐）利用ApplicationContextAware

当一个类实现了ApplicationContextAware接口之后，通过此类可以直接获取spring配置文件中所有由Spring容器管理的bean对象。 

实现了这个接口之后，也要放到spring容器里面，否则无法使用

```
@Component("springApplicationContext")  
public class ApplicationContextHelper implements ApplicationContextAware {  
    private static ApplicationContext appCtx;  
  
    @Override  
    public void setApplicationContext(ApplicationContext applicationContext )  
            throws BeansException {  
        appCtx = applicationContext ;  
    }  
  
    public static Object getBean(String beanName) {  
        return appCtx.getBean(beanName);  
    }  
}  

```

[参考地址](http://hwei199.iteye.com/blog/2262049)

```
//或者使用@Configuration，其本质仍然是@Component

@Configuration
public class SpringContextUtils implements ApplicationContextAware {
    private static ApplicationContext applicationContext;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.applicationContext =applicationContext;    }

    public static Object getBean(Class c){
        return applicationContext.getBean(c);
    }
}


```

```
// 调用
public class ESBTestServiceImpl implements Service{

    private Logger logger = LoggerFactory.getLogger(ESBTestServiceImpl.class);

    private DataService dataService;

    public ESBTestServiceImpl() {
        this.dataService = (DataService) SpringContextUtils.getBean(DataService.class);
    }
...
    
}
```

### 手动创建ApplicationContext对象

```
public class ApplicationContextUtil {
    private static ApplicationContext context;
 
    static {
        context = new ClassPathXmlApplicationContext("applicationContext.xml");
    }
 
    public static ApplicationContext getApplicationContext() {
        return context;
    }
}

```

### 其他方式

> 其他方式1

在web环境中通过spring提供的工具类获取，需要ServletContext对象作为参数。然后才通过ApplicationContext对象获取bean。下面两个工具方式的区别是，前者在获取失败时返回null，后者抛出异常。另外，由于spring是容器的对象放在ServletContext中的，所以可以直接在ServletContext取出 WebApplicationContext 对象。

```
ApplicationContext context1 = WebApplicationContextUtils.getWebApplicationContext(ServletContext sc);
ApplicationContext context2 = WebApplicationContextUtils.getRequiredWebApplicationContext(ServletContext sc);


WebApplicationContext webApplicationContext = (WebApplicationContext) servletContext.getAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE);
```

> 其他方式2

工具类继承抽象类ApplicationObjectSupport，并在工具类上使用@Component交由spring管理。这样spring容器在启动的时候，会通过父类ApplicationObjectSupport中的setApplicationContext()方法将ApplicationContext对象设置进去。可以通过getApplicationContext()得到ApplicationContext对象。

> 其他方式3

工具类继承抽象类WebApplicationObjectSupport，查看源码可知WebApplicationObjectSupport是继承了ApplicationObjectSupport，所以获取ApplicationContext对象的方式和上面一样，也是使用getApplicationContext()方法。


[更多方式参考https://blog.csdn.net/lzx_longyou/article/details/56853570](https://blog.csdn.net/lzx_longyou/article/details/56853570)
