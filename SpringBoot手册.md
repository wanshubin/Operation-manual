[TOC]



# Spring Boot

Spring Boot 是一个快速开发框架，可以迅速搭建出一套基于 Spring 框架体系的应用，是 Spring Cloud 的基础

Spring Boot 开启了各种自动装配，从而简化代码的开发，不需要编写各种配置文件，只需要引入相关依赖就可以迅速搭建起一个工程



## 特点

+ 不需要 web.xml
+ 不需要 springmvc.xml
+ 不需要 tomcat，Spring Boot 内嵌了 tomcat
+ 不需要配置 JSON 解析，支持 REST 架构
+ 个性化的配置，非常简单



# 使用 Spring Boot

## 项目结构

![image-20210816125529236](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210816125529236.png)



## 导入 Maven 依赖

```xml
<!--继承父包-->
<parent>
  <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-parent -->
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.3.0.RELEASE</version>
</parent>

<dependencies>
  <!--web启动jar-->
  <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
</dependencies>
```



## 创建实体类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    private Integer id;
    private String name;
    private Integer age;
}
```



## 创建 Repository 接口

```java
public interface StudentRepository {
    public Collection<Student> findAll();
    public Student findById(Integer id);
    public void saveOrUpdate(Student student);
    public void deleteById(Integer id);
}
```



## 实现 Repository

```java
@Repository
public class StudentRepositoryImpl implements StudentRepository {
    private static Map<Integer,Student> studentMap;

    static {
        studentMap=new HashMap<>();
        studentMap.put(1, new Student(1, "dsahk", 20));
        studentMap.put(2, new Student(2, "gfjlksn", 16));
        studentMap.put(3, new Student(3, "xzcj", 26));
        studentMap.put(4, new Student(4, "eaesk", 18));
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
        studentMap.put(student.getId(), student);
    }

    @Override
    public void deleteById(Integer id) {
        studentMap.remove(id);
    }
}
```



## 创建 Handler

```java
@Controller
@RequestMapping("/student")
public class StudentHandler {

    @Autowired
    private StudentRepository studentRepository;

    @ResponseBody
    @RequestMapping("/findAll")
    public Collection<Student> findAll(){
        return studentRepository.findAll();
    }

    @ResponseBody
    @GetMapping("/findById/{id}")
    public Student findById(@PathVariable("id") Integer id){
        return studentRepository.findById(id);
    }

    @ResponseBody
    @PostMapping("/save")
    public void save(@RequestBody Student student){
        studentRepository.saveOrUpdate(student);
    }

    @ResponseBody
    @PutMapping("/update")
    public void update(@RequestBody Student student){
        studentRepository.saveOrUpdate(student);
    }

    @ResponseBody
    @DeleteMapping("/deleteById/{id}")
    public void deleteById(@PathVariable("id") Integer id){
        studentRepository.deleteById(id);
    }
}
```



## 创建 SpringBoot 的启动类（入口）

类名可以自定义，但是**位置必须在其他业务类位置的父级下**

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```





## SpringBoot 和 Thymeleaf

SpringBoot 可以结合 Thymeleaf 模板来整合 HTML ，使用原生的 HTML 作为视图

Thymeleaf 模板是面向 Web 和独立环境的 Java 模板引擎，能够处理 HTML、XML、JavaScript、CSS等

```html
<p th:text="${message}"></p>
```



## 添加 Maven 依赖

```xml
<!--继承父包-->
<parent>
  <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-parent -->
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.3.0.RELEASE</version>
</parent>

<dependencies>
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
    <scope>test</scope>
  </dependency>
  <!--web启动jar-->
  <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
  </dependency>
  <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
  <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.20</version>
    <scope>provided</scope>
  </dependency>
</dependencies>
```



## 配置 application.yaml

```yaml
server:
  port: 8080


spring:
  thymeleaf:
    prefix: classpath:/templates/
    suffix: .html
    mode: HTML5
    encoding: utf-8
```



## 创建 HTML 并引入 th

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>index</title>
</head>
<body>
<div>
    <h1>welcome</h1>
