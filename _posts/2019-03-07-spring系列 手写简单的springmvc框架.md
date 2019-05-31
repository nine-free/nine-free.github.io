---
layout:     post
title:      spring系列之手写简单的springmvc框架
subtitle:   
date:       2019-03-07
author:     nine-free
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - spring
    - spring mvc
---

## spring mvc核心原理 
直接上图（不知道哪个大神的运行流程图，盗来用一下）
![image](http://soft1010.top/img/spring-mvc-1.jpg)
简单聊两句：核心原理就在于DispatcherServlet继承HttpServlet,这就相当于一个大的HttpServlet。
跟配置HttpServlet一样在web.xml配置DispatcherServlet。然后handlerMapping这个映射器根据请求uri映射到Controller.Method
当然还有好多其他组件但是这是最核心的原理。

## 手撸代码
#### 基本注解
```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyController {

    String value() default "";
}
```
```
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyRequestMapping {

    String value() default "";
}
```
```
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyRequestParam {
    String value();
}
```
#### MyDispatcherServlet中央控制器
get post 方法到指定的doDispatch
```
 @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        try {
            //处理请求
            doDispatch(req, resp);
        } catch (Exception e) {
            resp.getWriter().write("500!! Server Exception");
        }
    }
```
初始化组件
```
    @Override
    public void init(ServletConfig config) throws ServletException {
        //1.加载配置文件
        doLoadConfig(config.getInitParameter("contextConfigLocation"));

        //2.初始化所有相关联的类,扫描用户设定的包下面所有的类
        doScanner(properties.getProperty("scanPackage"));

        //3.拿到扫描到的类,通过反射机制,实例化,并且放到ioc容器中(k-v  beanName-bean) beanName默认是首字母小写
        doInstance();

        //4.初始化HandlerMapping(将url和method对应上)
        initHandlerMapping();
    }
```
#### 核心逻辑处理方法
```
  private void doDispatch(HttpServletRequest req, HttpServletResponse resp) throws Exception {
        if (handlerMapping.isEmpty()) {
            return;
        }

        String url = req.getRequestURI();
        String contextPath = req.getContextPath();

        url = url.replace(contextPath, "").replaceAll("/+", "/");

        if (!this.handlerMapping.containsKey(url)) {
            resp.getWriter().write("404 NOT FOUND!");
            return;
        }

        Method method = this.handlerMapping.get(url);

        //获取方法的参数列表
        Class<?>[] parameterTypes = method.getParameterTypes();

        //获取请求的参数
        Map<String, String[]> parameterMap = req.getParameterMap();

        //保存参数值
        Object[] paramValues = new Object[parameterTypes.length];

        //方法的参数列表
        for (int i = 0; i < parameterTypes.length; i++) {
            //根据参数名称，做某些处理
            String requestParam = parameterTypes[i].getSimpleName();


            if (requestParam.equals("HttpServletRequest")) {
                //参数类型已明确，这边强转类型
                paramValues[i] = req;
                continue;
            }
            if (requestParam.equals("HttpServletResponse")) {
                paramValues[i] = resp;
                continue;
            }
            if (requestParam.equals("String")) {
                for (Map.Entry<String, String[]> param : parameterMap.entrySet()) {
                    String value = Arrays.toString(param.getValue()).replaceAll("\\[|\\]", "").replaceAll(",\\s", ",");
                    paramValues[i] = value;
                }
            }
        }
        //利用反射机制来调用
        try {
            method.invoke(this.controllerMap.get(url), paramValues);//第一个参数是method所对应的实例 在ioc容器中
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```
#### handlerMapping初始化
```
 private void initHandlerMapping() {
        if (ioc.isEmpty()) {
            return;
        }
        try {
            for (Map.Entry<String, Object> entry : ioc.entrySet()) {
                Class<? extends Object> clazz = entry.getValue().getClass();
                if (!clazz.isAnnotationPresent(MyController.class)) {
                    continue;
                }

                //拼url时,是controller头的url拼上方法上的url
                String baseUrl = "";
                if (clazz.isAnnotationPresent(MyRequestMapping.class)) {
                    MyRequestMapping annotation = clazz.getAnnotation(MyRequestMapping.class);
                    baseUrl = annotation.value();
                }
                Method[] methods = clazz.getMethods();
                for (Method method : methods) {
                    if (!method.isAnnotationPresent(MyRequestMapping.class)) {
                        continue;
                    }
                    MyRequestMapping annotation = method.getAnnotation(MyRequestMapping.class);
                    String url = annotation.value();

                    url = (baseUrl + "/" + url).replaceAll("/+", "/");
                    handlerMapping.put(url, method);
                    controllerMap.put(url, clazz.newInstance());
                    System.out.println(url + "," + method);
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```
#### web.xml配置
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee" xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0">
    <servlet>
        <servlet-name>MySpringMVC</servlet-name>
        <servlet-class>top.soft1010.spring.mvc.manual.core.MyDispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>conf/mvc.properties</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>MySpringMVC</servlet-name>
        <url-pattern>/*</url-pattern>
    </servlet-mapping>

</web-app>
```
#### 运用
```
@MyController
@MyRequestMapping(value = "/mvc")
public class MyTestController {

    @MyRequestMapping(value = "/test")
    public Object test(@MyRequestParam(value = "name") String name, HttpServletRequest request, HttpServletResponse response) {
        try {
            response.getWriter().println(name);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

[源码](https://github.com/nine-free/spring-all)

