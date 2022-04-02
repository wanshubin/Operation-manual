[TOC]



# MVC结构

将程序分为 Controller、Model、View 三层，Controller 接收客户端请求，调用 Model 生成业务数据，传递给 View

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210810110323665.png" alt="image-20210810110323665" style="zoom: 80%;" />

# Spring MVC

Spring MVC 是目前主流的实现 MVC 设计模式的企业级开发框架，Spring 框架的一个子模块，无需整合，开发起来更加便捷

Spring MVC 是对 MVC 流程的封装，屏蔽了很多底层代码，开放出接口，让开发者可以更加轻松便捷地完成基于 MVC 模式的 Web 开发



# Spring MVC 的核心组件

+ **DispatcherServlet** —— 前置控制器，是整个流程控制的核心，控制其他组件的执行，进行统一调度，降低组件之间的耦合性
+ **Handler** —— 处理器，完成具体的业务逻辑，相当于 Servlet 或 Action
+ **HandlerMapping** —— DispatcherServlet 接收到请求之后，通过 HandlerMapping 将不同的请求映射到不同的 Handler
+ **HandlerInterceptor** —— 处理器拦截器，是一个接口，如果需要完成一些拦截处理，可以实现该接口
+ **HandlerExecutionChain** —— 处理器执行链，包括两部分内容：Handler 和 HandlerInterceptor（系统会有一个默认的 HandlerInterceptor，如果需要额外设置拦截，可以添加拦截器）
+ **HandlerAdapter** —— 处理器适配器，Handler 执行业务方法之前，需要进行一系列操作，包括表单数据验证、数据类型的转换、将表单数据封装到 JavaBean 等，这些操作都是由 HandlerAdapter 来完成，开发者只需将注意了集中业务逻辑的处理上，DispatcherServlet 通过 HandlerAdapter 执行不同的 Handler
+ **ModelAndView** —— 装载了模型数据和视图信息，作为 Handler 的处理结果，返回给 DispatcherServlet
+ **ViewResolver** —— 视图解析器，DispatcherServlet 通过它将逻辑视图解析为物理视图，最终将渲染的结果响应给客户端



# Spring MVC 的工作流程

![image-20210810115349734](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210810115349734.png)

+ 客户端请求被 DispatcherServlet 接收
+ 根据 HandlerMapping 映射到 Handler
+ 生成 Handler 和 HandlerInterceptor
+ Handler 和 HandlerInterceptor 以 HandlerExecutionChain 的形式一并返回给 DispatcherServlet
+ DispatcherServlet 通过 HandlerAdapter 调用 Handler 的方法完成业务逻辑处理
+ Handler 返回一个 ModelAndView 给 DispatcherServlet
+ DispatcherServlet 将获取的 ModelAndView 对象传给 ViewResolver 视图解析器将逻辑视图解析为物理视图 View
+ ViewResovler 返回一个 View 给 DispatcherServlet
+ DispatcherServlet 根据 View 进行视图渲染（将模型数据 Model 填充到视图 View 中）
+ DispatcherServlet将渲染后的结果响应给客户端



# 使用Spring MVC

Spring MVC 流程非常复杂，实际开发中很简单，因为大部分的组件不需要开发者创建、管理，只需要通过配置文件的方式完成配置即可，真正需要开发者处理的只有 Handler、View

## 创建Maven依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webmvc</artifactId>
  <version>5.3.9</version>
</dependency>
```

## 在 web.xml 中配置 DispatcherServlet

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>
    
    <!--使用过滤器解决中文乱码-->
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
</web-app>
```

## 配置 springmvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.0.xsd
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <!--自动扫描-->
    <context:component-scan base-package="com.fury.*"/>

    <!--配置视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

## 创建 Handler

```java
@Controller
@RequestMapping("/hello")
public class HelloHandler {
    @RequestMapping("/index")
    public String index(){
        System.out.println("执行了index");
        return "index";
    }
}
```



# Spring MVC 注解

+ @RequestMapping

Spring MVC 通过 @RequestMapping 注解将 URL 请求与业务方法进行映射，在 Handler 的类定义处以及方法定义处都可以添加 @RequestMapping，在类定义处添加，相当于客户端多了一层访问路径

+ @Controller

