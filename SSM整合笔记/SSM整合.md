# 1. web.xml

路径：/taotao-manager/src/main/webapp/WEB-INF/web.xml

内容简介：
0. `<web-app>` 总括标签
1. `<display-name>` 项目名称 taotao-manager
2.  `<welcome-file-list>` 欢迎文件列表
3. `<context-param> `指定加载的 Spring 配置文件 **applicationContext.xml**
4. `<listener>` 指定以 Listener 方式启动 Spring 容器
5. `<servlet>` 配置前端控制器 DispatcherServlet
6. `<servlet-mapping> `配置该 DispatcherServlet 需要处理的 url 路径
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

# 2. applicationContext.xml

路径：/taotao-manager/src/main/resources/applicationContext.xml

内容简介：
0. `<beans>` 总括标签
1. `<context:property-placeholder location="classpath:*.properties"/>` 指定加载以 *.properties 结尾文件
2. `数据库连接池对象 <bean>` 以 ioc 方式配置连接池对象 DruidDataSource
3. `MyBatis对象 <bean>` 以 ioc 方式配置 MyBatis对象 SqlSessionFactoryBean
    3.1. `<property name="dataSource" ref="dataSource"/>` 引用连接池对象 DruidDataSource
    3.2. `<property name="configLocation" value="classpath:sqlMapConfig.xml"/>` 指定加载的 MyBatis 全局配置文件 **sqlMapConfig.xml**
4. `Mapper扫描器/配置器对象 <bean>` 以 ioc 方式配置 Mapper扫描器/配置器对象 MapperScannerConfigurer
    4.1. `<property name="basePackage" value="com.taotao.mapper"/>` 扫描 com.taotao.mapper 包下面的所有 MyBatis配置文件
5. `<context:component-scan base-package="com.taotao.service"/>` 扫描com.taotao.service包下面的所有 Spring 注解
6. `事务管理器对象 <bean>` 管理项目中 service 包下面的事务
    6.1. <property name="dataSource" ref="dataSource"/> 引用连接池对象 DruidDataSource
7. `<tx:advice id="txAdvice" transaction-manager="transactionManager">` 声明定义一个事务管理器样板
	7.1. `<tx:attributes>` 该事务管理器样板的属性
	​	7.1.1. `<tx:method name="save*" isolation="REPEATABLE_READ" propagation="REQUIRED"/>` 指定方法名的该方法所对应的隔离级别、传播行为
9. `<aop:config>` 面向切面的配置
	9.1. `<aop:advisor advice-ref="txAdvice" pointcut="execution(* com.taotao.service.*.*(..))"/>` 引用上面已声明的事务管理器样板，并配置切入点 (作用范围) 为 com.taoato.service 包下面的任意方法任意参数，都进行事务管理

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop-4.0.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
	http://www.springframework.org/schema/util
	http://www.springframework.org/schema/util/spring-util-4.0.xsd">

    <!--
        从类路径上面去加载以.properties结尾的文件
     -->
    <context:property-placeholder location="classpath:*.properties"/>
    <!--在创建这个对象的同时 赋初值
        db.properties
        jdbc.url=jdbc:mysql://localhost:3306/taotao?characterEncoding=utf-8
        jdbc.username=root
    -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="maxActive" value="${jdbc.maxActive}"/>
        <property name="minIdle" value="${jdbc.minIdle}"/>
    </bean>
    <!--
        创建mybatis对象 并且交给spring容器来管理
        SqlSessionFactoryBean  mybatis对象
        创建mybatis对象的同时赋初值
            ref: 只能写 引用数据类型
            value: 只能写 基本数据类型
                在三大框架里面 String他认为是基本数据类型

        1.加载一个数据库连接池
        SqlSessionFactoryBean sqlSessionFactory = new SqlSessionFactoryBean();
        sqlSessionFactory.setDataSource(dataSource);
        2.加载一个配置文件  mybatis的核心配置文件
            记住一句话 凡是在spring的配置文件里面写的路径  都要加上classpath
     -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 数据库连接池 -->
        <property name="dataSource" ref="dataSource"/>
        <!-- 加载mybatis的全局配置文件 -->
        <property name="configLocation" value="classpath:sqlMapConfig.xml"/>
    </bean>
    <!--
        使用mybatis与spring的整合包里面的一个对象
        MapperScannerConfigurer configurer = new MapperScannerConfigurer();
        有一个属性 叫做
        configurer.setBasePackage("com.clive.mapper");
        他会去扫描com.clive.mapper这个包下面的所有的 mybatis的配置文件
        吧这些配置文件加载到spring容器中去
     -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.taotao.mapper"/>
    </bean>
    <!--
        扫描com.taotao.service包下面的所有的spring注解
        解析注解 把他加载到spring容器中去
    -->
    <context:component-scan base-package="com.taotao.service"/>
    <!--
        创建了spring的事务管理器对象用于管理我们项目中的所有service里面的事务
            1.开启事务
            2.提交事务
            3.回滚事务
            4.事务的隔离级别和传播行为
     -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!-- 数据源 -->
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--
        定义我们的隔离级别是什么级别
        定义我们的传播行为是什么行为
        定位什么方法名管理什么级别和行为
     -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <!-- 传播行为
                这里配置的是 事务的隔离级别和传播行为
                默认是不用配置隔离级别的  默认使用4 作为隔离级别就行了
                传播行为 也是用默认的  一个方法里面有两个事务  使用同一个事务 还是分开使用
             -->

            <tx:method name="save*"  propagation="REQUIRED"/>
            <tx:method name="add*" propagation="REQUIRED"/>
            <tx:method name="insert*" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED"/>
            <tx:method name="update*" propagation="REQUIRED"/>
            <tx:method name="find*" propagation="SUPPORTS" read-only="true"/>
            <tx:method name="get*" propagation="SUPPORTS" read-only="true"/>
        </tx:attributes>
    </tx:advice>
    <!--
        定义规则
            我们项目中那个包下那个类下管理我们的事务
            凡是以 com.taoato.service包下面的所有类所有方法任意参数都进行事务管理
    -->
    <aop:config>
        <aop:advisor advice-ref="txAdvice"
                     pointcut="execution(* com.taotao.service.*.*(..))"/>
    </aop:config>
</beans>
```

