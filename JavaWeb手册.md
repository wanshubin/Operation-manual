[TOC]



# 使用JSP的3种方式

+ JSP脚本，用来**执行Java逻辑代码**

```jsp
<% Java代码 %>
```

+ JSP声明，用来**定义Java方法**

```jsp
<%!
	声明Java方法
%>
```

+ JSP表达式，**将Java对象直接输出到HTML页面中**

```jsp
<%= Java变量 %>
```



```jsp
<%
    String str = "哈哈哈";
%>

<%!
    public String test(){
        return "HelloWorld";
    }
%>

<%=str%>
```





# JSP内置对象

+ **request** —— 表示一次**请求**，HttpServletRequest
+ **response** —— 表示一次**响应**，HttpServletResponse
+ **pageContext** —— **页面上下文**，获取页面信息，PageContext
+ **session** —— 表示一次**会话**，**保存用户信息**，HttpSession
+ **application** —— 表示**当前的 Web 应用**，全局对象，**保存所有用户的共享信息**，ServletContext
+ **config** —— 表示当前 JSP 对应的 Servlet 的 **ServletConfig对象**，获取当前 Servlet 的信息
+ **out** —— **向浏览器输出数据**，JspWriter
+ **page** —— 当前 JSP对应的 **Servlet 对象**，Servlet
+ **exception** —— 表示 JSP 页面发生的**异常**，Exception



**常用 request、 response、 session、 application、 pageContext**



# request常用方法

|                                                    方法 |                             描述 |
| ------------------------------------------------------: | -------------------------------: |
|                     String **getParameter(String key)** |         **获取客户端传来的参数** |
|                       String[] **getParameterValues()** | **获取客户端传来的多个同名参数** |
|         void **setAttribute(String key, Object value)** |     **通过键值对的形式保存数据** |
|                     Object **getAttribute(String key)** |          **根据 key 取出 value** |
| RequestDispatcher **getRequestDispatcher(String path)** |                 用于**转发请求** |
|           void **setCharacterEncoding(String charset)** |           指定每个请求的**编码** |





# HTTP状态码

+ 200 —— 正常
+ 404 —— 资源找不到
+ 400 —— 请求类型不匹配
+ 500 —— java抛出异常





# response常用方法

|                      方法 |   描述 |
| ------------------------: | -----: |
| sendRedirect(String path) | 重定向 |



# 转发 getRequestDispatcher 和重定向 sendRediect 的区别

转发是将同一个请求传递给下一个页面

重定向是创建一个新的请求传递给下一个页面，之前的请求的生命周期结束

**转发：同一个请求在服务器之间传递，地址栏不变，也叫服务器跳转**

**重定向：由客户端发送一次新的请求来访问跳转后的目标资源，地址栏会改变，也叫客户端跳转**

如果两个页面之间需要通过request来传值，则必须使用转发，不能使用重定向







## 实现会话状态的两种方式

+ session
+ cookie





# session

用户会话

服务器无法识别每一次HTTP请求的出处（不知道来自哪一个终端），它只会接收一个请求信号，所以就存在一个问题：将用户的响应发送给其他人，必须有一种技术来让服务器知道请求来自于哪里，这就是会话技术

## 会话

客户端和服务器之间发生的一系列请求和响应的过程，打开浏览器到关闭浏览器之间的过程

## 会话状态

指服务器和浏览器在会话过程中产生的状态信息，借助于会话状态，服务器能够把属于同一次会话的一系列请求和响应关联起来

## sessionID

属于同一次会话的请求都有一个相同的标识符，sessionID

## session常用方法

|                                            方法 |                                描述 |
| ----------------------------------------------: | ----------------------------------: |
|                              String **getId()** |                   **获取sessionID** |
|   void **setMaxInactiveInterval(int interval)** | **设置session的失效时间**，单位为秒 |
|                int **getMaxInactiveInterval()** |       **获取当前session的失效时间** |
|                            int **invalidate()** |             **设置session立即失效** |
| void **setAttribute(String key, Object value)** |      **通过键值对的形式来存储数据** |
|             Object **getAttribute(String key)** |              **通过键获取对应数据** |
|            void **removeAttribute(String key)** |            **通过键删除对应的数据** |