@Controller 在类定义处添加，将该类交给 IOC 容器来管理（结合springmvc.xml的自动扫描配置），同时使其成为一个控制器，可以接收客户端请求

```xml
@Controller
@RequestMapping("/hello")
public class HelloHandler {
    @RequestMapping("/index")
    public String index(){
        System.out.println("执行了index");
        return "index";
    }
}
```

## @RequestMapping 的属性

+ **value** —— 指定 URL 请求的实际地址，是 @RequestMapping 的默认参数选项

```java
@RequestMapping("/hello")
@RequestMapping(value = "/hello")
```

+ **method** —— 指定请求的 method 类型，get、post、put、delete

```java
@RequestMapping(value = "/hello",method = RequestMethod.GET)
```

上述代码表示 index 方法只能接收 GET 请求

+ **params** —— 指定请求中必须包含某些参数，否则无法调用该方法

```java
@RequestMapping(value = "/hello",method = RequestMethod.GET,params = {"name","id"})
```

上述代码表示 index 方法必须接收 name 和 id 参数

```java
@RequestMapping(value = "/hello",method = RequestMethod.GET,params = {"name","id=10"})
```

上述代码表示 index 方法必须接收 name 和 id 参数，且 id 必须为 10



## @RequestParam 在方法中获取参数

使用 @RequestParam 注解来获取传来的参数，并且自动完成数据类型的转换，这些工作是由 HandlerAdapter 来完成的

当请求参数username不存在时会有异常发生,可以通过设置属性required=false解决,例如: @RequestParam(value="username", required=false)

```java
@Controller
@RequestMapping(value = "/hello",method = RequestMethod.GET,params = {"name","id"})
public class HelloHandler {
    @RequestMapping("/index")
    public String index(@RequestParam("name") String name,@RequestParam("id") Integer id){
        System.out.println(name+" "+id);
        System.out.println("执行了index");
        return "index";
    }
}
```



## HttpServletRequest 获取参数

```java
@RequestMapping("/httpServletRequest")
public String httpServletRequest(HttpServletRequest httpServletRequest){
    User user=(User)httpServletRequest.getAttribute("user");
    return "view";
}
```



## @RequestAttribute 获取参数

是 httpServletRequest 方式的注解办法

```java
@RequestMapping("/RequestAttribute")
public String requestAttribute(@RequestAttribute("msg") String msg){
    requestService.add(msg);
    return "view";
}
```





## Spring MVC 也支持 RESTful 风格的 URL

传统类型：http://localhost:8080/hello/index?name=张三&id=10

REST：http://localhost:8080/hello/index/张三/10

**在 Spring MVC 中解析 RESTful 风格的 URL**

```java
@RequestMapping("/rest/{name}/{id}")
public String test(@PathVariable("name") String name,@PathVariable("id") Integer id){
    System.out.println(name);
    System.out.println(id);
    return "rest";
}
```

通过 @PathVariable 注解完成参数请求与形参的映射



## Spring MVC 通过映射可以直接在业务方法中获取 Cookie 的值

```java
@RequestMapping("/cookie")
public String cookie(@CookieValue(value = "JSESSIONID") String sessionId){
    System.out.println(sessionId);
    return "index";
}
```



## 使用 JavaBean 绑定参数

Spring MVC 会根据请求参数名和 JavaBean 属性名进行自动匹配，自动为对象填充属性值，同时支持级联属性

### JavaBean

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private Long id;
    private String name;
    private Address address;
}
```

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Address {
    private String value;
}
```

### JSP

**其中 name 属性对应 JavaBean 中的属性字段，级联时要使用  对象.属性  的形式**

```jsp
<form action="/hello/save" method="post">
    用户id：<input type="text" name="id">
    用户名：<input type="text" name="name">
    地址：<input type="text" name="address.value">
    <input type="submit" value="提交">
</form>
```

### Handler

在形参中传入 JavaBean

```java
@RequestMapping(value = "/save",method = RequestMethod.POST)
public String save(User user){
    System.out.println(user);
    return "index";
}
```



# JSP 页面的转发和重定向

**Spring MVC 默认是以转发的形式来响应 JSP**

## 转发

除了默认值，还可以使用如下方法