</div>
<div>
    <table>
        <tr>
            <th>ID</th>
            <th>姓名</th>
            <th>年龄</th>
        </tr>
        <tr th:each="student:${list}">
            <td th:text="${student.id}"></td>
            <td th:text="${student.name}"></td>
            <td th:text="${student.age}"></td>
        </tr>
    </table>
</div>
</body>
</html>
```



## 如何能直接访问 HTML ？——static包

**SpringBoot 中只有在 static 包的 html才能被直接访问，static 包是 SpringBoot 的扫描范围**

如下 test.html可以直接访问，而 index.html不能

![image-20210816135722154](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210816135722154.png)





# Spring Initializer

可以使用 Initializer 根据内容来勾选要使用的模块

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210824093351927.png" alt="image-20210824093351927" style="zoom:67%;" />





# Thymeleaf 语法

## 赋值、拼接

```html
<p th:text="${name}"></p>
<p th:text="'学生姓名是'+${name}+2"></p>
<p th:text="|学生姓名是,${name}|"></p>
```

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210816140801474.png" alt="image-20210816140801474" style="zoom:67%;" />



## 条件判断	if/unless

```html
<p th:if="${flag==true}" th:text="${name}"></p>
<p th:unless="${flag!=true}" th:text="${name}"></p>
```



## 循环

```html
<div>
    <table>
        <tr>
            <th>ID</th>
            <th>姓名</th>
            <th>年龄</th>
        </tr>
        <tr th:each="student,stat:${list}" th:style="'background-color:'+@{${stat.odd}?'#F2F2F2'}">
            <td th:text="${student.id}"></td>
            <td th:text="${student.name}"></td>
            <td th:text="${student.age}"></td>
        </tr>
    </table>
</div>
```

stat 是状态变量，属性：

+ index —— 集合中元素的index（从0开始）
+ count —— 集合中元素的count（从1开始）
+ size —— 集合的大小
+ current —— 当前迭代变量
+ even/odd —— 当前迭代是否为偶数/奇数（从0开始计算）
+ first —— 当前迭代的元素是否是第一个
+ last —— 当前迭代的元素是否是最后一个



## url

Thymeleaf对于 URL 的处理时通过`@{...}`进行处理，结合 th:href、th:src

```html
<a th:href=“@{http://localhost:8080/index/{name}(name=${name})}”>跳转</a>
```



## src

在 handler 中添加 model 后

在页面中

```html
<img th:src="${src}">
```



## value

```
<input th:value="${age gt 30?'中年':'青年'}"/>
```



## switch

```html
<div th:switch="${gender}">
    <p th:case="女">女</p>
    <p th:case="男">男</p>
</div>
```



## 基本对象

+ `#ctx` —— 上下文对象
+ `#vars` —— 上下文变量
+ `#locale` —— 区域对象
+ `#request` —— HTTPServletRequest 对象
+ `#response` —— HTTPServletResponse 对象
+ `#session` —— HttpSession 对象
+ `#servletContext` —— ServletContext 对象

```
<p th:text="${#request.getAttribute('name')}"></p>
<p th:text="${#request.getAttribute('name')}"></p>
<p th:text="${#locale.country}"></p>
```



## 内置对象

可以直接通过 # 访问

+ dates —— 相当于 java.util.Date 的功能方法
+ calendars —— 相当于 java.util.Calendar 的功能方法
+ numbers —— 格式化数字
+ strings —— java.lang.String 的功能方法
+ objects —— Object 的功能方法

+ bools —— 对布尔求值的方法
+ arrays —— 操作数组的功能方法
+ lists —— 操作集合的功能方法
+ sets —— 操作集合的功能方法
+ maps —— 操作集合的功能方法

```
<p th:text="${#rdates.format(date,'yyyy-MM-dd HH:mm:ss')}"></p>
```





# SpringBoot 数据校验

## 引入 maven 依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

## 设置规则

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    @NotNull(message = "id不能为空")
    private Integer id;
    @NotEmpty(message = "姓名不能为空")
    @Length(min =6,max = 12,message = "姓名长度应该在6-12位")
    private String name;
    private Integer age;
}
```

## 使用校验

```java
@ResponseBody
@PostMapping("/save")
public void save(@Valid @RequestBody Student student){
    studentRepository.saveOrUpdate(student);
}
```





# SpringBoot 和 Mybatis

## 引入 Maven 依赖

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter -->
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>2.2.0</version>
</dependency>
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.26</version>
</dependency>
```