# cookie

cookie是服务端在HTTP响应中附带传给浏览器的一个小的文本文件，一旦浏览器保存了某个cookie，在之后的请求和响应过程中，会将此cookie来回传递，这样就可以通过cookie这个载体完成客户端和服务端的数据交互

## 创建cookie

```java
Cookie cookie = new Cookie("name", name);
cookie.setMaxAge(200);					//设置cookie生命周期，默认是-1，即关闭浏览器就清除
response.addCookie(cookie);
```

## 读取cookie

```
Cookie[] cookies = request.getCookies();
for(Cookie cookie : cookies){
	out.write(cookie.getName()+" "+cookie.getValue());
}
```

## cookie常用方法

|                        方法 |                               描述 |
| --------------------------: | ---------------------------------: |
| void **setMaxAge(int age)** | **设置cookie的有效时间**，单位为秒 |
|         int **getMaxAge()** |           **获取cookie的有效时间** |
|        String **getName()** |               **获取cookie的name** |
|       String **getValue()** |              **获取cookie的value** |



# session 和 cookie 的区别

session：保存在服务器

​	保存的数据是Object

​	会随着会话的结束而销毁

​	保存重要信息



cookie：保存在客户端

​	保存的数据是String

​	可以长期保存在浏览器中，与会话无关

​	保存不重要信息



存储用户信息：

session：   setAttribute(name, “admin”)		存

​					getAttribute(name)						取

​					session.invalidate()						退出登录

​	生命周期：服务端只要web应用重启就销毁，客户端浏览器关闭就销毁



cookie：	 respon.add(new Cookie(name, “admin”))		存

```jsp
<%
  Cookie[] cookies=request.getCookies();
  for (Cookie cookie:cookies){
    if(cookie.getName().equals("name")){
%>
    欢迎用户：<%=cookie.getValue()%><br />
<%
    }
  }
%>										 //取
```

​					setMaxAge(0)													退出登录

​	生命周期：不随服务端的重启而销毁，在客户端默认关闭浏览器就销毁，但是可以通过 setMaxAge() 方法设置有效期来控制销毁时间





# JSP内置对象作用域

+ **page** —— 对应的内置对象是 **pageContext** —— **在当前页面有效**
+ **request** —— 对应的内置对象是 **request** —— **在一次请求内有效**
+ **session** —— 对应的内置对象是 **session** —— **在一次会话内有效**
+ **application** —— 对应的内置对象是 **application** —— **对整个web应用有效**

它们都能用来传输数据，都有 setAttribute() 和 getAttribute() 方法

page < request < session < application



## 网站访问量统计

```jsp
<%
  Integer count = (Integer) application.getAttribute("count");
  if (count==null){
    count=1;
    application.setAttribute("count", count);
  }else {
    count++;
    application.setAttribute("count", count);
  }
%>
```





# EL表达式

Expression Language 表达式语言，替代JSP页面中数据访问时的复杂编码

**${变量名}**						变量名是 setAttribute 对应的 key 值

只要用 setAttribute() 存储在域对象（pageContext、request、session、application）中的变量都可以使用EL表达式读取

## EL表达式不起作用？

如果显示${msg}类似的内容，可能是没有对EL表达式进行解析

在JSP中添加

```jsp
<%@page isELIgnored="false" %>
```

## EL对于4种域对象的查找顺序

pageContext -> request -> session -> application

按上述顺序查找，找到立即返回，如果都没有找到则返回null

## 指定作用域查找

以变量 name 为例

**pageContext** —— ${pageScope.name}

**request** —— ${requestScope.name}

**session** —— ${sessionScope.name}

**application** —— ${applicationScope.name}

## 与方法绑定	和key相关

${user.num}

这个表达式的取值过程是将num变为Num，然后再寻找getNum方法来进行取值

也就是说表达式中的变量并不与java属性绑定，而是与get、set方法绑定



并且这个 user 是 setAttribute 中的 key

```
request.setAttribute("user",user);
```



## 另一种格式

${user[“id”]}

## EL执行表达式

&& || ! > < == >= <=

进行逻辑运算，返回true或false，要写在一个${}中