```java
@RequestMapping("/forward")
public String forward(){
    return "forward:/index.jsp";
}
```

## 重定向

```java
@RequestMapping("/redirect")
public String redirect(){
    return "redirect:/index.jsp";
}
```



# Spring MVC 数据绑定

数据绑定：在后端的业务方法中直接获取客户端 HTTP 请求中的参数，将请求参数映射到业务方法的形参中

Spring MVC 中数据绑定的工作是由 HandlerAdapter 来完成的



## @RequestParam 的属性

+ **value = “num”** —— 将 Http 请求中名为 num 的参数 赋值给形参 id
+ **required** —— 设置 num 是否为必填项，默认为 true
+ **defaultValue = “ ”** ——设置未填写参数时的默认值



## 基本数据类型的绑定

```java
@RequestMapping("/baseType")
@ResponseBody
public String baseType(@RequestParam(value = "id",required = false,defaultValue = "1") Integer id){
    return "<h1>"+id+"</h1>";
}
```

@ResponseBody 表示 Spring MVC 会直接将业务方法的返回值响应给客户端，如果不加 @ResponseBody，Spring MVC 会将业务方法的返回值传递给 DispatcherServlet，再由 DispatcherServlet 调用 ViewResolver 对返回值进行解析，映射到一个 JSP 资源

**使用 包装类 避免没有传递参数而造成程序错误**，包装类可以接收 null



## 数组的绑定

```java
@RequestMapping("/arrayType")
@ResponseBody
public String arrayType(String[] name){
    String str= Arrays.toString(name);
    return str;
}
```

使用

http://localhost:8080/data/arrayType?name=zhangsan&name=lisi&name=wanger&name=mazi

来完成数组参数的传递



## List类型的绑定

Spring MVC 不支持 List 类型的直接转换，需要对 List 类型进行包装

### 创建List类型

```java
@Data
public class UserList {
    private List<User> userList=new ArrayList<>();
}
```

### 编辑表单项目的 name 属性

```jsp
<form action="/data/listType" method="post">
    用户1编号：<input type="text" name="userList[0].id">
    用户1姓名：<input type="text" name="userList[0].name">
    用户2编号：<input type="text" name="userList[1].id">
    用户2姓名：<input type="text" name="userList[1].name">
    用户3编号：<input type="text" name="userList[2].id">
    用户3姓名：<input type="text" name="userList[2].name">
    <input type="submit" value="提交">
</form>
```

### 创建 Handler

```java
@RequestMapping("/listType")
@ResponseBody
public String listType(UserList userList){
    StringBuffer str=new StringBuffer();
    for (User user:userList.getUserList()){
        str.append(user);
    }
    return str.toString();
}
```



## Map类型的绑定

Spring MVC 不支持 Map 类型的直接转换，需要对 Map 类型进行包装

### 创建Map类型

```java
@Data
public class UserMap {
    private Map<String, User> userMap;
}
```

### 编辑表单项目的 name 属性

```jsp
<form action="/data/mapType" method="post">
    用户1编号：<input type="text" name="userList['a'].id">
    用户1姓名：<input type="text" name="userList['a'].name">
    用户2编号：<input type="text" name="userList['b'].id">
    用户2姓名：<input type="text" name="userList['b'].name">
    用户3编号：<input type="text" name="userList['c'].id">
    用户3姓名：<input type="text" name="userList['c'].name">
    <input type="submit" value="提交">
</form>
```

### 创建 Handler

```java
@RequestMapping("/mapType")
@ResponseBody
public String mapType(UserMap userMap){
    StringBuffer stringBuffer = new StringBuffer();
    for (String key: userMap.getUserMap().keySet()){
        User user = userMap.getUserMap().get(key);
        stringBuffer.append(user);
    }
    return stringBuffer.toString();
}
```



## JSON类型的绑定

客户端发送 JSON 格式的数据，直接通过 Spring MVC 绑定到业务方法的形参中

### 引入fastjson工具

maven

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>fastjson</artifactId>
  <version>1.2.78</version>
</dependency>
```

springmvc.xml配置

```xml
<!--配置消息转换器解决中文乱码,处理服务器到客户端的数据-->
<mvc:annotation-driven>
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="supportedMediaTypes" value="text/html;charset=UTF-8"/>
        </bean>
        <!--配置fastjson工具-->
        <bean class="com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter"/>
    </mvc:message-converters>
