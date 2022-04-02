[TOC]



# SSM框架

Spring MVC 负责实现 MVC 设计模式，MyBatis 负责数据持久层，Spring 负责管理 Spring MVC 和 MyBatis 相关对象的创建和依赖注入



## 项目结构

![image-20210815135036303](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210815135036303.png)



## 引入 Maven 依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webmvc</artifactId>
  <version>5.3.9</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-jdbc</artifactId>
  <version>5.3.9</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework/spring-aop -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-aop</artifactId>
  <version>5.3.9</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework/spring-aspects -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-aspects</artifactId>
  <version>5.3.9</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>3.5.7</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-core -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.14.1</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis-spring</artifactId>
  <version>2.0.6</version>
</dependency>
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.26</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.mchange/c3p0 -->
<dependency>
  <groupId>com.mchange</groupId>
  <artifactId>c3p0</artifactId>
  <version>0.9.5.5</version>
</dependency>
<!-- https://mvnrepository.com/artifact/javax.servlet/jstl -->
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>jstl</artifactId>
  <version>1.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>javax.servlet-api</artifactId>
  <version>4.0.1</version>
  <scope>provided</scope>
</dependency>
<!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>fastjson</artifactId>
  <version>1.2.78</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <version>1.18.20</version>
  <scope>provided</scope>