| 符号 | 单词 |
| ---- | ---- |
| &&   | and  |
| \|\| | or   |
| !    | not  |
| ==   | eq   |
| !=   | ne   |
| <    | lt   |
| >    | gt   |
| <=   | le   |
| >=   | ge   |



empt		判断是否为空，变量为null是空、长度为0的String是空、size为0的集合是空





# JSTL

JSP Standard Tag Library				JSP标准标签库

它是JSP为开发者提供的一系列的标签，使用这些标签可以完成一些逻辑处理，比如循环遍历集合，让代码更加简洁，不再出现JSP脚本穿插的情况

实际开发中 EL 和 JSTL 结合起来使用，JSTL 侧重于逻辑处理，EL 负责展示

## JSTL的使用

1. 导入jar包

+ **jstl.jar**

```xml
<!-- https://mvnrepository.com/artifact/javax.servlet/jstl -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
```

+ **standard.jar**

```xml
<!-- https://mvnrepository.com/artifact/org.apache.taglibs/taglibs-standard-impl -->
<dependency>
    <groupId>org.apache.taglibs</groupId>
    <artifactId>taglibs-standard-impl</artifactId>
    <version>1.2.5</version>
    <scope>runtime</scope>
</dependency>
```



2. 在JSP页面开始的地方导入 JSTL 标签库

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
```



3. 在需要的地方使用

```jsp
<c:forEach items="${list}" var="user">
    <tr>
        <td>${user.id}</td>
        <td>${user.name}</td>
        <td>${user.score}</td>
        <td>${user.address.name}</td>
    </tr>
</c:forEach>
```



常用标签：

+ set —— 向域对象中添加数据
+ out —— 输出域对象中的数据
+ remove —— 删除域对象中的数据
+ catch —— 用来捕获异常



条件标签：

+ if

+ choose





# 过滤器 Filter

用来拦截传入的请求和传出的响应

修改或以某种方式处理正在客户端和服务端之间交换的数据流

## 使用过滤器

使用方式与Servlet相似，Filter是java web提供的一个接口，开发者只需要自定义一个类并且实现该接口即可

```java
@WebFilter(filterName = "CharacterFilter",
        urlPatterns = {"/login","/test"})					//匹配的目标url
public class CharacterFilter implements Filter {
    public void init(FilterConfig config) throws ServletException {
    }

    public void destroy() {
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        chain.doFilter(request, response);					//处理过滤请求
    }
}
```

## Filter的生命周期

当Tomcat启动时，通过反射机制调用Filter的无参构造函数创建实例化对象，同时调用init方法实现初始化，doFilter方法调用多次，当Tomcat服务关闭的时候，调用destroy来销毁Filter对象

## 多个Filter的执行顺序

同时配置多个Filter，Filter的调用顺序是由web.xml中的配置顺序来决定的，写在上面的配置先调用，因为web.xml是从上到下顺序读取的

## Filter的使用场景

+ 中文乱码
+ 屏蔽敏感词

```java
request.setCharacterEncoding("UTF-8");
String name=request.getParameter("name");
name =name.replaceAll("万", "*");
request.setAttribute("name", name);
chain.doFilter(request, response);
```

+ 控制资源访问权限





# 文件的上传和下载

+ JSP
  + input 标签，其 type 为 file
  + 表单的 method 设置为 post
  + 表单的 enctype 设置为 multipart/form-data ，以二进制的形式传输数据
+ Servlet
  + fileupload组件可以将所有的请求信息都解析成FileItem对象，可以通过对FileItem对象的操作完成上传
  + 使用io组件

在maven中

```xml
<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.4</version>
</dependency>
<!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.11.0</version>
</dependency>
```



# AJAX

Asychronous JavaScript And XML				异步的 JavaScript 和 XML

AJAX 是一种交互方式，异步加载，客户端和服务器的数据交互更新在局部页面的技术，只做局部刷新，不需要刷新整个页面

优点：

+ 局部刷新，效率更高
+ 用户体验更好



## 基于JQuery的AJAX

引入JQuery

```jsp
<script type="text/javascript" src="https://cdn.staticfile.org/jquery/3.6.0/jquery.min.js"></script>
```



## 使用AJAX

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <script type="text/javascript" src="https://cdn.staticfile.org/jquery/3.6.0/jquery.min.js"></script>
    <script type="text/javascript">
        $(function (){
            var btn=$("#btn");
            btn.click(function (){					//设置按钮点击
                $.ajax({							//使用ajax
                    url:'/AJAXServlet',				//ajax请求地址
                    type:'post',					//ajax请求方式
                    data:'id=1',					//ajax请求参数
                    dataType:'text',				//从服务器接收的数据的类型
                    success:function (data){		//成功动作
                        var text =$("#text")		//创建对象并绑定到id上
                        text.before("<span>"+data+"</span><br />")		//添加元素
                    }
                });
            });
        })
    </script>
</head>
<body>
    ${str}
    <input id="text" type="text" />
    <br />
    <input id="btn" type="button" value="提交" />
</body>
</html>
```