</mvc:annotation-driven>
```

### 前端使用 ajax

```jsp
<script type="text/javascript" src="https://cdn.staticfile.org/jquery/1.10.0/jquery.min.js"></script>
<script type="text/javascript">
    $(function (){
       var user={
           "id":1,
           "name":"张三"
       }
       $.ajax({
           url:"/data/json",
           data:JSON.stringify(user),
           type:"POST",
           contentType:"application/json;charset=UTF-8",
           dataType:"JSON",
           success:function (data){
               alert(data.id+"   "+data.name)
           }
       })
    });
</script>
```

### 创建 Handler，使用 @RequestBody 注解

```java
@RequestMapping("/json")
@ResponseBody
public User jsonType(@RequestBody User user){
    System.out.println(user);
    user.setId(6L);
    user.setName("乌拉");
    return user;
}
```





# @RestController 和 @Controller

@RestController 修饰类将会使得该类的所有方法的返回值都直接响应给客户端（以 REST 形式返回）

@Controller 表示该控制器的每一个业务方法的返回值都会交给视图解析器进行解析，如果只需要将数据响应给客户端，而不需要进行视图解析，则需要在对应的业务方法定义处添加 @ResponseBody

```java
@RestController
@RequestMapping("/data")
public class DataBindHandler {

    @RequestMapping("/baseType")
    public String baseType(@RequestParam(value = "id",required = false,defaultValue = "1") Integer id){
        return "<h1>"+id+"</h1>";
    }

    @RequestMapping("/arrayType")
    public String arrayType(String[] name){
        String str= Arrays.toString(name);
        return str;
    }
}
```

```java
@Controller
@RequestMapping("/data")
public class DataBindHandler {

    @RequestMapping("/baseType")
    @ResponseBody
    public String baseType(@RequestParam(value = "id",required = false,defaultValue = "1") Integer id){
        return "<h1>"+id+"</h1>";
    }

    @RequestMapping("/arrayType")
    @ResponseBody
    public String arrayType(String[] name){
        String str= Arrays.toString(name);
        return str;
    }
}
```

这两段代码的功能是一样的





# Spring MVC 模型数据解析

JSP 四大作用域对应的内置对象：pageContext、request、session、application

模型数据的绑定是由 ViewResolver来完成的，实际开发中，我们需要先添加模型数据，再交给 ViewResolver来绑定

## Spring MVC 提供了几种方式添加数据模型

### Map

```java
@Controller
@RequestMapping("/view")
public class ViewHandler {

    @RequestMapping("/map")
    public String map(Map<String, User> map){
        User user=new User();
        user.setId(1L);
        user.setName("张三");
        map.put("User", user);
        return "view";
    }
}
```

在页面中取出

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<html>
<head>
    <title>view</title>
</head>
<body>
    ${requestScope.User}
</body>
</html>
```



### Model

```java
@RequestMapping("/model")
public String model(Model model){
    User user=new User(1L, "张三", null);
    model.addAttribute("user", user);
    return "view";
}
```



### ModelAndView

```java
@RequestMapping("/modelAndView")
public ModelAndView modelAndView(){
    User user=new User(1L, "张三", null);
    ModelAndView modelAndView = new ModelAndView("view");
    modelAndView.addObject("user", user);
    return modelAndView;
}
```

或者使用这种方式

```java
@RequestMapping("/modelAndView2")
public ModelAndView modelAndView2(){
    User user=new User(1L, "张三", null);
    ModelAndView modelAndView = new ModelAndView();
    modelAndView.addObject("user", user);
    View view=new InternalResourceView("/view.jsp");
    modelAndView.setView(view);
    return modelAndView;
}
```



### HttpServletRequest

```java
@RequestMapping("/httpServletRequest")
public String httpServletRequest(HttpServletRequest httpServletRequest){
    User user=new User(1L, "张三", null);
    ModelAndView modelAndView = new ModelAndView();
    modelAndView.addObject("user", user);
    httpServletRequest.setAttribute("user", user);
    return "view";
}
```



### SessionAttribute