## 创建实体类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    @NotNull(message = "id不能为空")
    private Integer id;
    @NotEmpty(message = "姓名不能为空")
    @Length(min =6,max = 12,message = "姓名长度应该在6-12位")
    private String name;
    private Double score;
    private Date birthday;
}
```

## 创建 Repostiory 接口

```java
public interface StudentRepository {
    public Collection<Student> findAll();
    public Student findById(Integer id);
    public void saveOrUpdate(Student student);
    public void deleteById(Integer id);
}
```

## 创建 Mapper 映射配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace通常设置为Repository接口文件所在包+文件名的形式，如com.fury.mapper.AccountRepository-->
<mapper namespace="com.fury.repository.StudentRepository">
    <!-- <insert id=" " parameterType=" "></insert> -->
    <!-- <update id=" " parameterType=" "></update> -->
    <!-- <delete id=" " parameterType=" "></delete>  -->
    <select id="findAll" resultType="Student">
        select * from student;
    </select>

    <select id="findById" parameterType="integer" resultType="Student">
        select * from student where id=#{id};
    </select>

    <insert id="save" parameterType="Student">
        insert into student(name, password, score, birthday) values(#{name},#{password},#{score},#{birthday});
    </insert>
    
    <update id="update" parameterType="Student">
        update student set name=#{name},password=#{password},score=#{#score},birthday=#{birthday} where id = #{id};
    </update>

    <delete id="deleteById" parameterType="integer">
        delete from student where id = #{id};
    </delete>

</mapper>
```



## 创建 Mybatis 辅助配置

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



## 创建 Handler

```java
@Controller
@RequestMapping("/student")
public class StudentHandler {

    @Autowired
    private StudentRepository studentRepository;

    @ResponseBody
    @RequestMapping("/findAll")
    public List<Student> findAll(){
        return studentRepository.findAll();
    }

    @ResponseBody
    @GetMapping("/findById/{id}")
    public Student findById(@PathVariable("id") Integer id){
        return studentRepository.findById(id);
    }

    @ResponseBody
    @PostMapping("/save")
    public void save(@Valid @RequestBody Student student){
        studentRepository.save(student);
    }

    @ResponseBody
    @PutMapping("/update")
    public void update(@RequestBody Student student){
        studentRepository.update(student);
    }

    @ResponseBody
    @DeleteMapping("/deleteById/{id}")
    public void deleteById(@PathVariable("id") Integer id){
        studentRepository.deleteById(id);
    }
}
```



## 创建启动类

要添加 @MapperScan 注解

```java
@SpringBootApplication
@MapperScan("com.fury.repository")
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```



## 创建 application.yml 配置

```yaml
server:
  port: 8080
spring:
  thymeleaf:
    prefix:
      classpath: /templates/
    suffix: .html
    mode: HTML5
    encoding: utf-8
  datasource:
    url: jdbc:mysql://localhost:3719/ssm?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC
    username: wanshubin
    password: wanshubin0223
    driver-class-name: com.mysql.cj.jdbc.Driver
mybatis:
  mapper-locations: classpath:/mapping/*.xml
  config-location: classpath:mybatis-config-min.xml
```





# SpringBoot 和 JPA

Java Persistence API，Java 持久层 API

它可以将实体类和其中的属性直接与数据库表和其中的字段映射起来，是一个不同于 Mybatis 半自动化的全自动映射的框架

## 引入 Maven 依赖

```xml
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.26</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-jpa -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```



## 创建实体类

使用 @Entity 注解来使实体联系数据库表

