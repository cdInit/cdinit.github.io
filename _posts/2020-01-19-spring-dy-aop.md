---
layout: post
title:  "springboot动态添加aop切面"
categories: java
tags: java
author: Init
---

* content
{:toc}

需求：在不停止服务的情况下，通过上传一个jar包然后捕获某方法的异常进行处理

思路：

使用springaop实现

- 定义一个切入点为service包下面的所以方法

- 将jar文件加载到classLoader

- 动态添加切入点到指定的方法







至于为什么要定义一个切入点到service包下面的所以方法，感兴趣的可以研究一下springAop的源码，里面有个postProcessBeforeInstantiation方法，会返回代理对象，如果没有则不会返回代理对象。
当然还有一种思路，就是在动态添加切入点的时候把spring容器中的对象替换成自己的代理对象（没有实验过，在非单例模式的时候有问题，这里不深入研究）。

引入aop的starter：
```

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>

```


### 第一步：

```

@Aspect
@Configuration
public class TestAop {
    /*
     * 定义一个大的切入点
     */
    @Pointcut("execution(* com.cdinit.spring.demo..*(..))")
    public void initAllAop(){}
    @Before("initAllAop()")
    public void initAllAop1(){
    }
}

```

### 第二步：

```

//核心逻辑 实例化jar包里面的类
private Advice buildAdvice(PluginConfig config) throws Exception {

		if (adviceCache.containsKey(config.getClassName())) {
			return adviceCache.get(config.getClassName());
		}

		File jarFile = new File(config.getLocalUrl());

		// 将本地jar 文件加载至 classLoader
		URLClassLoader loader = (URLClassLoader) getClass().getClassLoader();
		URL targetUrl = jarFile.toURI().toURL();
		boolean isLoader = false;
		for (URL url : loader.getURLs()) {
			if (url.equals(targetUrl)) {
				isLoader = true;
				break;
			}
		}
		if (!isLoader) {
			Method add = URLClassLoader.class.getDeclaredMethod("addURL", new Class[] { URL.class });
			add.setAccessible(true);
			add.invoke(loader, targetUrl);
		}
		// Advice 实例化
		Class<?> adviceClass = loader.loadClass(config.getClassName()); //上传的jar文件的类
		if (!Advice.class.isAssignableFrom(adviceClass)) {
			throw new RuntimeException("无法实例化非" + Advice.class.getName() + "的实例");
		}
		adviceCache.put(adviceClass.getName(), (Advice) adviceClass.newInstance());
		return adviceCache.get(adviceClass.getName());
	}

```


```

//核心逻辑 根据切入点动态切入
	private ApplicationContext applicationContext; // 应用上下文
	private Map<String, Advice> adviceCache = new HashMap<>();

	private PluginConfig pluginConfig = new PluginConfig()
			.setId("1")
			.setName("test")
			.setClassName("CountingBeforeAdvice")
			.setLocalUrl("E:\\aop-fix-zero\\target\\aop-fix-zero-1.0-SNAPSHOT.jar")
			.setActive("true")
//            .setExp("execution(* *.test(..))") // 加入切入点到切面
            .setExp("execution(* test(..))")
			.setVersion("1.0");

	public void activePlugin(String pluginId) {

		PluginConfig config = pluginConfig; // TODO 这里应该从数据库里面查询config的配置

		for (String name : applicationContext.getBeanDefinitionNames()) {
			Object bean = applicationContext.getBean(name);
			if (bean == this)
				continue;
			if (!(bean instanceof Advised)) // 如果bean不是Advised类型则跳过
				continue;
			if (findAdvice(config.getClassName(), (Advised) bean) != null) // 如果bean已经注册了Advised则跳过
				continue;

			Advice advice = null;
			try {
				advice = buildAdvice(config); //初始化 Plugin Advice 实例化
                // 包一层 advisor
                AspectJExpressionPointcutAdvisor advisor = new AspectJExpressionPointcutAdvisor();
                advisor.setExpression(config.getExp());
                advisor.setAdvice(advice);
				((Advised) bean).addAdvisor(advisor);
			} catch (Exception e) {
				throw new RuntimeException("安装失败", e);
			}
		}
	}

	private Advice findAdvice(String className, Advised advised) {
		for (Advisor a : advised.getAdvisors()) {
			if (a.getAdvice().getClass().getName().equals(className)) {
				return a.getAdvice();
			}
		}
		return null;
	}


```

### jar包怎么写？只需要实现对应的切面方法就行了

```

public class ServerLogPlugin implements MethodBeforeAdvice {

	public void before(Method method, Object[] args, Object target) throws Throwable {
		String result = String.format("%s.%s() 参数:%s", method.getDeclaringClass().getName(), method.getName(),
				Arrays.toString(args));
		System.out.println(result);
	}

}


```

--

通常有方法前拦截，方法后拦截，以及异常拦截。通过在这些拦截中编写自己的业务处理，可以达到特定的需求。

- MethodBeforeAdvice

- MethodBeforeAdvice

- ThrowsAdvice

--

execution表达式

```

匹配所有类public方法  execution(public * *(..))
匹配指定包下所有类方法 execution(* com.baidu.dao.*(..)) 不包含子包
execution(* com.baidu.dao..*(..))  ..*表示包、子孙包下所有类
匹配指定类所有方法 execution(* com.baidu.service.UserService.*(..))
匹配实现特定接口所有类方法
    execution(* com.baidu.dao.GenericDAO+.*(..))
匹配所有save开头的方法 execution(* save*(..))

```