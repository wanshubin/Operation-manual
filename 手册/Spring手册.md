[TOC]



# Spring概述

Spring是一个企业级开发框架，是软件设计层面的框架，优势在于可以将应用程序进行分层，开发者可以自主选择组件

MVC：Struts2、Spring MVC

ORMapping：Hibernate、MyBatis、Spring Data



# Spring的优点

+ 低侵入式设计
+ 独立于各种应用服务器
+ 依赖注入特性将组件关系透明化，降低了耦合度
+ 面向切面编程特性允许将通用任务进行集中式处理
+ 与第三方框架的良好整合



# Spring框架两大核心机制	IOC、AOP

+ IOC（控制反转）/ DI（依赖注入）
+ AOP （面向切面编程）



# IOC	控制反转

传统的程序开发中，需要调用对象时，通常由调用者来创建被调用者的实例，即对象是由调用者主动new出来的

但是在 Spring 框架中创建对象的工作不再由调用者来完成，而是交给 IOC 容器来创建，再推送给调用者，整个流程完成反转



## IOC底层原理

+ 读取配置文件，解析xml
+ 通过反射机制实例化配置文件中所配置的所有的bean

## 使用 IOC

### 添加Maven依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.3.9</version>
</dependency>
```

### 创建实体类Student

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    private Long id;
    private String name;
    private Integer age;
}
```



### 通过无参构造函数创建对象，在xml配置文件配置添加需要管理的对象

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="student" class="com.fury.entity.Student">
        <property name="id" value="1"/>
        <property name="name" value="张三"/>
        <property name="age" value="25"/>
    </bean>
</beans>
```

### 通过有参构造函数创建对象，在xml配置文件配置添加需要管理的对象

```xml
<bean id="student2" class="com.fury.entity.Student">
    <constructor-arg name="id" value="3"/>
    <constructor-arg name="name" value="张四"/>
    <constructor-arg name="age" value="18"/>
    <constructor-arg name="address" ref="address"/>
</bean>
```

或通过下标

```xml
<bean id="student2" class="com.fury.entity.Student">
    <constructor-arg index="0" value="3"/>
    <constructor-arg index="1" value="张四"/>
    <constructor-arg index="2" value="18"/>
    <constructor-arg index="3" ref="address"/>
</bean>
```

### 为bean对象注入集合

```xml
<bean id="student" class="com.fury.entity.Student">
    <property name="id" value="1"/>
    <property name="name" value="张三"/>
    <property name="age" value="25"/>
    <property name="address">
        <list>
            <ref bean="address"/>
            <ref bean="address2"/>
        </list>
    </property>