```java
@RequestMapping("/session")
public String session(HttpServletRequest httpServletRequest){
    HttpSession session=httpServletRequest.getSession();
    session.setAttribute("user", new User(1L, "张三", null));
    return "view";
}
```

或者直接传入session

```java
@RequestMapping("/session2")
public String session2(HttpSession session){
    session.setAttribute("user", new User(1L, "张三", null));
    return "view";
}
```



@SessionAttributes(value = "user") 注解将会使得该类所有对 user 对象添加到 request 的操作都会将 user 也添加到 session 中

```java
@Controller
@RequestMapping("/data")
@SessionAttributes(value = "user")
public class DataBindHandler
```

或者是绑定一个类型

```java
@Controller
@RequestMapping("/data")
@SessionAttributes(types = {User.class, Address.class})
public class DataBindHandlerr
```



### Application

通过 request 间接绑定

```java
@RequestMapping("/application")
public String application(HttpServletRequest request){
    ServletContext application=request.getServletContext();
    application.setAttribute("user", new User(1L, "张三", null));
    return "view";
}
```



### @ModelAttribute

```java
//定义一个方法，该方法用来返回要填充到数据模型中的对象
@ModelAttribute
public User getUser(){
    return new User(1L, "张三", null);
}

业务方法中无需再处理数据模型，只需返回视图即可
@RequestMapping("/modelAttribute")
public String modelAttribute(){
    return "view";
}
```

或者

```java
@ModelAttribute
public void getUser(Map<String, User> map){
    map.put("user", new User(1L, "张三", null));
}

@RequestMapping("/modelAttribute")
public String modelAttribute(){
    return "view";
}
```





# Spring MVC 自定义数据转换器

数据转换器是指将客户端 HTTP 请求中的参数转换为业务方法中定义的形参，自定义表示开发者可以自主设计转换的方式，HandlerAdapter 已经提供了通用的转换，String -> int ，String ->double，表单数据的封装等，但是在特殊的业务场景下，HandlerAdapter 无法进行转换，就需要开发者自定义转换器

## 例子1：如客户端输入 String 类型的数据 “2019-03-03”，自定义转换器将该数据转为 Date 类型的对象

### 创建自定义转换器，并且实现 Convert 接口

```java
@Override
public Date convert(String s) {
    SimpleDateFormat simpleDateFormat=new SimpleDateFormat(pattern);
    Date date=null;
    try {
        date=simpleDateFormat.parse(s);
    } catch (ParseException e) {
        e.printStackTrace();
    }
    return date;
}
```

### 在 Spring MVC 中配置转换器

```xml
<!--配置自定义转换器-->
<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <list>
            <bean class="com.fury.converter.DateConverter">
                <constructor-arg type="java.lang.String" value="yyyy-MM-dd"/>
            </bean>
        </list>
    </property>
</bean>
```

### 在 mvc:annotation-driven 中添加属性

```xml
<mvc:annotation-driven conversion-service="conversionService">
```

### 创建 Handler

```java
@Controller
@RequestMapping("/convert")
public class ConverterHandler {

    @RequestMapping("/date")
    @ResponseBody
    public String dataConverter(Date date){
        return date.toString();
    }
}
```

### JSP

```jsp
<form action="/convert/date" method="post">
    请输入日期：<input type="text" name="date">(yyyy-MM-dd)
    <input type="submit" value="提交">
</form>
```



## 例子2：将客户端输入的字符串转换为 Student 对象

### 创建自定义转换器，并且实现 Convert 接口

```java
@Override
public Student convert(String s) {
    String[] args=s.split("-");
    return new Student(Integer.parseInt(args[0]),args[1],Integer.parseInt(args[2]));
}
```

### 在 Spring MVC 中配置转换器

```xml
<!--配置自定义转换器-->
<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <list>
            <bean class="com.fury.converter.DateConverter">
                <constructor-arg type="java.lang.String" value="yyyy-MM-dd"/>
            </bean>
            <bean class="com.fury.converter.StudentConverter"/>
        </list>
    </property>
</bean>
```

### 在 mvc:annotation-driven 中添加属性

```xml
<mvc:annotation-driven conversion-service="conversionService">
```

### 创建 Handler

