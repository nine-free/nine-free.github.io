---
layout:     post
title:      spring系列之spring启动过程
subtitle:   
date:       2019-06-25
author:     nine-free
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - spring
---

## 启动spring容器
简单
```
  //spring容器
  ClassPathXmlApplicationContext classPathXmlApplicationContext =
                new ClassPathXmlApplicationContext("spring/applicationContext.xml");
        UserService userService1 = classPathXmlApplicationContext.getBean(UserService.class);
```
指定环境，使用占位符替换配置文件路径
```
        System.setProperty("env", "test");
        ClassPathXmlApplicationContext classPathXmlApplicationContext =
                new ClassPathXmlApplicationContext("spring/applicationContext-${env}.xml");
        UserService userService = (UserService) classPathXmlApplicationContext.getBean("userService");
```

## 启动流程
构造方法如下
```
public ClassPathXmlApplicationContext(String[] configLocations, boolean refresh, ApplicationContext parent)
			throws BeansException {
		super(parent);
		setConfigLocations(configLocations);//1、设置启动的配置文件路径
		if (refresh) {
			refresh();//主要流程
		}
	}
```
### 1、设置启动的配置文件路径 
setConfigLocations（）这个方法包含了启动环境初始化（服务器jdk路径，服务器上的一些System.Properties），占位符处理也在这部分，
根据路径是classpath路径还是URL路径处理也是在这部分处理。
### 2、refresh()主要启动流程
```
        synchronized (this.startupShutdownMonitor) {
			// 1、准备上下文刷新
			prepareRefresh();

			// 2、beanfactory开始
			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

			// 3、准备beanfactory
			prepareBeanFactory(beanFactory);

			try {
				// 4、允许子类对beanfactory后处理
				postProcessBeanFactory(beanFactory);

				// 5、后置处理器处理bean
				invokeBeanFactoryPostProcessors(beanFactory);

				// 6、注册bean拦截器
				registerBeanPostProcessors(beanFactory);

				// 7、初始化上下文的消息源
				initMessageSource();

				// 8、初始化上下文的多播器
				initApplicationEventMulticaster();

				// 9、实例化上下文子类中特殊的bean
				onRefresh();

				// 10、注册监听器
				registerListeners();

				// 11、实例化剩下的单例
				finishBeanFactoryInitialization(beanFactory);

				// 12、完成刷新后置处理
				finishRefresh();
			}

			catch (BeansException ex) {
				logger.warn("Exception encountered during context initialization - cancelling refresh attempt", ex);

				// Destroy already created singletons to avoid dangling resources.
				destroyBeans();

				// Reset 'active' flag.
				cancelRefresh(ex);

				// Propagate exception to caller.
				throw ex;
			}

			finally {
				// Reset common introspection caches in Spring's core, since we
				// might not ever need metadata for singleton beans anymore...
				resetCommonCaches();
			}
		}
```
#### prepareRefresh() 
记录开始时间，active标记，处理propertiesSource,现有类没有initPropertySources的处理，校验环境必须的属性等操作
initPropertySources()可以在子类中实现新的占位符属性源 默认的propertySources是在setConfigLocations这个方法处理的，
并且默认只有System.getProperties()和System.getenv()
```
        this.startupDate = System.currentTimeMillis();
		this.active.set(true);

		if (logger.isInfoEnabled()) {
			logger.info("Refreshing " + this);
		}

		// Initialize any placeholder property sources in the context environment
		initPropertySources();

		// Validate that all properties marked as required are resolvable
		// see ConfigurablePropertyResolver#setRequiredProperties
		getEnvironment().validateRequiredProperties();

		// Allow for the collection of early ApplicationEvents,
		// to be published once the multicaster is available...
		this.earlyApplicationEvents = new LinkedHashSet<ApplicationEvent>();
```
#### obtainFreshBeanFactory()
```
	protected final void refreshBeanFactory() throws BeansException {
		if (hasBeanFactory()) {
			destroyBeans();
			closeBeanFactory();
		}
		try {
			DefaultListableBeanFactory beanFactory = createBeanFactory();
			beanFactory.setSerializationId(getId());
			customizeBeanFactory(beanFactory);
			loadBeanDefinitions(beanFactory);
			synchronized (this.beanFactoryMonitor) {
				this.beanFactory = beanFactory;
			}
		}
		catch (IOException ex) {
			throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);
		}
	}
```
1、createBeanFactory()该方法内部是用给定的父项创建一个新的DefaultListableBeanFactory
2、customizeBeanFactory()设置是否允许bean相同名称不同定义、是否自动尝试解决bean之间的循环引用
3、