</bean>
```



### 从IOC中获取对象

```java
ApplicationContext applicationContext=new ClassPathXmlApplicationContext("spring.xml");
Student student= (Student) applicationContext.getBean("student");
```

### 或者通过运行实例获取对象	不推荐使用

使用这种方式，配置文件中一个数据类型的对象只能有一个实例，否则会抛出异常，因为没有唯一的bean，所以不推荐使用

```java
ApplicationContext applicationContext=new ClassPathXmlApplicationContext("spring.xml");
Student student= (Student) applicationContext.getBean(Student.class);
```





## 配置文件

+ 通过配置 `<bean>` 标签来完成对象的管理
  + `id` —— 对象名
  + `class` —— 对象的模板类，**所有交给IOC容器来管理的类必须有无参构造方法**，因为Spring底层是通过反射机制来创建对象的，反射机制调用的是无参构造

+ 对象成员变量通过 `<property>` 标签完成赋值

  + `name` —— 成员变量名
  + `value` —— 成员变量值，基本数据类型、 String 可以直接赋值，**其他的引用类型，不能通过 value 来赋值**
  + `ref` —— 将 IOC 中的另外一个 bean 赋给当前的成员变量（DI依赖注入）

  ```xml
  <bean id="student" class="com.fury.entity.Student">
      <property name="id" value="1"/>
      <property name="name" value="张三"/>
      <property name="age" value="25"/>
      <property name="address" ref="address"/>
  </bean>
  <bean id="address" class="com.fury.entity.Address">
      <property name="id" value="1"/>
      <property name="name" value="科华北路"/>
  </bean>
  ```



## scope 作用域

Spring 管理的 bean 是根据 scope 来生成的，表示 bean 的作用域，共4种

+ singleton —— 单例，表示通过 Spring 容器获取的 bean 是唯一的，默认的就是单例模式

+ prototype —— 原型，表示通过 Spring 容器获取的 bean 是不同的
+ request —— 请求，表示在一次 http 请求内有效
+ session —— 会话，表示在一个用户会话内有效

request 和 session 只适用于 Web 项目，大多数情况下，使用单例和原型较多

prototype 模式当业务代码获取 IOC 容器中的 bean 时，Spring 才去调用无参构造创建对应的 bean

singleton 模式无论业务代码是否获取 IOC 容器中的 bean ，Spring在加载 spring.xml 时就会创建 bean



## Spring的继承

与 Java 的继承不同，Java是类层面的继承，子类可以继承父类的内部结构的信息；Spring是对象层面的继承，子对象可以继承父对象的属性值

如下配置将会产生一个继承于 student 对象和属性的 stu 对象，并且覆盖 name 为 “李四”

```xml
<bean id="stu" class="com.fury.entity.Student" parent="student">
    <property name="name" value="李四"/>
</bean>
```

Spring 的继承关注点在于具体的对象，而不在于类，即不同的两个类的实力换对象可以完成继承，前提是子对象必须包含父对象的所有属性，同时可以在此基础上添加其他的属性



## Spring的依赖

与继承类似，依赖也是描述 bean 和 bean之间的一种关系，配置依赖之后，被依赖的 bean 一定是先创建，再创建依赖的 bean

```xml
<bean id="stu" class="com.fury.entity.Student" depends-on="user"/>
<bean id="user" class="com.fury.entity.User"/>
```



## Spring的p命名空间

p命名空间是对IOC/DI的简化操作，使用p命名空间可以更加方便地完成bean的配置和bean之间的依赖注入

引入

```xml
xmlns:p="http://www.springframework.org/schema/p"
```

配置

```xml
<bean id="newStu" class="com.fury.entity.Student" p:id="1" p:name="张三" p:age="15" p:address-ref="newAddress"/>
<bean id="newAddress" class="com.fury.entity.Address" p:id="1" p:name="南昌"/>
```



## Spring的工厂方法

IOC通过工厂模式创建bean的方式有两种：

### 静态工厂方法

创建静态工厂方法

```java
public class StaticCarFactory {
    private static Map<Long, Car> carMap;
    static {
        carMap = new HashMap<Long,Car>();
        carMap.put(1L, new Car(1L,"宝马"));
        carMap.put(2L, new Car(2L,"奔驰"));
    }

    public static Car getCar(Long id){
        return carMap.get(id);
    }
}
```

创建spring配置

```xml
<bean id="carFac" class="com.fury.factory.StaticCarFactory" factory-method="getCar">
    <constructor-arg value="2"/>
</bean>
```



### 实例工厂方法

创建实例工厂方法

```java
public class InstanceCarFactory {
    private Map<Long, Car> carMap;

    public InstanceCarFactory(){
        carMap = new HashMap<Long,Car>();
        carMap.put(1L, new Car(1L,"宝马"));
        carMap.put(2L, new Car(2L,"奔驰"));
    }

    public Car getCar(Long id){
        return carMap.get(id);
    }
}
```

创建spring配置

```xml
<!--配置实例工厂 bean-->
<bean id="carFac2" class="com.fury.factory.InstanceCarFactory"/>
<!--使用实例工厂创建 Car-->
<bean id="car" factory-bean="carFac2" factory-method="getCar">
    <constructor-arg value="1"/>