```java
@RequestMapping("/student")
@ResponseBody
public String student(Student student){
    return student.toString();
}
```

### JSP

```jsp
<form action="">
    请输入学生信息：<input type="text" name="student">（按照id-name-age输入）
    <input type="submit" value="提交">
</form>
```





# Spring MVC REST

REST：Representational State Transfer，资源表现层状态转换，是目前比较主流的一种互联网软件架构，它结构清晰、标准规范、易于理解、便于扩展

## 资源（Resource）

网络上的一个实体，或者说网络中存在的一个具体信息，一段文本、一张图片、一首歌曲、一段视频...是一个具体的存在，可以用一个 URI （统一资源标识符）来指向它，每个资源都有对应的 URI，要获取该资源的时候，只需要访问对应的 URI 即可

## 表现层（Representation）

资源具体呈现出来的形式，比如文本可以用 txt 格式表示，也可以用 HTML、XML、JSON 等格式来表示

## 状态转换（State Transfer）

客户端如果希望操作服务器中的某个资源，就需要通过某种方式让服务端发生状态转换，而这种转换是建立在表现层之上的，所以叫做“表现层状态转换”



## 特点

+ URL 更加简洁
+ 有利于不同系统之间的资源共享，只需要遵守一定的规范，不需要进行其他配置即可实现资源共享



## REST 请求格式

接收端写好占位符

```java
@GetMapping("/guestByOrderId/{orderId}")
```

客户端

直接写在对应的位置，不用加引号等符号

```xml
http://localhost:8181/guestByOrderId/101
```



## REST 类型

REST 具体操作就是 HTTP 协议协议中四个表示操作方式的动词分别对应 CRUD 基本操作

### GET 

用来表示获取资源

### POST

用来表示新建资源

### PUT

用来表示修改资源

### DELETE

用来表示删除资源



## 使用 REST

### 创建 Repository接口 和 impl 实现类

```java
public interface StudentRepository {
    public Collection<Student> findAll();
    public Student findById(Integer id);
    public void saveOrUpdate(Student student);
    public void deleteById(Integer id);
}
```



```java
@Repository
public class StudentRepositoryImpl implements StudentRepository {
    private static Map<Integer,Student> studentMap;

    static {
        studentMap=new HashMap<>();
        studentMap.put(1, new Student(1,"张三",22));
        studentMap.put(2, new Student(2,"李四",30));
        studentMap.put(3, new Student(3,"王二",16));
        studentMap.put(4, new Student(4,"麻子",18));
        studentMap.put(5, new Student(5,"乌拉",21));
    }


    @Override
    public Collection<Student> findAll() {
        return studentMap.values();
    }

    @Override
    public Student findById(Integer id) {
        return studentMap.get(id);
    }

    @Override
    public void saveOrUpdate(Student student) {
        studentMap.put(studentMap.size()+1, student);
    }

    @Override
    public void deleteById(Integer id) {
        studentMap.remove(id);
    }
}
```

### 创建 Handler 实现 REST

```java
@Controller
@RequestMapping("/rest")
public class RESTHandler {

    @Autowired
    private StudentRepository studentRepository;

    //@RequestMapping("/findAll")
    @GetMapping("/findAll")
    @ResponseBody
    public Collection<Student> findAll(){
        return studentRepository.findAll();
    }

    @GetMapping("/findById/{id}")
    public Student findById(@PathVariable("id") Integer id){
        return studentRepository.findById(id);
    }

    @PostMapping("/save")
    public void save(@RequestBody Student student){
        studentRepository.saveOrUpdate(student);
    }

    @PostMapping("/update")
    public void update(@RequestBody Student student){
        studentRepository.saveOrUpdate(student);
    }

    @DeleteMapping("/delete/{id}")
    public void deleteById(@PathVariable("id") Integer id){
        studentRepository.deleteById(id);
    }
}
```





# Spring MVC 文件上传下载

## 单文件上传

底层是使用 Apache fileupload 组件完成上传，Spring MVC 对这种方式进行了封装

### 添加 Maven 依赖

```xml
<!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
<dependency>
  <groupId>commons-io</groupId>
  <artifactId>commons-io</artifactId>
  <version>2.11.0</version>
</dependency>
<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
<dependency>
  <groupId>commons-fileupload</groupId>
  <artifactId>commons-fileupload</artifactId>
  <version>1.4</version>
</dependency>
```