</dependency>
```



## 配置 Maven 资源路径

此配置放在`<build>`中

```xml
<resources>
  <resource>
    <directory>src/main/java</directory>
    <includes>
      <include>**/*.xml</include>
    </includes>
  </resource>
  <resource>
    <directory>src/main/resources</directory>
    <includes>
      <include>*.xml</include>
      <include>*.properties</include>
    </includes>
  </resource>
</resources>
```



## web.xml 配置 Sping MVC、Spring、字符编码过滤器、加载静态资源

```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
    <display-name>Archetype Created Web Application</display-name>

    <!--启动Spring-->
    <context-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring.xml</param-value>
    </context-param>

    <!--配置过滤器解决中文乱码，处理客户端到服务器的数据-->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>

    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--配置监听-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!--配置Spring MVC 默认过滤器-->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--处理Spring MVC 加载静态资源-->
    <servlet-mapping>
        <servlet-name>default</servlet-name>
        <url-pattern>*.js</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>default</servlet-name>
        <url-pattern>*.css</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>default</servlet-name>
        <url-pattern>*.png</url-pattern>
    </servlet-mapping>
</web-app>
```



## spring.xml 配置数据库

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--配置 MyBatis-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!--数据源基本属性-->
        <property name="user" value="wanshubin"/>
        <property name="password" value="wanshubin0223"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3719/test?useUnicode=true&amp;characterEncoding=utf-8&amp;serverTimezone=UTC"/>
        <property name="driverClass" value="com.mysql.cj.jdbc.Driver"/>
        <!--初始状态的连接数-->
        <property name="initialPoolSize" value="20"/>
        <!--连接的临界值，允许的池中最小剩余连接数，当达到此值时，开始再次申请连接-->
        <property name="minPoolSize" value="5"/>
        <!--当连接对象不够时，再次申请的连接个数-->
        <property name="acquireIncrement" value="5"/>
        <!--设置最大连接个数-->
        <property name="maxPoolSize" value="40"/>
    </bean>

    <!--配置Mybatis SqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="mapperLocations" value="classpath:com/fury/repository/*.xml"/>
        <property name="configLocation" value="classpath:config.xml"/>
    </bean>

    <!--扫描Mybatis Mapper接口-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.fury.repository"/>
    </bean>

</beans>
```



## 配置 mybatis-config-min

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <settings>
        <!--开启日志 logImpl：控制日志 STDOUT_LOGGING：把日志输出到控制台上-->
        <setting name="logImpl" value="STDOUT_LOGGING"/>
        <!--开启延迟加载-->
        <setting name="lazyLoadingEnabled" value="true"/>
        <!--开启二级缓存-->
        <setting name="cacheEnabled" value="true"/>
    </settings>

    <!--指定一个包名，Mybatis会在包名下搜索需要的JavaBean-->
    <typeAliases>
        <package name="com.fury.entity"/>
    </typeAliases>
</configuration>
```



## 创建 JavaBean

```java
@Data
public class User {
    private Integer id;
    private String name;
    private String password;
    private Double score;
    private Date birthday;
}
```



## 创建 Repository

```java
public interface UserRepository {
    public List<User> findAll();
}
```



## 创建 Repository 的 mapper xml配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace通常设置为文件所在包+文件名的形式，如com.fury.mapper.AccountMapper-->
<mapper namespace="com.fury.repository.UserRepository">
    <!-- <insert id=" " parameterType=" "></insert> -->
    <!-- <update id=" " parameterType=" "></update> -->
    <!-- <delete id=" " parameterType=" "></delete>  -->
    <select id="findAll" resultType="User">
        select * from user;
    </select>
</mapper>
```



## 创建 Service 接口

```java
public interface UserService {
    public List<User> findAll();
}
```



## 实现 Service 接口并注入 Repository

```java
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserRepository userRepository;

    @Override
    public List<User> findAll() {
        return userRepository.findAll();
    }
}
```



## 创建 Handler 并注入 Service

```java
@Controller
@RequestMapping("/user")
public class UserHandler {
    @Autowired
    private UserService userService;

    @RequestMapping("/index")
    public ModelAndView index(){
        ModelAndView modelAndView=new ModelAndView("index");
        modelAndView.addObject("list",userService.findAll());
        return modelAndView;
    }
}
```



## 编写 JSP

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@page isELIgnored="false" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>index</title>
</head>
<body>
<c:forEach items="${list}" var="user">
    ${user.id}--${user.name}--${user.password}--${user.score}--${user.birthday}
</c:forEach>
</body>
</html>
```



# 使用 log4j 和 slf4j 日志

## 添加依赖

```xml
<!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-api -->
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-api</artifactId>
  <version>1.7.32</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-web -->
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-web</artifactId>
  <version>2.14.1</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-core -->
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-core</artifactId>
  <version>2.14.1</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-slf4j-impl -->
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-slf4j-impl</artifactId>
  <version>2.14.1</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-api -->
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-api</artifactId>
  <version>2.14.1</version>
</dependency>
```



## 配置 lo4j

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!-- status : 指定log4j本身的打印日志的级别.ALL< Trace < DEBUG < INFO < WARN < ERROR
    < FATAL < OFF。 monitorInterval : 用于指定log4j自动重新配置的监测间隔时间，单位是s,最小是5s. -->
<Configuration status="DEBUG" monitorInterval="30">
    <Properties>
        <!-- 配置日志文件输出目录 ${sys:user.home} -->
        <property name="LOG_HOME">D:/logs</property>
        <property name="INFO_LOG_FILE_NAME">D:/logs/info</property>
        <property name="ERROR_LOG_FILE_NAME">D:/logs/error</property>
        <property name="WARN_LOG_FILE_NAME">D:/logs/warn</property>

        <property name="PATTERN">%d{yyyy-MM-dd HH:mm:ss.SSS} [%t-%L] %-5level %logger{36} - %msg%n</property>
    </Properties>

    <Appenders>
        <!--这个输出控制台的配置 -->
        <Console name="Console" target="SYSTEM_OUT">
            <!-- 控制台只输出level及以上级别的信息(onMatch),其他的直接拒绝(onMismatch) -->
            <ThresholdFilter level="DEBUG" onMatch="ACCEPT"
                             onMismatch="DENY" />
            <!-- 输出日志的格式 -->
            <!--
                %d{yyyy-MM-dd HH:mm:ss, SSS} : 日志生产时间
                %p : 日志输出格式
                %c : logger的名称
                %m : 日志内容，即 logger.info("message")
                %n : 换行符
                %C : Java类名
                %L : 日志输出所在行数
                %M : 日志输出所在方法名
                hostName : 本地机器名
                hostAddress : 本地ip地址 -->
            <PatternLayout
                    pattern="${PATTERN}" />
        </Console>

        <!--文件会打印出所有信息，这个log每次运行程序会自动清空，由append属性决定，这个也挺有用的，适合临时测试用 -->
        <!--append为TRUE表示消息增加到指定文件中，false表示消息覆盖指定的文件内容，默认值是true -->
        <File name="log" fileName="logs/test.log" append="false">
            <PatternLayout
                    pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </File>
        <!-- 这个会打印出所有的info及以下级别的信息，每次大小超过size，
        则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档 -->
        <RollingFile name="RollingFileInfo" fileName="${INFO_LOG_FILE_NAME}/info.log"
                     filePattern="${LOG_HOME}/$${date:yyyy-MM}/info-%d{yyyy-MM-dd}-%i.log">
            <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch） -->
            <ThresholdFilter level="INFO" onMatch="ACCEPT"
                             onMismatch="DENY" />
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" />
            <Policies>
                <!-- 基于时间的滚动策略，interval属性用来指定多久滚动一次，默认是1 hour。 modulate=true用来调整时间：比如现在是早上3am，interval是4，那么第一次滚动是在4am，接着是8am，12am...而不是7am. -->
                <!-- 关键点在于 filePattern后的日期格式，以及TimeBasedTriggeringPolicy的interval，
                日期格式精确到哪一位，interval也精确到哪一个单位 -->
                <!-- log4j2的按天分日志文件 : info-%d{yyyy-MM-dd}-%i.log-->
                <TimeBasedTriggeringPolicy interval="1" modulate="true" />
                <!-- SizeBasedTriggeringPolicy:Policies子节点， 基于指定文件大小的滚动策略，size属性用来定义每个日志文件的大小. -->
                <SizeBasedTriggeringPolicy size="10MB" />
            </Policies>
            <DefaultRolloverStrategy max="10" />
        </RollingFile>

        <RollingFile name="RollingFileWarn" fileName="${WARN_LOG_FILE_NAME}/warn.log"
                     filePattern="${WARN_LOG_FILE_NAME}/$${date:yyyy-MM}/warn-%d{yyyy-MM-dd}-%i.log">
            <ThresholdFilter level="WARN" onMatch="ACCEPT"
                             onMismatch="DENY" />
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" />
            <Policies>
                <TimeBasedTriggeringPolicy />
                <SizeBasedTriggeringPolicy size="10MB" />
            </Policies>
            <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件，这里设置了20 -->
            <DefaultRolloverStrategy max="10" />
        </RollingFile>

        <RollingFile name="RollingFileError" fileName="${ERROR_LOG_FILE_NAME}/error.log"
                     filePattern="${ERROR_LOG_FILE_NAME}/$${date:yyyy-MM}/error-%d{yyyy-MM-dd-HH-mm}-%i.log">
            <ThresholdFilter level="ERROR" onMatch="ACCEPT"
                             onMismatch="DENY" />
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" />
            <Policies>
                <!-- log4j2的按分钟 分日志文件 : warn-%d{yyyy-MM-dd-HH-mm}-%i.log-->
                <TimeBasedTriggeringPolicy interval="1" modulate="true" />
                <SizeBasedTriggeringPolicy size="10 MB" />
            </Policies>
            <DefaultRolloverStrategy max="10" />
        </RollingFile>

    </Appenders>

    <!--然后定义logger，只有定义了logger并引入的appender，appender才会生效-->
    <Loggers>
        <!--过滤掉spring和mybatis的一些无用的DEBUG信息-->
        <logger name="org.springframework" level="INFO"/>
        <logger name="org.mybatis" level="INFO"/>

        <!-- 第三方日志系统 -->
        <logger name="org.springframework.core" level="info" />
        <logger name="com.mchange.v2.cfg.MConfig" level="warn"/>
        <logger name="org.springframework.beans" level="info" />
        <logger name="org.springframework.context" level="info" />
        <logger name="org.springframework.web" level="info" />
        <logger name="org.jboss.netty" level="warn" />
        <logger name="org.apache.http" level="warn" />


        <!-- 配置日志的根节点 -->
        <root level="all">
            <appender-ref ref="Console"/>
            <appender-ref ref="RollingFileInfo"/>
            <appender-ref ref="RollingFileWarn"/>
            <appender-ref ref="RollingFileError"/>
        </root>

    </Loggers>

</Configuration>
```



## web.xml 中配置

```xml
<!--加载log4j配置-->
<context-param>
    <param-name>log4jConfiguration</param-name>
    <param-value>classpath:log4j2.xml</param-value>
</context-param>
```

```xml
<!--配置log4j监听-->
<listener>
    <listener-class>org.apache.logging.log4j.web.Log4jServletContextListener</listener-class>
</listener>
```

