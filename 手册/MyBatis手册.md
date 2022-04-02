[TOC]



# ORM	对象关系映射

Object Relationship Mapping

对象指面向对象

关系指关系型数据库

Java到MySql的映射，开发者可以以面向对象的思想来管理数据库



# 需要的依赖

需要 mybatis 和 jdbc

maven

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.7</version>
</dependency>
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.26</version>
</dependency>
```



# 使用Mybatis的原生接口

## 创建xml配置

此配置已经创建为模板，直接在新建中选取即可

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <settings>
        <!--开启日志 logImpl：控制日志 STDOUT_LOGGING：把日志输出到控制台上-->
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>

    <environments default=" ">
        <environment id=" ">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url"
                          value="jdbc:mysql://localhost:3719/ {数据库名称} ?useUnicode=true&amp;characterEncoding=utf-8&amp;serverTimezone=UTC"/>
                <property name="username" value="wanshubin"/>
                <property name="password" value="wanshubin0223"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper resource=" "/>
    </mappers>
</configuration>
```



## 创建mapper

已配置为模板，可以直接新建此类型的xml

namespace通常设置为文件所在包+文件名的形式

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">


<mapper namespace="com.fury.mapper.AccountMapper">
    <insert id="save" parameterType="com.fury.entity.Account">
        insert into t_account(username,password,age) values(#{username},#{password},#{age})
    </insert>
</mapper>
```



## 在配置文件中注册mapper

```xml
<mappers>
    <mapper resource="com/fury/mapper/AccountMapper.xml"/>
</mappers>
```



## 调用mapper

```java
public class Test {
    public static void main(String[] args) {
        //加载mybatis的配置
        InputStream inputStream=Test.class.getClassLoader().getResourceAsStream("mybatis_conf.xml");
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder=new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory=sqlSessionFactoryBuilder.build(inputStream);
        SqlSession sqlSession =sqlSessionFactory.openSession();
        String statement="com.fury.mapper.AccountMapper.save";
        Account account=new Account(1L,"张三","123123",25);
        sqlSession.insert(statement,account);
        sqlSession.commit();
        sqlSession.close();
    }
}
```





# 通过mapper代理实现自定义接口	常用

## 自定义接口，定义相关业务方法

```java
public interface AccountRepository {
    public int save(Account account);
    public int update(Account account);
    public int deleteById(Long id);
    public List<Account> findAll();
    public Account findById(Long id);
}
```



## 编写与方法相应的 Mapper.xml，定义接口方法对应的sql语句

statement 标签可根据 sql 执行的业务来选择 insert、delete、update、select

### Mybatis 框架会根据规则自动创建接口实现类的代理对象

**规则：**

+ **Mapper.xml 中 namespace 为接口的 全类名**
+ **Mapper.xml 中 statement 的 id 为接口中对应的 方法名**
+ **Mapper.xml 中 statement 的 parameterType 和接口中对应的方法的 参数类型 一致**
+ **Mapper.xml 中 statement 的 resultType 和接口中对应方法的 返回值类型 一致**



mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace通常设置为文件所在包+文件名的形式，如com.fury.mapper.AccountMapper-->
<mapper namespace="com.fury.repository.AccountRepository">
    <insert id="save" parameterType="com.fury.entity.Account">
        insert into t_account(username, password, age) values (#{username},#{passwod},#{age})
    </insert>
    <update id="update" parameterType="com.fury.entity.Account">
        update t_account set username = #{username},password=#{password},age=#{age} where id =#{id}
    </update>
    <delete id="deleteById" parameterType="Long">
        delete from t_account where id=#{id}
    </delete>
    <select id="findAll" resultType="com.fury.entity.Account">
        select * from t_account
    </select>
    <select id="findById" parameterType="Long" resultType="com.fury.entity.Account">
        select * from t_account where id=#{id}
    </select>
</mapper>
```



## 在config 中注册 mapper

```xml
<mappers>
    <mapper resource="com/fury/repository/AccountRepository.xml"/>
</mappers>
```



## 调用自定义接口函数

```java
public class Test2 {
    public static void main(String[] args) {
        InputStream inputStream=Test2.class.getClassLoader().getResourceAsStream("mybatis_conf.xml");
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder=new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory=sqlSessionFactoryBuilder.build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //获取实现接口的代理对象
        AccountRepository accountRepository =sqlSession.getMapper(AccountRepository.class);
        /*List<Account> accountList=new ArrayList<>();
        accountList.add(new Account(6L,"李四","2134",15));
        accountList.add(new Account(4L,"乌拉","dsa13d3a",16));
        accountList.add(new Account(2L,"阿达","2asda",21));
        accountList.add(new Account(10L,"茶庵","ew1g35d5",22));
        accountList.add(new Account(9L,"搜来","2fdsg53",16));
        for (Account account:accountList){
            accountRepository.save(account);
        }*/
        sqlSession.commit();
        System.out.println(accountRepository.findAll());
        System.out.println(accountRepository.findById(17L));
        System.out.println(accountRepository.findByName("张三"));
        sqlSession.close();
    }
}
```



# 标签

## statement

+ select —— 查询
+ update —— 修改
+ delete —— 删除
+ insert —— 添加

## parameterTpye

+ 基本数据类型
+ String
+ 包装类

+ 多个参数

多个参数类型时不写parameterType，读参数时可以用#{arg0}、#{arg1}...或者#{param1}、#{param2}...

+ JavaBean

读取数据时用#{属性名}

## resultType

+ 基本数据类型
+ 包装类
+ String类型
+ JavaBean



# resultMap	结果映射

当查询的结果和实体类不是一一对应的时候，就要使用 resultMap 来手动映射结果

如使用下面这段查询，结果是多个表的连接得到的

```sql
select s.id,
       s.name as name,
       c.name as className
from student s
left join classes c on c.id = s.cid
where s.id=92001
```

## 编写resultMap

### 使用 association 联合其他 entity 的属性

**之后在语句中不能使用 resultType，使用 resultMap，其值为指定的 resultMap 的 id**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace通常设置为文件所在包+文件名的形式，如com.fury.mapper.AccountMapper-->
<mapper namespace="com.fury.repository.StudentRepository">
    <resultMap id="studentMap" type="com.fury.entity.Student">
        <id column="id" property="id"></id>
        <result column="name" property="name"></result>
        <association property="classes" javaType="com.fury.entity.Classes">
            <id column="cid" property="id"></id>
            <result column="cname" property="name"></result>
        </association>
    </resultMap>

    <select id="findById" parameterType="Integer" resultMap="studentMap">
        select s.id as id,
               s.name as name,
               c.id as cid,
               c.name as cname
        from student s
        left join classes c on c.id = s.cid
        where s.id=#{id}
    </select>
</mapper>
```

### 使用 collection 联合其他集合类型的属性

设置 collection 的类型要使用 oftype 属性

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace通常设置为文件所在包+文件名的形式，如com.fury.mapper.AccountMapper-->
<mapper namespace="com.fury.repository.ClassesRepository">
    <!-- <insert id=" " parameterType=" "></insert> -->
    <!-- <update id=" " parameterType=" "></update> -->
    <!-- <delete id=" " parameterType=" "></delete>  -->

    <resultMap id="classesMap" type="com.fury.entity.Classes">
        <id column="cid" property="id"/>
        <result column="cname" property="name"/>
        <collection property="students" ofType="com.fury.entity.Student">
            <id column="id" property="id"/>
            <result column="name" property="name"/>
        </collection>
    </resultMap>

    <select id="findById" parameterType="Integer" resultMap="classesMap">
        select c.id as cid,
               c.name as cname,
               s.id as id,
               s.name as name
        from student s
                 left join classes c on c.id = s.cid
        where c.id=#{id}
    </select>
</mapper>
```



# 逆向工程

mybatis 框架需要：实体类、自定义 mapper 接口、mapper.xml配置

传统开发中上述的三个组件需要开发者手动创建，逆向工程可以帮助开发者自动创建三个组件，减轻开发者的工作量，提高工作效率

## 使用逆向工程

Mybatis Generator，简称MBG，是一个专门为Mybatis框架开发者定制的代码生成器，可自动生成mybatis框架所需的实体类、mapper接口、mapper.xml，支持基本的crud操作，但是一些相对复杂的sql需要开发者自己来完成

### 导入genetor

maven

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.generator/mybatis-generator-core -->
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-core</artifactId>
    <version>1.4.0</version>
</dependency>
```

### 创建MBG配置文件

jdbcConnection	配置数据库连接信息

javaModelGenerator	配置 JavaBean 的生成策略

sqlMapGenerator	配置 sql 映射文件的生成策略

javaClientGenerator	配置 Mapper 接口的生成策略

table	配置目标数据表（tableName：表名，domainObjectName：JavaBean类名）

### 创建generator执行类





# MyBatis延迟加载

延迟加载也叫懒加载、惰性价值，使用延迟加载可以提高程序的运行效率，针对于数据持久层的操作，在某些特定的情况下去访问特定的数据库，其他情况下可以不访问某些表，从一定程度上较少了Java应用于数据库的交互次数

例如查询学生和班级时，学生和班级是两张不同的表，如果当前需求只需要获取学生的信息，那么查询学生单表即可，如果需要通过学生获取对应的班级信息，则必须查询两张表

不同的业务需求，需要查询不同的表，根据具体的业务需求来动态减少数据表查询的工作就是延迟加载

## 开启延迟加载

```xml
<settings>
    <!--开启日志 logImpl：控制日志 STDOUT_LOGGING：把日志输出到控制台上-->
    <setting name="logImpl" value="STDOUT_LOGGING"/>
    <!--开启延迟加载-->
    <setting name="lazyLoadingEnabled" value="true"/>
</settings>
```

## 将多表关联查询拆分成多个单表查询

StudentRepository

```java
public Student findByIdLazy(Integer id);
```

StudentRepository.xml

```xml
<resultMap id="studentMapLazy" type="com.fury.entity.Student">
    <id column="id" property="id"/>
    <result column="name" property="name"/>
    <association property="classes"
                 javaType="com.fury.entity.Classes"
                 select="com.fury.repository.ClassesRepository.findByIdLazy"
                 column="cid"/>
</resultMap>

<select id="findByIdLazy" parameterType="integer" resultMap="studentMapLazy">
    select * from student where id=#{id}
</select>
```

ClassesRepository

```java
public Classes findByIdLazy(Integer id);
```

ClassesRepository.xml

```xml
<select id="findByIdLazy" parameterType="integer" resultType="com.fury.entity.Classes">
    select * from classes where id = #{id}
</select>
```



以上查询会在查询student信息时一同将班级信息也查询出来





# MyBatis缓存

使用缓存可以减少Java应用与数据库的交互次数，从而提升程序的运行效率，比如查询出 id=1 的对象，第一次查询出来之后就会自动将该对象保存到缓存中，当下一次查询时，直接从缓存中取出该对象即可，无需再次访问数据库

## mybatis缓存分类

+ 一级缓存：SqlSession级别，默认开启，并且不能关闭

操作数据库时需要创建 SqlSession 对象，在对象中有一个 HashMap 用于存储数据，不同的 SqlSession 之间的缓存数据区域是互不影响的

一级缓存的作用域是 SqlSession 范围的，当在同一个 SqlSession 中执行两次相同的 SQL 语句时，第一次执行完毕会将结果保存到缓存中，第二次查询时直接从缓存中获取

需要注意的是，如果 SqlSession 执行了 DML 操作（insert、update、delete），mybatis 必须将缓存清空以保证数据的一致性

+ 二级缓存：Mapper 级别，默认关闭，可以开启

使用二级缓存时，多个 SqlSession 使用同一个Mapper 的 SQL 语句操作数据库，得到的数据会保存在二级缓存区，同样使用 HashMap 进行数据存储，相比较于一级缓存，二级缓存范围更大，多个 SqlSession 可以共用二级缓存，也就是说二级缓存是跨 SqlSession 的

二级缓存是多个 SqlSession 共享的，其作用域是 Mapper 的同一个 namespace，不同的 SqlSession 两次执行相同的 namespace 下的 SQL 语句，参数也相等，则第一次执行成功之后会将数据保存到二级缓存中，第二次可直接从二级缓存中取出数据



## 使用二级缓存

### 开启二级缓存

```xml
<settings>
    <!--开启日志 logImpl：控制日志 STDOUT_LOGGING：把日志输出到控制台上-->
    <setting name="logImpl" value="STDOUT_LOGGING"/>
    <!--开启延迟加载-->
    <setting name="lazyLoadingEnabled" value="true"/>
    <!--开启二级缓存-->
    <setting name="cacheEnabled" value="true"/>
</settings>
```

### 在对应的 mapper.xml 中配置二级缓存

```xml
<cache/>
```

### 实体类实现序列化接口

```java
public class Account implements Serializable
```





# mybatis 动态 SQL

使用动态 SQL 可简化代码，减少开发者的工作量，程序可以自动根据业务参数来决定 SQL 语句的组成

+ **if 标签** —— 可以根据表达式的结果决定是否查询某个条件

```xml
<select id="findById" 
		parameterType="com.fury.entity.Account" 
		resultType="com.fury.entity.Account">
    select * from t_account where 
    <if test="id!=0">
        id=#{id}
    </if> 
    <if test="username!=null">
        and username = #{username}
    </if>
    <if test="password!=null">
        and password =#{password}
    </if>
    <if test="age!=0">
        and age = #{age}
    </if>
</select>
```

+ **where 标签** —— 可以根据其中的内容决定是否有 where 关键字，如果检测到 where 直接跟 and 拼接，则自动删除 and ，通常情况下 if 和 where 结合起来使用

```xml
<select id="findById" 
        parameterType="com.fury.entity.Account" 
        resultType="com.fury.entity.Account">
    select * from t_account 
    <where>
        <if test="id!=0">
            id=#{id}
        </if>
        <if test="username!=null">
            and username = #{username}
        </if>
        <if test="password!=null">
            and password =#{password}
        </if>
        <if test="age!=0">
            and age = #{age}
        </if>
    </where>
</select>
```

+ **choose、when标签**

```
<select id="findById" 
		parameterType="com.fury.entity.Account" 
		resultType="com.fury.entity.Account">
    select * from t_account
    <where>
        <choose>
            <when test="id!=0">
                id=#{id}
            </when>
            <when test="username!=null">
                username = #{username}
            </when>
            <when test="password!=null">
                password =#{password}
            </when>
            <when test="age!=0">
                age = #{age}
            </when>
        </choose>
    </where>
</select>
```

+ **trim 标签**

```xml
<select id="findById" 
        parameterType="com.fury.entity.Account" 
        resultType="com.fury.entity.Account">
    select * from t_account 
    <trim prefix="where" prefixOverrides="and">
        <if test="id!=0">
            id=#{id}
        </if>
        <if test="username!=null">
            and username = #{username}
        </if>
        <if test="password!=null">
            and password =#{password}
        </if>
        <if test="age!=0">
            and age = #{age}
        </if>
    </trim>
</select>
```

+ set 标签 —— 用于update操作，自动根据参数生成 SQL 语句

```xml
<update id="update" parameterType="com.fury.entity.Account">
    update t_account
    <set>
        <if test="username!=null">
            username = #{username}
        </if>
        <if test="password!=null">
            password=#{password}
        </if>
        <if test="age!=0">
            age=#{age}
        </if>
    </set>
    where id =#{id}
</update>
```

+ foreach 标签 —— 可以迭代生成一系列值，这个标签主要用于 SQL 的 in语法

比如 select * from t_user where id in (1,4,5)

```
<select id="findByIds" parameterType="com.fury.entity.Account" resultType="com.fury.entity.Account">
    select * from  t_account
    <where>
        <foreach collection="ids" open="id in (" item="id" close=")" separator=",">
            #{id}
        </foreach>
    </where>
</select>
```