</bean>
```



## IOC 自动注入/自动装载（Autowire）

IOC 负责创建对象，DI 负责完成对象的依赖注入，通过配置 property 标签的 ref 属性来完成，同时 Spring 提供了更加简便的依赖注入方式：自动装载 —— 不需要手动配置property，IOC容器会自动选择 bean 完成注入

## 自动装载的两种方式

+ byName —— 通过属性名自动装载

通过 属性名 在 IOC 中找到 id 与其相同的配置

```xml
<bean id="car" class="com.fury.entity.Car">
    <property name="id" value="1"/>
    <property name="name" value="宝马"/>
</bean>

<bean id="person" class="com.fury.entity.Person" autowire="byName">
    <property name="id" value="11"/>
    <property name="name" value="张三"/>
</bean>
```

+ byType —— 通过属性的数据类型自动装载

寻找与 属性 相同类型 的配置

但是要注意，**如果同时存在两个及以上的符合条件的 bean 时，自动装载会抛出异常**

```xml
<bean id="car2" class="com.fury.entity.Car">
    <property name="id" value="1"/>
    <property name="name" value="宝马"/>
</bean>

<bean id="person2" class="com.fury.entity.Person" autowire="byType">
    <property name="id" value="11"/>
    <property name="name" value="张三"/>
</bean>
```





# AOP	面向切面编程

将复杂的需求分解出不同方面，将散布在系统中的公共功能集中解决

+ 降低模块之间的耦合度

+ 使系统容易扩展

+ 更好的代码复用

+ 非业务代码更加集中，不分散，便于统一管理

+ 业务代码更加简洁纯粹，不掺杂其他代码的影响

  AOP是对面向对象编程的一个补充，在运行前就预编译为字节码文件，在运行时动态地将代码切入到类的指定方法、指定位置上的编程思想就是面向切面编程。将不同方法的同一个位置抽象成一个切面对象，对该切面对象进行编程就是 AOP

  

## AOP 的几个概念

+ 切面 —— 横切关注点被模块化的抽象对象
+ 通知 —— 切面对象完成的工作
+ 目标 —— 被通知的对象，即被横切的对象
+ 代理 —— 切面、通知、目标混合之后的对象
+ 连接点 —— 通知要插入业务代码的具体位置
+ 切点 ——  AOP 通过切点定位到连接点



## 为什么使用AOP？    例子

### 创建接口

```
public interface Cal {
    public int add(int num1,int num2);
    public int sub(int num1,int num2);
    public int mul(int num1,int num2);
    public int div(int num1,int num2);
}
```

### 创建接口的实现类

```java
public class CalImpl implements Cal {
    @Override
    public int add(int num1, int num2) {
        int result=num1+num2;
        System.out.println("add方法的参数是[ "+num1+" ] 和 [ "+num2+" ]");
        System.out.println("add方法的结果是"+result);
        return result;
    }

    @Override
    public int sub(int num1, int num2) {
        int result=num1-num2;
        System.out.println("sub方法的参数是[ "+num1+" ] 和 [ "+num2+" ]");
        System.out.println("sub方法的结果是"+result);
        return result;
    }

    @Override
    public int mul(int num1, int num2) {
        int result=num1*num2;
        System.out.println("mul方法的参数是[ "+num1+" ] 和 [ "+num2+" ]");
        System.out.println("mul方法的结果是"+result);
        return result;
    }

    @Override
    public int div(int num1, int num2) {
        int result=num1/num2;
        System.out.println("div方法的参数是[ "+num1+" ] 和 [ "+num2+" ]");
        System.out.println("div方法的结果是"+result);
        return result;
    }
}
```



可见这段代码的重复性和耦合性非常的高，不利于系统的维护



## 通过动态代理实现 AOP

AOP通过动态代理的方式来实现

### 引入maven依赖

```xml
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
```

### 继承InvocationHandler接口实现动态代理

相当于自定义类作为代理，调用委托对象来完成操作，而不是使用者直接调用

```java
public class MyInvocationHandler implements InvocationHandler {
    //接收委托对象
    private Object object=null;