```java
@Override
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    String id =request.getParameter("id");
    try {
        Thread.sleep(3000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    String str= "hello world";
    response.getWriter().write(str);				//这里respon返回的数据就是data
}
```



## 传统数据交互和AJAX对比

|      类别      |                           传统方式                           |                AJAX                |
| :------------: | :----------------------------------------------------------: | :--------------------------------: |
| 客户端请求方式 |                      浏览器发送同步请求                      |      异步引擎对象发送异步请求      |
| 服务器响应方式 |                     响应一个完整JSP页面                      |           响应需要的数据           |
| 客户端处理方式 | 需要等待服务器响应并且重新加载整个页面之后，用户才能进行后续操作 | 动态更新页面，不影响用户的其他操作 |



## AJAX原理

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210731122459853.png" alt="image-20210731122459853" style="zoom:80%;" />



## 基于JQuery的AJAX语法

**$.ajax({属性})**

常用的属性参数

+ **url** —— 请求的后端服务地址
+ **type** —— 请求方式，默认为 get
+ **data** —— 请求参数
+ **datatype** —— 服务器返回的数据类型

+ **success** —— 请求成功的回调函数
+ **error** —— 请求失败的回调函数
+ **complete** —— 请求完成的回调函数（无论成功还是失败都会调用）



## JSON

JavaScript Object Notation，一种轻量级数据交互格式，完成 js 与 java 等后端开发语言对象数据之间的转换

客户端和服务器之间传递对象数据需要用 JSON 格式

## 使用JSON

在maven中

```xml
<dependency>
    <groupId>org.json</groupId>
    <artifactId>json</artifactId>
    <version>20210307</version>
</dependency>
```





# JDBC

Java DataBase Connectivity				一个独立于特定数据库的管理系统，通用的SQL数据库存取和操作的公共接口

定义了一组标准，为访问不同的数据库提供了通用途径



## JDBC体系结构

JDBC接口包括两个层面

+ 面向应用的API，供程序员调用
+ 面向数据库的API，供数据库厂商使用
+ 

## JDBC的使用

1. 加载数据库驱动，Java程序和数据库之间的桥梁
2. 获取 Connection ，Java程序与数据库的一次连接
3. 创建 Statement 对象，由 Connection 产生，执行 SQL 语句
4. 如果需要接收返回值，创建ResultSet对象，保存 Statement 执行之后所查询到的结果

```java
public class JDBCPrac {
    public static void main(String[] args) {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            String url ="jdbc:mysql://localhost:3719/nbaplayerdata?useUnicode=true&characterEncoding=UTF-8";
            String user = "wanshubin";
            String password = "wanshubin0223";
            Connection connection= DriverManager.getConnection(url,user,password);
            String sql = "insert into nbaplayerdata(playerimg,playerteam,playername,playernumber,playerjob,playertall,playerweight,playerbirthday,playercont,playersal) " +
                    "value('insagad','自由球员','万树彬','23','G','1米85/6尺1','85公斤','2000-02-23','不适用','不适用')";
            Statement statement=connection.createStatement();
            System.out.println(statement.executeUpdate(sql));
            String query="select * from nbaplayerdata.nbaplayerdata where playername='万树彬';";
            Statement statement1=connection.createStatement();
            ResultSet resultSet=statement1.executeQuery(query);
            while (resultSet.next()){
                String playerimg =resultSet.getString("playerimg");
                String playerteam =resultSet.getString("playerteam");
                String playername =resultSet.getString("playername");
                String playernumber =resultSet.getString("playernumber");
                String playerjob =resultSet.getString("playerjob");
                String playertall =resultSet.getString("playertall");
                String playerweight =resultSet.getString("playerweight");
                String playerbirthday =resultSet.getString("playerbirthday");
                String playercont =resultSet.getString("playercont");
                String playersal =resultSet.getString("playersal");
                System.out.println(playerimg+playerteam+playername+playernumber+playerjob+playertall+playerweight+playerbirthday+playercont+playersal);
            }
        } catch (ClassNotFoundException | SQLException e) {
            e.printStackTrace();
        }
    }
}
```