```java
@Data
@Entity
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @NotNull(message = "id不能为空")
    private Integer id;

    @Column
    @NotEmpty(message = "姓名不能为空")
    @Length(min =6,max = 12,message = "姓名长度应该在6-12位")
    private String name;

    @Column
    private Double score;

    @Column
    private Date birthday;
}
```



## 创建 Repository

只需继承 JpaRepository 接口 ，其泛型参数为 返回类型 和 主键的类型

```java
public interface StudentRepositoryJPA extends JpaRepository<Student,Integer> {
    
}
```



## 创建 Handler

将会调用  JpaRepository 接口中的方法

```java
@RestController
@RequestMapping("/studentJPA")
public class StudentHandlerJPA {

    @Autowired
    private StudentRepositoryJPA studentRepository;

    @ResponseBody
    @RequestMapping("/findAll")
    public List<Student> findAll(){
        return studentRepository.findAll();
    }

    @ResponseBody
    @GetMapping("/findById/{id}")
    public Student findById(@PathVariable("id") Integer id){
        return studentRepository.findById(id).get();
    }

    @ResponseBody
    @PostMapping("/save")
    public void save(@Valid @RequestBody Student student){
        studentRepository.save(student);
    }

    @ResponseBody
    @PutMapping("/update")
    public void update(@RequestBody Student student){
        studentRepository.save(student);
    }

    @ResponseBody
    @DeleteMapping("/deleteById/{id}")
    public void deleteById(@PathVariable("id") Integer id){
        studentRepository.deleteById(id);
    }
}
```



## 配置 application.yml

```yaml
server:
  port: 8080
spring:
  thymeleaf:
    prefix:
      classpath: /templates/
    suffix: .html
    mode: HTML5
    encoding: utf-8
  datasource:
    url: jdbc:mysql://localhost:3719/ssm?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC
    username: wanshubin
    password: wanshubin0223
    driver-class-name: com.mysql.cj.jdbc.Driver
  jpa:
    show-sql: true
    properties:
      hibernate:
        format_sql: true
```



## 创建启动类

```
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```





# SpringBoot 和 MongoDB

## 引入 Maven 依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```



## 创建实体类

```java
@Data
@Document(collection = "student")
@AllArgsConstructor
@NoArgsConstructor
public class StudentMG {
    @NotNull(message = "id不能为空")
    private String id;

    @Field(value = "name")
    @NotEmpty(message = "姓名不能为空")
    @Length(min =6,max = 12,message = "姓名长度应该在6-12位")
    private String name;

    @Field(value = "age")
    private Integer age;
}
```



## 创建 Repository 接口

MongoDB 的 repository 同样也只需要继承 MongoRepository<T, K>，不需要编写内容

泛型参数 T 为返回值类型，K 为主键类型

```java
public interface StudentRepositoryMG extends MongoRepository<Student,String> {
}
```



## 创建 Handler

```java
@RestController
@RequestMapping("/studentMG")
public class StudentHandlerMG {
    @Autowired
    private StudentRepositoryMG studentRepository;

    @ResponseBody
    @RequestMapping("/findAll")
    public List<Student> findAll(){
        return studentRepository.findAll();
    }

    @ResponseBody
    @GetMapping("/findById/{id}")
    public Student findById(@PathVariable("id") Integer id){
        return studentRepository.findById(String.valueOf(id)).get();
    }

    @ResponseBody
    @PostMapping("/save")
    public void save(@Valid @RequestBody Student student){
        studentRepository.save(student);
    }

    @ResponseBody
    @PutMapping("/update")
    public void update(@RequestBody Student student){
        studentRepository.save(student);
    }

    @ResponseBody
    @DeleteMapping("/deleteById/{id}")
    public void deleteById(@PathVariable("id") Integer id){
        studentRepository.deleteById(String.valueOf(id));
    }
}
```



## 配置 application.yml

```yaml
server:
  port: 8080
spring:
  data:
    mongodb:
      database: student
      host: 127.0.0.1
      port: 27017
      username: wanshubin
      password: wanshubin0223
```