    //返回代理对象
    public Object bind(Object object){
        this.object=object;
        return Proxy.newProxyInstance(
                object.getClass().getClassLoader(),
                object.getClass().getInterfaces(),
                this );
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println(method.getName()+"方法的参数是: "+ Arrays.toString(args));
        Object result=method.invoke(this.object, args);
        System.out.println(method.getName()+"方法的结果是"+result);
        return result;
    }
}
```

原先类中重复的部分可以去除，简化了许多

```java
public class CalImpl implements Cal {
    @Override
    public int add(int num1, int num2) {
        int result=num1+num2;
        return result;
    }

    @Override
    public int sub(int num1, int num2) {
        int result=num1-num2;
        return result;
    }

    @Override
    public int mul(int num1, int num2) {
        int result=num1*num2;
        return result;
    }

    @Override
    public int div(int num1, int num2) {
        int result=num1/num2;
        return result;
    }
}
```



## 使用 Spring 框架封装的 AOP

Spring 框架中不需要创建 InvocationHandler，只需要创建一个切面对象，将所有的非业务代码在切面对象中完成即可。Spring 框架底层会自动根据切面类以及目标类生成一个代理对象

### 创建切面对象

```java
@Aspect
@Component
public class LoggerAspect {
    @Around(value = "execution(public int com.fury.impl.CalImpl.*(..))")
    public void before(JoinPoint joinPoint){
        //获取方法名
        String name=joinPoint.getSignature().getName();
        //获取参数
        Object[] args=joinPoint.getArgs();
        System.out.println(name+"方法的参数是: "+ Arrays.toString(args));
    }
    
    @Before(value = "execution(public int com.fury.impl.CalImpl.*(..))")
    public void before(JoinPoint joinPoint){
        //获取方法名
        String name=joinPoint.getSignature().getName();
        //获取参数
        Object[] args=joinPoint.getArgs();
        System.out.println(name+"方法的参数是: "+ Arrays.toString(args));
    }

    @After(value = "execution(public int com.fury.impl.CalImpl.*(..))")
    public void after(JoinPoint joinPoint){
        //获取方法名
        String name=joinPoint.getSignature().getName();
        System.out.println("方法执行完毕");
    }

    @AfterReturning(value = "execution(public int com.fury.impl.CalImpl.*(..))",returning = "result")
    public void afterReturning(JoinPoint joinPoint,Object result){
        String name=joinPoint.getSignature().getName();
        System.out.println(name+" 方法执行结果为："+result);
    }

    @AfterThrowing(value = "execution(public int com.fury.impl.CalImpl.*(..))",throwing = "exception")
    public void afterThrowing(JoinPoint joinPoint,Exception exception){
        String name=joinPoint.getSignature().getName();
        System.out.println(name+" 方法执行异常："+exception);
    }
}
```

类的注解：

+ `@Aspect` —— 表示该类是切面类
+ `@Component` —— 将该类的对象注入到 IOC 容器中

方法的注解：

+ `@Before` —— 表示方法执行的具体位置和时机

### 使用注解 @Component 修饰 委托类

```java
@Component
public class CalImpl implements Cal{.....}
```

### 在 Spring.xml 中配置 AOP

```xml
<!--自动扫描-->
<context:component-scan base-package="com.fury"/>
<!--使@Aspect注解生效，为目标类自动生成代理对象-->
<aop:aspectj-autoproxy/>
```

`context:component-scan` 将 `com.fury` 包中的所有类进行扫描，如果该类同时添加了 @Component，则将该类扫描到IOC容器中，即IOC管理它的对象

`aop:aspectj-autoproxy` 让Spring框架结合切面类和目标类自动生成动态代理对象

### 使用代理对象

**bean 的值为 委托类 的首字母小写**

```java
public static void main(String[] args) {
    //加载配置文件
    ApplicationContext applicationContext=new ClassPathXmlApplicationContext("spring_aop.xml");
    //获取代理对象
    Cal proxyCal = (Cal) applicationContext.getBean("calImpl");
    proxyCal.add(1, 1);
}
```

