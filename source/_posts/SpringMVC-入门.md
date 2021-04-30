---
title: SpringMVC 入门
date: 2021-04-30 18:19:43
tags: 总结文档
copyright: false
---


## 1. SpringMVC简介

### 1.1 MVC模式

MVC是软件工程中的一种软件架构模式，它是一种分离业务逻辑与显示界面的开发思想。

{% note info no-icon %}

* M（model）模型：处理业务逻辑，封装实体
* V（view） 视图：展示内容
* C（controller）控制器：负责调度分发（1.接收请求、2.调用模型、3.转发到视图）

{% endnote %}

![](https://gitee.com/Jasper-zh/image_host/raw/master/20210430182358.png)

### 1.2 SpringMVC概述

SpringMVC 是一种基于 Java 的实现 MVC 设计模式的轻量级 Web 框架，属于SpringFrameWork 的后续产品，已经融合在 Spring Web Flow 中。

SpringMVC 已经成为目前最主流的MVC框架之一，并且随着Spring3.0 的发布，全面超越 Struts2，成为最优秀的 MVC 框架。它通过一套注解，让一个简单的 Java 类成为处理请求的控制器，而无须实现任何接口。同时它还支持 RESTful 编程风格的请求。

![](https://gitee.com/Jasper-zh/image_host/raw/master/20210430182450.png)

**总结**

SpringMVC的框架就是封装了原来Servlet中的共有行为；例如：参数封装，视图转发等。


### 1.3 SpringMVC快速入门

**需求**
客户端发起请求，服务器接收请求，执行逻辑并进行视图跳转。

**步骤分析**
{% note info no-icon %}

1. 创建web项目，导入SpringMVC相关坐标
2. 配置SpringMVC前端控制器 DispathcerServlet
3. 编写Controller类和视图页面
4. 使用注解配置Controller类中业务方法的映射地址
5. 配置SpringMVC核心文件 spring-mvc.xml

{% endnote %}


**1）创建web项目，导入SpringMVC相关坐标**

```xml
<!-- 设置为web工程 -->
<packaging>war</packaging>
<dependencies>
    <!--springMVC坐标-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.1.5.RELEASE</version>
    </dependency>
    <!--servlet坐标-->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>
    <!--jsp坐标-->
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>jsp-api</artifactId>
        <version>2.2</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

**2）配置SpringMVC前端控制器DispathcerServlet**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app
    xmlns="http://xmlns.jcp.org/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee 
    http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd" version="3.1">
    <!--前端控制器-->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet- class>org.springframework.web.servlet.DispatcherServlet
        </servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>2</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

**3）编写Controller类和视图页面**

UserController.java
```java
public class UserController {
	public String quick() {
		System.out.println("quick running.....");
		return "/WEB-INF/pages/success.jsp";
	}
}
```


/WEB-INF/pages/ success.jsp
```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
		<head>
			<title>
				success
			</title>
		</head>
		<body>
			<h3>
				请求成功！
			</h3>
		</body>
	</html>
```


**4）使用注解配置Controller类中业务方法的映射地址**

```java
@Controller 
public class UserController {
	@RequestMapping("/quick") 
	public String quick() {
		System.out.println("quick running.....");
		return "/WEB-INF/pages/success.jsp";
	}
}
```

**5）配置SpringMVC核心文件spring-mvc.xml**

```xml
<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd 
    http://www.springframework.org/schema/mvc 
    http://www.springframework.org/schema/mvc/spring-mvc.xsd 
    http://www.springframework.org/schema/context 
    http://www.springframework.org/schema/context/spring-context.xsd">
    
    <!--配置注解扫描-->
    <context:component-scan base-package="com.lagou.controller"/>
</beans>
```