### JSP

form 的 method 要设置为 post ，get 请求只能将文件名传给服务器；enctype 设置为 multipart/form-data，否则也只能传送文件名

```jsp
<form method="post" enctype="multipart/form-data">
    <input type="file" name="img"/>
    <input type="submit" value="上传"/>
</form>
<div>
    <img src="${path}">
</div>
```

### 创建 Handler 

```java
@Controller
@RequestMapping("/file")
public class FileHandler {

    @PostMapping("/upload")
    public String upload(MultipartFile file, HttpServletRequest httpServletRequest){
        if (file.getSize()>0){
            //获取保存上传文件的file路径
            String path=httpServletRequest.getServletContext().getRealPath("file");
            System.out.println(path);
            //获取上传的文件名
            String name=file.getOriginalFilename();
            File file1=new File(path,name);
            try {
                file.transferTo(file1);
                httpServletRequest.setAttribute("path", "/file/"+name);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return "file";
    }
}
```

### 配置 springmvc.xml

```xml
<!--配置上传组件-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"/>
```

### 配置 web.xml

```xml
<!--处理Spring MVC无法加载png的请求-->
<servlet-mapping>
  <servlet-name>default</servlet-name>
  <url-pattern>*.png</url-pattern>
</servlet-mapping>
```



## 多文件上传

### 添加标签依赖

```xml
<!-- https://mvnrepository.com/artifact/javax.servlet/jstl -->
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>jstl</artifactId>
  <version>1.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.taglibs/taglibs-standard-impl -->
<dependency>
  <groupId>org.apache.taglibs</groupId>
  <artifactId>taglibs-standard-impl</artifactId>
  <version>1.2.5</version>
  <scope>runtime</scope>
</dependency>
```

### JSP

```xml
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="/file/uploads" method="post" enctype="multipart/form-data">
    file1:<input type="file" name="multipartFiles"/>
    file2:<input type="file" name="multipartFiles"/>
    file3:<input type="file" name="multipartFiles"/>
    <input type="submit" value="上传"/>
</form>
<div>
    <c:forEach items="${files}" var="file">
        <img src="${file}" width="300px">
    </c:forEach>
</div>
</body>
</html>
```

### 创建 Handler

```java
@PostMapping("/uploads")
public String uploads(MultipartFile[] multipartFiles,HttpServletRequest httpServletRequest){
    List<String> files=new ArrayList<>();
    for (MultipartFile file:multipartFiles){
        if (file.getSize()>0){
            //获取保存上传文件的file路径
            String path=httpServletRequest.getServletContext().getRealPath("file");
            System.out.println(path);
            //获取上传的文件名
            String name=file.getOriginalFilename();
            File file1=new File(path,name);
            try {
                file.transferTo(file1);
                files.add("/file/"+name);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
    httpServletRequest.setAttribute("files", files);
    return "files";
}
```



## 文件下载

### JSP

```jsp
<div>
    <a href="/file/download/万树彬电子签名.png">电子签名</a>
    <a href="/file/download/万树彬照片.jpg">照片</a>
    <a href="/file/download/万树彬照片_方.png">照片_方</a>
</div>
```

### 创建 Handler

```java
@GetMapping("/download/{name}")
public void download(@PathVariable("name") String name, HttpServletRequest request, HttpServletResponse response){
    if (name!=null){
        String path=request.getServletContext().getRealPath("file");
        File file = new File(path,name);
        OutputStream outputStream=null;
        if (file.exists()){
            response.setContentType("application/forc-download");
            response.setHeader("Content-Disposition", "attachment;filename="+name);
            try {
                outputStream = response.getOutputStream();
                outputStream.write(FileUtils.readFileToByteArray(file));
                outputStream.flush();
            } catch (IOException e) {
                e.printStackTrace();
            }finally {
                if (outputStream!=null){
                    try {
                        outputStream.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
}
```





# Spring MVC 数据校验

Spring MVC 提供两种数据校验的方式：1、基于 Validator 接口		2、使用 Annotation JSR - 303 标准进行校验

