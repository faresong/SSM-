# web.xml

路径：/taotao-manager/src/main/webapp/WEB-INF/web.xml

内容简介：
1. <display-name> 项目名称 taotao-manager
2.  <welcome-file-list> 欢迎文件列表
3. <context-param> 指定加载的 Spring 配置文件 (applicationContext.xml)
4. <listener> 指定以 Listener 方式启动 Spring 容器
5. <servlet> 配置前端控制器 DispatcherServlet
6. <servlet-mapping> 配置该 DispatcherServlet 需要处理的 url 路径
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         id="WebApp_ID" version="2.5">
    <!--项目名称 taotao-manager-->
    <display-name>taotao-manager</display-name>
    <!--欢迎文件列表-->
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
        <welcome-file>index.htm</welcome-file>
        <welcome-file>index.jsp</welcome-file>
        <welcome-file>default.html</welcome-file>
        <welcome-file>default.htm</welcome-file>
        <welcome-file>default.jsp</welcome-file>
    </welcome-file-list>
    <!--指定加载的 Spring 配置文件 (applicationContext.xml)-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <!--指定以 Listener 方式启动 Spring 容器-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!--配置前端控制器 DispatcherServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
    </servlet>
    <!--配置该 DispatcherServlet 需要处理的 url 路径-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```