## PreparedStatement

Statement的子类，提供了 SQL 占位符的功能

使用 Statement 进行开发有两个问题：

+ 需要频繁拼接 String 字符串，出错率较高
+ 存在 SQL 注入的风险

```java
public class JDBCPrac {
    public static void main(String[] args) {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            String url ="jdbc:mysql://localhost:3719/nbaplayerdata?useUnicode=true&characterEncoding=UTF-8";
            String user = "wanshubin";
            String password = "wanshubin0223";
            Connection connection= DriverManager.getConnection(url,user,password);

            PreparedStatement preparedStatement =connection.prepareStatement("select * from nbaplayerdata.nbaplayerdata where playername= ? ;");		// ？为占位符
            preparedStatement.setString(1, "万树彬");				 // 设置第n个占位符的值
            ResultSet resultSet=preparedStatement.executeQuery();
            while (resultSet.next()){
                String playerimg =resultSet.getString("playerimg");
                String playerteam =resultSet.getString("playerteam");
                String playername =resultSet.getString("playername");
                String playernumber =resultSet.getString("playernumber");
                String playerjob =resultSet.getString("playerjob");
                String playertall =resultSet.getString("playertall");
                String playerweight =resultSet.getString("playerweight");
                String playerbirthday =resultSet.getString("playerbirthday");
                String playercont =resultSet.getString("playercont");
                String playersal =resultSet.getString("playersal");
                System.out.println(playerimg+playerteam+playername+playernumber+playerjob+playertall+playerweight+playerbirthday+playercont+playersal);
            }
        } catch (ClassNotFoundException | SQLException e) {
            e.printStackTrace();
        }
    }
}
```





# 数据库连接池

JDBC中数据库连接对象是通过 DriverManager 来获取的，每次获取都需要向数据库申请获取连接，验证用户名和密码，执行完 SQL 语句后断开连接，这样的方式会造成资源浪费，数据库连接资源没有得到很好的重复利用

数据库连接池的基本思想就是为数据库建立一个缓冲池，预先向缓冲池中放入一定数量的连接对象，当需要获取数据库连接对象的时候，只需要从缓冲池中取出一个对象，用完之后再放回缓冲池中，供下一次请求使用，实现了资源的重复利用，允许程序重复使用一个现有的数据库连接对象，而不需要重新创建

## 数据库连接池实现

JDBC的数据库连接池使用 javax.sql.DataSource 接口来完成的，DataSource 是 Java 官方提供的接口，在使用时，开发者可以使用第三方工具

C3P0 是一个常用的第三方实现，实际开发中直接使用C3P0即可完成数据库连接池的操作

**maven配置**

```xml
<!-- https://mvnrepository.com/artifact/com.mchange/c3p0 -->
<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.5.5</version>
</dependency>
```

**xml配置**

**配置文件的名字必须是 c3p0-config.xml**

**初始化 ComboPooledDataSource 时，传入的参数必须是 c3p0-config.xml 中 named-config 标签的 name 属性值**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--此文件名必须叫 c3o0-config ！！！-->
<c3p0-config>
    <named-config name="c3p0_conf">
        <!--数据源基本属性-->
        <property name="user">wanshubin</property>
        <property name="password">wanshubin0223</property>
        <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3719/ssm?useUnicode=true&amp;characterEncoding=utf-8&amp;serverTimezone=UTC</property>

        <!--当连接对象不够时，再次申请的连接个数-->
        <property name="acquireIncrement">5</property>
        <!--初始状态的连接数-->
        <property name="initialPoolSize">20</property>
        <!--连接的临界值，允许的池中最小剩余连接数，当达到此值时，开始再次申请连接-->
        <property name="minPoolSize">2</property>
        <!--设置最大连接个数-->
        <property name="maxPoolSize">40</property>
    </named-config>