基于 Validator 接口的方式需要自定义 Validator 验证器，每一条数据的验证规则需要开发者手动完成，使用 Annotation JSR - 303 则不需要自定义验证器，通过注解的方式可以直接在实体类中添加每个属性的验证规则，这种方式更加方便

## 使用 Annotation JSR - 303 标准进行验证

使用 Hibernate Validator来实现

### 添加依赖

```xml
<!-- https://mvnrepository.com/artifact/org.hibernate/hibernate-validator -->
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>7.0.1.Final</version>
</dependency>
<!-- https://mvnrepository.com/artifact/javax.validation/validation-api -->
<dependency>
    <groupId>javax.validation</groupId>
    <artifactId>validation-api</artifactId>
    <version>2.0.1.Final</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.jboss.logging/jboss-logging -->
<dependency>
  <groupId>org.jboss.logging</groupId>
  <artifactId>jboss-logging</artifactId>
  <version>3.4.2.Final</version>
</dependency>
```

### 创建 Bean

```java
@Data
public class Person {
    @NotEmpty(message = "用户名不能为空")
    private String username;
    @Size(min = 6,max = 12,message = "密码长度必须为6-12位")
    private String password;
    @Email(regexp = "^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\\.[a-zA-Z0-9_-]+)+$")
    private String email;
    @Pattern(regexp = "^((13[0-9])|(14[5|7])|(15([0-3]|[5-9]))|(18[0,5-9]))\\\\d{8}$")
    private String pattern;
}
```

### 创建 Handler

```java
@Controller
@RequestMapping("/validator")
public class ValidatorHandler {

    @GetMapping("/register")
    public String register(Model model){
        model.addAttribute("person", new Person());
        return "resigter";
    }

    @PostMapping("/register")
    public String register(@Valid Person person, BindingResult bindingResult){
        if (bindingResult.hasErrors()){
            return "resigter";
        }
        return "index";
    }
}
```

### JSP

```jsp
<body>
<form:form modelAttribute="person" action="/validator/register2" method="post">
    用户名：<form:input path="username"/><form:errors path="username"/> <br>
    密码：<form:input path="password"/><form:errors path="password"/><br>
    邮箱：<form:input path="email"/><form:errors path="email"/><br>
    电话：<form:input path="phone"/><form:errors path="phone"/><br>
    <input type="submit" value="提交">
</form:form>
</body>
```



## 校验标签

### @Null

必须为 null

### @NotNull

不能为 null

### @Min（value）

必须是一个数字，且大于等于 value

### @Max（value）

必须是一个数字，且小于等于 value

### @Email

被注解的元素必须是电子邮箱地址

### @Pattern

被注解的元素必须符合正则表达式

### @Length

被注解的元素的大小必须在指定的范围内

### @NotEmpty

被注解的字符串的值必须非空



# 处理 @RespondBody 中文乱码

## 使用过滤器处理客户端到服务器的数据

在 web.xml 中配置过滤器

```xml
<!--使用过滤器解决中文乱码-->
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
```

## 使用消息转换器处理服务器到客户端的数据

在 springmvc.xml 中配置消息转换器

```java
<!--处理中文乱码-->
<mvc:annotation-driven>
    <!--消息转换器-->
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="supportedMediaTypes" value="text/html;charset=UTF-8"/>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```





# 拦截器

## 创建拦截器类实现 HandlerInterceptor 接口

![image-20210824124818139](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210824124818139.png)

```java
@Component
public class LoginInterceptor implements HandlerInterceptor {

    //目标方法执行之前
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session=request.getSession();

        Object loginUser=session.getAttribute("loginUser");
        if (loginUser!=null){
            return true;
        }

        request.setAttribute("msg", "请先登录");
        request.getRequestDispatcher("/").forward(request, response);
        return false;
    }

    //目标方法完成之后
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    //页面渲染以后
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
    }
}
```



## 配置拦截器要拦截的请求

![image-20210824124846344](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210824124846344.png)

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Autowired
    private LoginInterceptor loginInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(loginInterceptor)
                .addPathPatterns("/**")                         //要拦截的资源模式
                .excludePathPatterns("/index","/login","/css/**","/fonts/**","/images/**","/js/**")        //不拦截的资源模式
                .order(1);                  //执行顺序
    }
}
```