## 创建启动类

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```







# SpringBoot 和 Redis(SQL缓存)

Redis是以 key-value 键值对形式存储数据的数据库，常用来作为缓存

## 项目结构

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210825123604440.png" alt="image-20210825123604440" style="zoom:80%;" />



## 添加 Maven 依赖

```xml
<!--继承父包-->
  <parent>
    <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-parent -->
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.0.RELEASE</version>
  </parent>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.apache.commons/commons-pool2 -->
    <dependency>
      <groupId>org.apache.commons</groupId>
      <artifactId>commons-pool2</artifactId>
      <version>2.11.0</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.20</version>
      <scope>provided</scope>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter -->
    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>2.2.0</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.26</version>
    </dependency>
    <dependency>
      <groupId>tk.mybatis</groupId>
      <artifactId>mapper-spring-boot-starter</artifactId>
      <version>2.1.5</version>
    </dependency>
    <dependency>
      <groupId>jakarta.persistence</groupId>
      <artifactId>jakarta.persistence-api</artifactId>
      <version>2.2.3</version>
    </dependency>
  </dependencies>
```



## 创建 application.yml 配置

```yaml
server:
  port: 8080
spring:
  redis:
    database: 0
    host: localhost
    port: 6379
```



## 创建 Mybatis 辅助配置

```xml
<configuration>

    <settings>
        <!--开启日志 logImpl：控制日志 STDOUT_LOGGING：把日志输出到控制台上-->
        <setting name="logImpl" value="STDOUT_LOGGING"/>
        <!--开启延迟加载-->
        <setting name="lazyLoadingEnabled" value="true"/>
        <!--开启二级缓存-->
        <setting name="cacheEnabled" value="true"/>
        <!--驼峰和下划线名称的转换-->
        <!-- <setting name="mapUderscoreToCamelCase" value="true"/> -->
    </settings>

    <!--指定一个包名，Mybatis会在包名下搜索需要的JavaBean-->
    <typeAliases>
        <package name="com.fury.entity"/>
    </typeAliases>