</c3p0-config>
```

**Utils**

```java
/*实现了数据与代码的分离，实现了解耦*/
package com.fury.util;

import com.mchange.v2.c3p0.ComboPooledDataSource;

import javax.sql.DataSource;
import java.sql.*;


public class C3P0Utils {
    private static DataSource dataSource;

    static {
        dataSource=new ComboPooledDataSource("c3p0_conf");
    }

    //连接数据库封装
    public static Connection getConnection() throws Exception{
        return dataSource.getConnection();
    }

    //关闭数据库封装，无结果集的写入
    public static void closeResource(Connection connection, Statement statement){

        //关闭PrepareStatement流
        try {
            if(statement!=null){
                statement.close();
            }
        }catch (SQLException e){
            e.printStackTrace();
        }

        //关闭connection流
        try{
            if(connection!=null){
                connection.close();
            }
        }catch (SQLException e){
            e.printStackTrace();
        }
    }

    //关闭数据库封装，有结果集的查询
    public static void closeResource(Connection connection, Statement statement, ResultSet resultSet){
        //关闭PrepareStatement流
        try {
            if(statement!=null){
                statement.close();
            }
        }catch (SQLException e){
            e.printStackTrace();
        }

        //关闭connection流
        try{
            if(connection!=null){
                connection.close();
            }
        }catch (SQLException e){
            e.printStackTrace();
        }

        //关闭ResultSet
        try {
            if (resultSet!=null){
                resultSet.close();
            }
        }catch (SQLException e){
            e.printStackTrace();
        }
    }
}
```







# DBUtils

DBUtils可以帮助开发者完成数据的封装（结果集到Java对象的映射）

导入common-dbutils

maven

```xml
<!-- https://mvnrepository.com/artifact/commons-dbutils/commons-dbutils -->
<dependency>
    <groupId>commons-dbutils</groupId>
    <artifactId>commons-dbutils</artifactId>
    <version>1.7</version>
</dependency>
```



## ResultHandler

ResultHandler 接口用来处理结果集，可以将查询到的结果集转换成 Java 对象，提供了 4 种实现类

+ **BeanHandler** —— 将结果集映射成 Java 对象
+ **BeanListHandler** —— 将结果集映射成 List 集合，如 List<Student>
+ **MapHandler** —— 将结果集映射成 Map 对象
+ **MapListHandler** —— 将结果集映射成 MapList 集合



## 使用DBUtils查询

**原型类要有无参构造函数，否则无法通过反射创建对象**

**原型类的 set方法名 要和 数据库的字段名 一致**

```java
public static List<Student> findByIdWithDBUtils(Integer id_temp){
    Connection connection;
    List<Student> studentArrayList = new ArrayList<>();
    try {
        connection=C3P0Utils.getConnection();
        String sql="select * from student where id = ? and name = ?";
        QueryRunner queryRunner = new QueryRunner();
        studentArrayList=queryRunner.query(connection, sql,new BeanListHandler<>(Student.class),id_temp,"王二");			//Student类要有无参构造函数，否则无法通过反射创建对象
    } catch (Exception e) {
        e.printStackTrace();
    }
    return studentArrayList;
}
```





# MVC

一种开发模式，将程序分层的一种思想

+ M —— Model 业务数据（service 处理业务、repository 与数据库交互、entity 将数据映射成 java 对象）
+ V —— View 视图（JSP、HTML、App...）
+ C —— Controller 控制（Servlet、Handler、Action）

请求进入 JavaWeb 应用后，Controller 接收该请求，进行业务逻辑处理，最终将处理结果再返回给用户（View + Model）



Controller -> Service -> Repository ---> DataBase ，此流程借助 entity 传输

请求进入 Controller 中，进行业务处理，从 Controller 中将 Model 带到 View 中返回给用户