</configuration>
```



## 添加 Redis 序列化配置

```java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory redisConnectionFactory){
        RedisTemplate<String,Object> redisTemplate=new RedisTemplate<>();
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer=new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectMapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));
        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);
        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        return redisTemplate;
    }
}
```



## 创建实体类

因为 redis 中没有映射关系，所以不需要注解，但是要实现序列化接口

```java
@Data
public class Student implements Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;
    private String name;
    private String password;
    private Double score;
    private Date birthday;
}
```



## 创建 Mybatis 的 Repository

```java
@Repository
public interface StudentRepository{
    public void addStudent(Student student);
    public Student findStudentById(Integer id);
}
```



## 创建 Mapper

```xml
<mapper namespace="com.fury.repository.StudentRepository">
    <!-- <insert id=" " parameterType=" "></insert> -->
    <!-- <update id=" " parameterType=" "></update> -->
    <!-- <delete id=" " parameterType=" "></delete>  -->
    <insert id="addStudent" parameterType="Student">
        insert into student(name, password, score, birthday) values(#{name},#{password},#{score},#{birthday});
    </insert>
    <select id="findStudentById" parameterType="integer" resultType="Student">
        select * from student where id = #{id};
    </select>
</mapper>
```



## 创建 Service 接口

```java
public interface StudentService {
    public void addStudent(Student student);
    public Student findStudentById(Integer id);
}
```



## 实现 Service 并注入 RedisTemplate

redis已经有了 RedisTemplate<> 模板，所以不需要 repository 模块，只需要将模板注入

这里是运用逻辑来进行redis缓存的判断和操作

```java
@Service
@Slf4j
public class StudentServiceImpl implements StudentService {
    @Autowired
    private StudentRepository studentRepository;
    @Autowired
    private RedisTemplate redisTemplate;

    @Override
    public void addStudent(Student student) {
        studentRepository.addStudent(student);
        String key="user:"+student.getId();
        redisTemplate.opsForValue().set(key,student);
    }

    @Override
    public Object findStudentById(Integer id) {
        //从缓存中获取数据，key的规则为 user：{id}
        Object object=redisTemplate.opsForValue().get("user:"+id);
        //如果缓存中有数据则从缓存中返回
        //如果没有则查询sql，并将数据设置到缓存
        //使用双重检测，先检测取到的对象是否为空，如果为空就进入等待队列，然后执行的线程会再判断是否redis内已有对象，并且将状态更新到队列中的所有线程
        if (object==null){
            synchronized (this.getClass()) {
                object=redisTemplate.opsForValue().get("user:"+id);
                if(object==null){
                    log.debug("------》查询数据库-----------");
                    Student student=studentRepository.findStudentById(id);
                    String key="user:"+student.getId();
                    redisTemplate.opsForValue().set(key,student);
                    return student;
                }else {
                    log.debug("------》查询缓存XXXXXXXXXXXX");
                    return object;
                }
            }
        }else {
            log.debug("------》查询缓存XXXXXXXXXXXX");
            return object;
        }
    }
}
```



## 创建 Handler

```java
@RestController
@RequestMapping("/student")
public class StudentHandler {

    @Autowired
    private StudentService studentService;

    @PostMapping("/add")
    public void addStudent(@RequestBody Student student){
        studentService.addStudent(student);
    }

    @GetMapping("/find/{id}")
    public Object fingStudentById(@PathVariable("id") Integer id){
        return studentService.findStudentById(id);
    }
}
```



## 创建启动类并启动

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```





# SpringBoot 和 Spring Security

## 使用 Security

### 添加 Maven 依赖

```xml
<!--继承父包-->
<parent>
  <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-parent -->
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.3.0.RELEASE</version>
</parent>

<dependencies>
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
    <scope>test</scope>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
  </dependency>
</dependencies>
```



### 创建 Handler

```java
@Controller
public class HelloHandler {

    @GetMapping("/index")
    public String index(){
        return "index";
    }
}
```



### 创建 HTML

在 resources 中创建 templates 包并创建 html



### 创建 application.yml 配置

```yaml
server:
  port: 8080
spring:
  thymeleaf:
    prefix:
      classpath: /templates/
    suffix: .html
    mode: HTML5
    encoding: utf-8
  security:
    user:
      name: admin
      password: 123123
```



### 创建启动类



### 启动后进入首页，security 会自动跳转到登录页





## 权限管理

定义两个 HTML 资源：index.html 和 admin.html，同时定义两个角色 ADMIN 和 USER，ADMIN 可以访问 两个文件， USER 只能访问 index.html

### 创建 SecurityConfig 配置类

继承 WebSecurityConfigurerAdapter 方法，并添加注解 @Configuration 和 @EnableWebSecurity

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication().passwordEncoder(new MyPasswordEncoder())
                .withUser("user").password(new MyPasswordEncoder().encode("000"))
                    .roles("USER")
                .and()
                .withUser("admin").password(new MyPasswordEncoder().encode("456")).roles("ADMIN","USER");
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests().antMatchers("/admin").hasRole("ADMIN")
                .antMatchers("/index").access("hasRole('ADMIN') or hasRole('USER')")
        .anyRequest().authenticated()
        .and()
        .formLogin().loginPage("/login")
        .permitAll()
        .and()
        .logout()
        .permitAll()
        .and()
        .csrf()
        .disable();
    }
}

```



### 创建 Handler

```java
@Controller
public class HelloHandler {

    @GetMapping("/index")
    public String index(){
        return "index";
    }

    @GetMapping("/admin")
    public String admin(){
        return "admin";
    }

    @GetMapping("/login")
    public String login(){
        return "login";
    }
}
```



### 创建 HTML

login.html

**两个输入框的 name 必须是 username 和 password，否则不能验证**

```html
<!DOCTYPE html>
<html lang="en">
<html xml:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>login</title>
</head>
<body>
<form th:action="@{/login}" method="post">
  用户名：<input type="text" name="username"><br>
  密码：<input type="text" name="password"><br>
  <input type="submit" value="登录">
</form>
</body>
</html>
```

admin.html

index.html





# Axios 传输数据

