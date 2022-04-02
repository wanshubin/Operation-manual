[TOC]

![image-20220227181533320](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220227181533320.png)



# Spring Cloud Alibaba

它是一系列分布式框架的集合，基于 Spring Boot 进行开发的，是一种规范

将不同公司生产的不同组件进行集成，以 Spring Boot 的风格进行集成，可以让开发者做到开箱即用，需要哪个组件就用 Spring Boot 整合进来

**通过以下工程结构来实现：**

Spring Boot =》 Spring Cloud =》 Spring Cloud Alibaba





# Nacos	服务治理、服务发现

多个微服务之间需要得到管理，采用如下模式：

**服务注册（提供方） + 服务发现（接收方）**

Alibaba 中是通过 Nacos 来实现的



# 使用 Spring Cloud Alibaba

## 创建父工程

使用 Spring Initializer，只配置 Lombok 和 Spring Web，作为父工程来使用

创建工程也可以使用 alibaba 提供的 bootstrap 来直接加入 springcloud alibaba 组件，将初始化器的地址改为 https://start.aliyun.com/ 

Spring Initializer 的地址为 https://start.spring.io/



### 导入 maven 依赖

添加 `<dependencyManagement>`  项，和 `<dependencies>` 同级，并引入依赖

```xml
<dependencyManagement>
    <dependencies>
        <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-dependencies -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>2020.0.3</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2.2.5.RELEASE</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```



### 修改 SpringBoot 版本

需要将 SpringBoot 版本修改到2.3.0.RELEASE

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.0.RELEASE</version>
</parent>
```



## Nacos 实现服务治理

打开startup.cmd 运行 nacos

登录，初始用户名和密码都为 nacos



## 服务注册

### 创建一个新模块

先在父工程的 pom 中添加打包格式配置

```xml
<packaging>pom</packaging>
```

创建新模块，并将子工程的 parent 标签中的内容变更为父工程的坐标

```xml
<parent>
    <groupId>com.fury</groupId>
    <artifactId>SpringCloudAlibabaPrac</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</parent>
```

添加nacos discovery依赖，版本号和 spring cloud alibaba 的版本一致

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-discovery -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    <version>2.2.5.RELEASE</version>
</dependency>
```



### 实现服务注册

#### 配置子工程的 application.yml

配置 nacos 地址和 application 的名字

```yaml
server:
  port: 8180
spring:
  application:
    name: consumer
  cloud:
    nacos:
      server-addr: localhost:8848
      username: nacos
      password: nacos
      # namespace：public    相同特征的服务进行归类管理
      # ephemeral：false   永久实例，即使宕机nacos也不会删除实例
      # service：默认取${spring.application.name}，也可以通过该选项配置
      # group：默认DEFAULT_GROUP   更细的相同特征的服务进行归类分组管理
      # weight：负载均衡权重
provider:
  ribbon:
    NFLoadBalancerRulerClassName: com.netflix.loadbalancer.NacosWeightedRule
```



#### 进入 Nacos

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210917232609608.png" alt="image-20210917232609608" style="zoom: 33%;" />



#### 设置可以允许多个实例并行

通过设置允许并行运行就可以同时运行多个实例了，每次启动之前修改它们的 server.port ，可以保证它们的端口不同

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210917232853642.png" alt="image-20210917232853642" style="zoom: 50%;" />





## 服务发现

### 创建一个新模块

先在父工程的 pom 中添加打包格式配置

```xml
<packaging>pom</packaging>
```

创建新模块，并将子工程的 parent 标签中的内容变更为父工程的坐标

```xml
<parent>
    <groupId>com.fury</groupId>
    <artifactId>SpringCloudAlibabaPrac</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</parent>
```

添加nacos discovery依赖，版本号和 spring cloud alibaba 的版本一致

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-discovery -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    <version>2.2.5.RELEASE</version>
</dependency>
```



### 实现服务发现

#### 创建controller

注入DiscoveryClient

编写方法，从discoveryClient对象中根据服务的 ID 来获取服务对象实例放入列表中，列表的泛型限定为 ServiceInstance

```java
@RestController
public class ConsumerController {
    @Autowired
    private DiscoveryClient discoveryClient;

    @GetMapping("/instances")
    public List<ServiceInstance> instances(){
        List<ServiceInstance> provider = this.discoveryClient.getInstances("provider");
        return provider;
    }
}
```



#### 编写application.yml

指定一个新端口和名称，其余基本相同





## 服务的互相调用

### 创建 Provider 的 controller

```java
@RestController
public class ProviderController {

    //使用Spring的EL表达式
    @Value("${server.port}")
    private String port;

    @GetMapping("/index")
    public String index(){
        return this.port;
    }
}
```



### 编写 Consumer 的 controller 和 配置

#### 编写RestTemplate的配置类

使用 RestTemplate 组件，来完成两个不同应用之间的调用，**RestTemplate 不是自动装载的，因此需要进行配置**

**此外RESTTemplate的配置也可以放在启动类中，因为启动类的注解本身也是一个配置注解**

```java
@Configuration
public class ConsumeConfiguration {

    @Bean
    public RestTemplate restTemplate(RestTemplateBuilder builder){
        RestTemplate restTemplate=builder.build();
        return restTemplate;
    }
}
```

#### 编写controller

```java
@RestController
@Slf4j
public class ConsumerController {
    @Autowired
    private DiscoveryClient discoveryClient;

    @Autowired
    private RestTemplate restTemplate;
    

    @GetMapping("/index")
    public String index(){
        //调用provider中的index方法
        //1.找到provider实例
        List<ServiceInstance> providerList = this.discoveryClient.getInstances("provider");
        //创建一个随机数，种子为provider的大小，来限制随机数范围
        int index = ThreadLocalRandom.current().nextInt(providerList.size());
        ServiceInstance serviceInstance = providerList.get(index);
        String uri = serviceInstance.getUri().toString();
        String url = uri+"/index";
        //2.调用index；使用RestTemplate组件，来完成两个不同应用之间的调用
        log.info("调用了端口{}",serviceInstance.getPort());
        return "调用了端口为 "+serviceInstance.getPort()+" 的服务，返回结果是 "+this.restTemplate.getForObject(url,String.class);
    }
}

```





# Ribbon	负载均衡

上面的调用流程比较繁琐，可以使用 Ribbon 组件进行简化；Ribbon 不是 Spring Cloud Alibaba 的组件，它是 Netfix 提供的，但是 `nacos-discovery` 中集成了 `ribbon` 组件



## 使用 Ribbon

### 为 RESTTemplate 配置添加注解

使用时直接在 RESTTemplate 的配置上添加 `@LoadBalance` 注解即可

```java
@Configuration
public class ConsumeConfiguration {

    @LoadBalanced
    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```



### 修改 controller 的调用方法

此时 url 的填写方式如下，调用多个服务时**默认采用轮询方式**来进行负载均衡

```java
@RestController
public class ConsumerController {
    @Autowired
    private DiscoveryClient discoveryClient;

    @Autowired
    private RestTemplate restTemplate;
    

    @GetMapping("/index")
    public String index(){
        //使用LoadBalance负载均衡时，此处url的写法如下，直接写要调用的实例，这里是provider
        return this.restTemplate.getForObject("http://provider/index", String.class);
    }
}
```



## 负载均衡方式

### 随机算法

在 Consumer 的 application.yml 配置中添加

```yaml
provider:
  ribbon:
    NFLoadBalancerRulerClassName: com.netflix.loadbalancer.RandomRule
```

### 基于权重的算法

#### 编写基于权重调用的配置类

```java
@Slf4j
public class NacosWeightedRule extends AbstractLoadBalancerRule {

    @Autowired
    private NacosDiscoveryProperties nacosDiscoveryProperties;

    @Override
    public void initWithNiwsConfig(IClientConfig iClientConfig) {
        //读取配置文件
    }

    @Override
    public Server choose(Object o) {
        ILoadBalancer loadBalancer = this.getLoadBalancer();
        BaseLoadBalancer baseLoadBalancer = (BaseLoadBalancer) loadBalancer;
        //获取要请求的微服务名称
        String name = baseLoadBalancer.getName();
        //获取服务发现的相关API
        NamingService namingService = nacosDiscoveryProperties.namingServiceInstance();
        try {
            Instance instance = namingService.selectOneHealthyInstance(name);
            log.info("选择的实例是port={},instance={}",instance.getPort(),instance);
            return new NacosServer(instance);
        } catch (NacosException e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

#### 在 Consumer 的 application.yml 配置中添加

```yaml
provider:
  ribbon:
    NFLoadBalancerRuleClassName: com.southwind.configuration.NacosWeightedRule
```





# Feign	服务间调用

Feign 是 Netflix 开发的组件，已经闭源，但是 Spring Cloud openfeign 对 Feign 进行了调整，使其支持 Spring MVC 注解，另外还整合了 Ribbon 和 Nacos ，从而使得 Feign 的使用更加方便

使用 RestTemplate 进行服务间调用比较不方便且代码难以阅读，可以使用 Feign 组件进行优化，Feign 可以做到使用 HTTP 请求远程服务时就调用本地方法一样的体验，开发者完全感知不到这是远程方法，更感知不到这是个 HTTP 请求。 Consumer 可以直接调用接口方法来调用 Provider，而不需要通过常规的 Http Client 构造请求再解析返回数据。它解决了让开发者调用远程接口就跟调用本地方法一样，无需关注与远程的交互细节，更无需关注分布式开发环境

**Feign 执行的过程就是根据 FeignClient 的 name 参数到 nacos 中根据服务名获取到对应的服务实例，再结合 负载均衡器ribbon 使用动态代理来进行调用**



## 使用 OpenFeign

### 引入依赖

 在父工程中引入 openfeign 的依赖

**OpenFeign 是基于 Ribbon 进行改进的，因此其中需要 `ribbon` 相关的依赖，而 `nacos-discovery` 中包含了 `ribbon`，因此使用 Feign 组件的服务要引入 `nacos-discovery`**

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-openfeign -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
    <version>2.2.5.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-discovery -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    <version>2.2.5.RELEASE</version>
</dependency>
```



### 添加 Feign 接口

Feign 接口的名字一般和 controller 接口的名字相同

项目结构

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220225195842905.png" alt="image-20220225195842905" style="zoom: 80%;" />

```java
/*  name 指定调用的rest接口对应的 服务名（工程组件名称），可在 nacos 中查看
*   path 指定调用的rest接口所在的controller中指定的 url，因为 pay 服务的 controller 层没有路径，所以不加这个参数
*/
@FeignClient(name = "pay")
public interface PayFeignService {
    //声明需要调用的rest接口对应的方法，与controller中的相同
    @GetMapping("/pay")
    String pay();
}
```



### 在启动类中添加注解

添加 `@EnableFeignClients` 注解

```java
@SpringBootApplication
@EnableFeignClients
public class OrderApplication {
    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }
}
```



### 在 controller 中使用

自动注入，使用时调用对应的方法

```java
@RestController
public class OrderController {
    @Autowired
    private OrderService orderService;
    
    @Autowired
    private PayFeignService payFeignService;

    @GetMapping("/order")
    public String order(){
        this.orderService.save();
        
        String msg = payFeignService.pay();
        
        return "success "+msg;
    }
}
```



访问 http://localhost:8010/order 即可对pay服务进行一次调用



# Sentinel	服务限流降级

雪崩效应：服务B 调用 服务A ，当 服务A 出现故障时，服务B 的请求就会全部处于阻塞状态，最终内存全部被占用，然后 服务B 也会崩溃；如果有其它服务调用 服务B，那么这些服务也将面临崩溃，以此类推剩余的服务也一样

解决方式：

+   设置线程超时
+   流量限制
+   熔断器 Sentinel、Hystrix
    +   **降级** —— 限制一些功能的使用，只提供部分功能，应对系统自身的故障
    +   **限流** —— 只接收系统能够承载的访问量，溢出时再请求将会直接抛出异常
    +   **熔断** —— 不请求功能，直接返回错误，应对外部系统的故障



## Sentinel 熔断机制

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20211009114527978.png" alt="image-20211009114527978" style="zoom: 80%;" />



## Sentinel 流控降级

### 引入 maven 依赖

在 provider 的 pom 中加入

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba.csp/sentinel-core -->
<dependency>
	<groupId>com.alibaba.csp</groupId>
	<artifactId>sentinel-core</artifactId>
	<version>1.7.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.alibaba.csp/sentinel-web-servlet -->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-web-servlet</artifactId>
    <version>1.7.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-sentinel -->
<!-- 流量控制 -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
    <version>2.2.5.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-actuator -->
<!-- 流量收集 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
    <version>2.3.0.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-actuator -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>2.3.0.RELEASE</version>
</dependency>

<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-transport-simple-http</artifactId>
    <version>1.7.2</version>
</dependency>

<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
    <version>1.7.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.alibaba.csp/sentinel-transport-common -->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-transport-common</artifactId>
    <version>1.7.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.alibaba.csp/sentinel-datasource-extension -->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-extension</artifactId>
    <version>1.7.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-discovery -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    <version>2.2.5.RELEASE</version>
</dependency>
```



### 配置 application.xml

配置 Provider 的 application.xml

```yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
    sentinel:
      transport:
        dashboard: localhost:8080
  application:
    name: provider
server:
  port: 8081
management:
  endpoints:
    web:
      exposure:
        include: '*'
```



### 启动 sentinel-dashboard 设置规则

启动服务并打开 localhost:8080，初次使用时**用户名**和**密码**都是 sentinel

打开 `簇点链路` ，设置流控规则



#### 流控

只接收系统能够承载的访问量，溢出时再请求将会直接抛出异常

##### 流控模式

+   直接 —— 限制当前资源

+   关联 —— **当前资源A** 设置关联 **其他资源B** ，当 **其他资源B** 的时刻访问量达到阈值时，限制 **当前资源A** 的使用

+   链路 —— 上面两种模式只能限制 controller 层的流量，链路可以做到更加细粒度的流量控制，可以控制各个控制层对于服务层的访问量；1.6.3 以上版本的需要先关闭URL收敛，否则链路不生效

    

##### 链路模式设置（关闭URL收敛）

###### provider的pom中引入依赖

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba.csp/sentinel-core -->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-core</artifactId>
    <version>1.8.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.alibaba.csp/sentinel-web-servlet -->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-web-servlet</artifactId>
    <version>1.8.2</version>
</dependency>
```



###### yml配置文件中关闭 filter

```yaml
spring:
  cloud:
      filter:
        enabled: false
```



###### 创建一个配置类

使链路不收敛而是开放，以达到我们限流的目的

```java
@Configuration
public class FilterConfiguration {
    
    @Bean
    public FilterRegistrationBean registrationBean(){
        FilterRegistrationBean registrationBean = new FilterRegistrationBean();
        registrationBean.setFilter(new CommonFilter());
        registrationBean.addUrlPatterns("/*");
        registrationBean.addInitParameter(CommonFilter.WEB_CONTEXT_UNIFY,"false");
        registrationBean.setName("sentinelFilter");
        return registrationBean;
    }
}
```



###### 使用注解 SentinelResource 修饰方法

通过注解的修饰使得 Service 层的内容也能得到保护，如下受到保护内容被命名为 test

```java
@Service
public class ProviderService {
    @SentinelResource("test")
    public void test(){
        System.out.println("test");
    }
}
```



###### 在 SentinelDashboard 中配置

选择 簇点链路 ，选择链路模式并且设置 入口资源

入口资源就是访问资源的上一级，比如 service 的上一级是 controller

如下，列如 /test1 和 /test2 两个控制层都调用了服务层的 test，下面入口资源设置为 /test1 的意思便是通过 /test1 访问服务层 test 的流量受到单机阈值设定值的限制

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220222164534201.png" alt="image-20220222164534201" style="zoom: 67%;" />

##### 流控效果

+   快速失败 —— 直接抛出异常，默认为这种方式
+   Warm Up —— 设置预热时间（单位为秒），在保存后开始预热时间计时，在预热时间内流量阈值会比单击阈值较低一些，预热时间后阈值为设置的单机阈值
+   排队等待 —— 设置超时时间（单位为毫秒），在调用失败后经过超时时间再次调用，如果可以调用则成功，调用失败则抛出异常



#### 降级

限制一些功能的使用，只提供部分功能，应对系统自身的故障

##### 降级策略

+   RT —— 单个请求的响应时间超过阈值就会降级，但是不会立即降级，而是进入准降级状态，在接下来1秒内连续5个请求响应时间均超过阈值，就进行降级，持续时间为时间窗口的值
+   异常比例 —— 请求中异常数占用通过量的比例大于异常比例值（0.x）时，就会进入降级状态
+   异常数 —— 每分钟内请求中异常数超过异常数值时，就进行降级处理，通常时间窗口大于60s



#### 热点规则

热点规则实际上是更细粒度的流控规则操作，对某个参数进行限流



如下代码

```java
@GetMapping("/hot")
@SentinelResource("hot")
public String hot(@RequestParam(value = "num1",required = false) Integer num1,
                  @RequestParam(value = "num2",required = false) Integer num2){
    return num1+"-"+num2;
}
```



如下，在设置对于hot的规则时，将参数索引设置为0，该规则将会对于传入参数位置第一位的num1进行限流

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220222182823816.png" alt="image-20220222182823816" style="zoom: 67%;" />

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220222182808916.png" alt="image-20220222182808916" style="zoom: 67%;" />



如下，设置 参数例外值 这个高级选项后，当num1为设置的10时，限流阈值为1000

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220222183216720.png" alt="image-20220222183216720" style="zoom:67%;" />



#### 授权

给指定的资源设置流控应用（追加参数），可以对流控应用进行访问权限的设置，具体就是添加白名单和黑名单



##### 授权操作

通过规则的设定，将会让带有name参数的请求才可以访问，没有name参数则会被拒绝，name参数的值是任意的



###### 实现 RequestOriginParser 接口，设置授权规则，如下采用 name 来作为识别

```java
public class RequestOriginParserDefinition implements RequestOriginParser {
    @Override
    public String parseOrigin(HttpServletRequest httpServletRequest) {
        String name=httpServletRequest.getParameter("name");
        if (StringUtils.isEmpty(name)){
            throw new RuntimeException("name is null");
        }
        return name;
    }
}
```



###### 要让 RequestOriginParserDefinition 生效，需创建授权规则的配置类

```java
@Configuration
public class SentinelConfiguration {
    @PostConstruct
    public void init(){
        WebCallbackManager.setRequestOriginParser(new RequestOriginParserDefinition());
    }
}
```



###### 设置白名单/黑名单

如下将流控应用设置为了admin并且为白名单，意味着当参数name的值为admin的请求才能够访问

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220222185159212.png" alt="image-20220222185159212" style="zoom:67%;" />





### 自定义规则异常返回

创建异常处理类

```java
public class ExceptionHandler implements UrlBlockHandler {
    @Override
    public void blocked(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, BlockException e) throws IOException {
        httpServletResponse.setContentType("text/html;charset=utf-8");
        String msg = null;
        if(e instanceof FlowException){
            msg = "限流";
        }else if(e instanceof DegradeException){
            msg = "降级";
        }
        httpServletResponse.getWriter().write(msg);
    }
}
```

在 SentinelConfiguration 中进行配置

```java
@Configuration
public class SentinelConfiguration {

    @PostConstruct
    public void init(){
        WebCallbackManager.setUrlBlockHandler(new ExceptionHandler());
    }
}
```





# RocketMQ	消息中间件





# Gateway	服务网关

服务网关可以在各个服务之间作为代理，列如 A服务 需要调用 B、C、D 三个服务，它可以通过服务网关来帮它调用服务，减少了 url 的记忆

Spring Cloud Gateway 是基于 Netty 的，跟 Servlet 是不兼容的，所以工程中不能出现 Servlet 的组件（不能出现 Spring Web 的依赖）



## 使用 Gateway

### 创建新模块

创建新模块 gateway，并引入将其依赖的 parent 修改为父工程

```xml
<parent>
    <groupId>com.fury</groupId>
    <artifactId>SpringCloudAlibabaPrac</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

因为父工程中含有 spring web 的依赖，所以将其在父工程中删除，分别添加到需要它的子工程的依赖下



### 添加依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-gateway -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
    <version>2.2.5.RELEASE</version>
</dependency>
```



### 编写 application.yml 配置文件

```yaml
server:
  port: 8181
spring:
  application:
    name: gateway
  # 开启网关对于服务的映射
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true
      # 配置网关路由
      routes:
        - id: provider_route
          uri: http://localhost:8081
          # predicates 表示这个path的路径将会映射到上面的uri
          predicates:
            - Path=/provider/**
          # 配置过滤器，去除前缀，如下设置是去除路径中的 provider
          filters:
            - StripPrefix=1
```



### 启动网关服务

访问 http://localhost:8181/provider/index 即可访问到 provider服务



### 可以直接配合 nacos服务发现 进行路由

#### gateway服务只需在yml配置这些

```yaml
server:
  port: 8181
spring:
  application:
    name: gateway
  # 开启网关对于服务的映射
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true
```



#### 在gateway服务中导入nacos依赖

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-discovery -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    <version>2.2.5.RELEASE</version>
</dependency>
```



#### 启动getway服务

访问 http://localhost:8181/provider/index 即可访问到 provider服务





## Gateway 限流

### 基于路由限流

这种方式只会让通过网关的流量受到限制，而直接访问服务的流量不会受限

#### 编写 application.yml

```yaml
server:
  port: 8181
spring:
  application:
    name: gateway
  # 开启网关对于服务的映射
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true
      # 配置网关路由
      routes:
        - id: provider_route
          uri: http://localhost:8081
          # predicates 表示这个path的路径将会映射到上面的uri
          predicates:
            - Path=/provider/**
          # 配置过滤器，去除前缀，如下设置是去除路径中的 provider
          filters:
            - StripPrefix=1
```



#### 引入依赖

还要去除 nacos-discovery 的依赖，否则链路将会通过nacos而不是路由调度

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-gateway -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
    <version>2.2.5.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.alibaba.csp/sentinel-spring-cloud-gateway-adapter -->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-spring-cloud-gateway-adapter</artifactId>
    <version>1.7.2</version>
</dependency>
```



#### 添加配置类

**其中方法 `initGatewayRules` 中的 new GatewayFlowRule(“网关路由的id”) ，其中网关路由的 `id` 要和 yml配置 中的 `id` 一致**

```java
@Configuration
public class GatewayConfiguration {
    private final List<ViewResolver> viewResolvers;
    private final ServerCodecConfigurer serverCodecConfigurer;


    public GatewayConfiguration(ObjectProvider<List<ViewResolver>> viewResolversProvider,
                                ServerCodecConfigurer serverCodecConfigurer) {
        this.viewResolvers = viewResolversProvider.getIfAvailable(Collections::emptyList);
        this.serverCodecConfigurer = serverCodecConfigurer;
    }

    //配置限流的异常处理
    @Bean
    @Order(Ordered.HIGHEST_PRECEDENCE)
    public SentinelGatewayBlockExceptionHandler sentinelGatewayBlockExceptionHandler() {
        return new SentinelGatewayBlockExceptionHandler(viewResolvers, serverCodecConfigurer);
    }

    //配置初始化的限流参数
    @PostConstruct
    public void initGatewayRules(){
        Set<GatewayFlowRule> rules = new HashSet<>();
        rules.add(
                new GatewayFlowRule("provider_route")
                        .setCount(1)
                        .setIntervalSec(1)
        );
        GatewayRuleManager.loadRules(rules);
    }

    //初始化限流过滤器
    @Bean
    @Order(Ordered.HIGHEST_PRECEDENCE)
    public GlobalFilter sentinelGatewayFilter() {
        return new SentinelGatewayFilter();
    }

    //自定义限流异常页面
    @PostConstruct
    public void initBlockHandlers(){
        BlockRequestHandler blockRequestHandler = new BlockRequestHandler() {
            @Override
            public Mono<ServerResponse> handleRequest(ServerWebExchange serverWebExchange, Throwable throwable) {
                Map map = new HashMap();
                map.put("code",0);
                map.put("msg","被限流了");
                return ServerResponse.status(HttpStatus.OK)
                        .contentType(MediaType.APPLICATION_JSON)
                        .body(BodyInserters.fromObject(map));
            }
        };
        GatewayCallbackManager.setBlockHandler(blockRequestHandler);
    }
}
```





### 基于API分组限流

可能要限流的路由很多，就可以对路由进行分组来对组进行限流

#### 基于路由配置

##### 添加几个分组

先在 provider 中添加两组模拟的分组，api1和api2

```java
@GetMapping("/api1/demo1")
public String demo1(){
    return "demo";
}

@GetMapping("/api1/demo2")
public String demo2(){
    return "demo";
}

@GetMapping("/api2/demo1")
public String demo3(){
    return "demo";
}

@GetMapping("/api2/demo2")
public String demo4(){
    return "demo";
}
```



##### 修改网关配置类

修改初始化参数的方法，其中 `provider_api1` 和 `provider_api2` 的名称都是自定义的

```java
//配置初始化的限流参数
@PostConstruct
public void initGatewayRules(){
    Set<GatewayFlowRule> rules = new HashSet<>();
    rules.add(new GatewayFlowRule("provider_api1").setCount(1).setIntervalSec(1));
    rules.add(new GatewayFlowRule("provider_api2").setCount(1).setIntervalSec(1));
    GatewayRuleManager.loadRules(rules);
}
```



添加自定义的 api 分组

```java
//自定义API分组
@PostConstruct
private void initCustomizedApis(){
    Set<ApiDefinition> definitions = new HashSet<>();
    //针对api1的所有接口限流
    ApiDefinition api1 = new ApiDefinition("provider_api1")
            .setPredicateItems(new HashSet<ApiPredicateItem>(){{
                add(new ApiPathPredicateItem().setPattern("/provider/api1/**")
                        .setMatchStrategy(SentinelGatewayConstants.URL_MATCH_STRATEGY_PREFIX));
            }});
    //对于api2，只对demo1进行限流
    ApiDefinition api2 = new ApiDefinition("provider_api2")
            .setPredicateItems(new HashSet<ApiPredicateItem>(){{
                add(new ApiPathPredicateItem().setPattern("/provider/api2/demo1"));
            }});
    definitions.add(api1);
    definitions.add(api2);
    GatewayApiDefinitionManager.loadApiDefinitions(definitions);
}
```



#### 基于 nacos-discovery组件 进行限流

###### gateway服务只需在yml配置这些

```yaml
server:
  port: 8181
spring:
  application:
    name: gateway
  # 开启网关对于服务的映射
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true
```



###### 在gateway服务中导入nacos依赖

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-discovery -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    <version>2.2.5.RELEASE</version>
</dependency>
```



###### 启动网关服务





# Seata	分布式事务

分布式数据库采用两段式提交，分为若干个本地事务和一个全局事务，将要执行的动作存入数据库，在回滚时由全局事务通知本地事务将其读出，生成一个语句然后执行本地事务。

Seata就是将微服务的本地事物进行统一管理，在注册中心nacos注册一个全局事务的服务，如果分布式事务需要回滚，那么就由全局事务来通知每个微服务的本地事务来完成回滚。简单地来说就是把一个大的操作分为若干个小的操作，这些若干个小的操作分布在不同的服务器上，分布式事务就是要保证这些小的操作要么全部成功，要么失败，分布式事务本质上就是为了保证不同数据库数据的一致性。



## 分布式事务模拟

1、创建两个子工程 order、pay，引入 web 、 jdbc 和 mysql 的依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```

2、建两个数据库 order、pay，两个微服务分别访问。

3、分别写两个服务的 application.yml

```yaml
server:
  port: 8010
spring:
  application:
    name: order
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: 123456
    url: jdbc:mysql://localhost:3306/order
```

```yaml
server:
  port: 8020
spring:
  application:
    name: pay
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: 123456
    url: jdbc:mysql://localhost:3306/pay
```

4、分别写两个 Service

```java
package com.southwind.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Service;

@Service
public class OrderService {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    public void save(){
        this.jdbcTemplate.update("insert into orders(username) values ('张三')");
    }
}
```

```java
package com.southwind.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Service;

@Service
public class PayService {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    public void save(){
        this.jdbcTemplate.update("insert into pay(username) values ('张三')");
    }
}
```

5、控制器 Order 通过 RestTemplate 调用 Pay 的服务

```java
package com.southwind.controller;

import com.southwind.service.OrderService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
public class OrderController {

    @Autowired
    private OrderService orderService;
    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/save")
    public String save(){
        //订单
        this.orderService.save();
        int i = 10/0;
        //支付
        this.restTemplate.getForObject("http://localhost:8020/save",String.class);
        return "success";
    }
}
```

```java
package com.southwind.controller;

import com.southwind.service.PayService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class PayController {
    @Autowired
    private PayService payService;

    @GetMapping("/save")
    public String save(){
        this.payService.save();
        return "success";
    }
}
```

6、启动类

```java
package com.southwind;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
public class OrderApplication {

    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }

    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```

```java
package com.southwind;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class PayApplication {

    public static void main(String[] args) {
        SpringApplication.run(PayApplication.class, args);
    }

}
```

分布式异常模拟结束，Order 存储完成之后，出现异常，会导致 Pay 无法存储，但是 Order 数据库不会进行回滚。



## Seata 解决

使用 **db + Nacos** 的方式部署高可用集群模式

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220225161437471.png" alt="image-20220225161437471" style="zoom: 80%;" />



### 配置

#### 修改 seata-server 的 /conf/ registry.conf

修改注册中心和配置中心为nacos，修改nacos配置

```xml
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos"

  nacos {
    application = "seata-server"
    serverAddr = "127.0.0.1:8848"
    group = "SEATA_GROUP"
    namespace = "public"
    cluster = "default"
    username = ""
    password = ""
  }
}

config {
  # file、nacos 、apollo、zk、consul、etcd3
  type = "nacos"

  nacos {
    serverAddr = "localhost"
    namespace = "public"
    group = "SEATA_GROUP"
    username = ""
    password = ""
    cluster = "default"
    dataId = "seataServer.properties"
  }
}
```



#### 修改 seata-server 的 /conf/ file.conf

修改存储模式

```xml
mode = "db"
```

修改数据库配置

```xml
db {
    ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp)/HikariDataSource(hikari) etc.
    datasource = "druid"
    ## mysql/oracle/postgresql/h2/oceanbase etc.
    dbType = "mariadb"
    driverClassName = "org.mariadb.jdbc.Driver"
    ## if using mysql to store the data, recommend add rewriteBatchedStatements=true in jdbc connection param
    url = "jdbc:mariadb://localhost:3719/seata_server?rewriteBatchedStatements=true"
    user = "wanshubin"
    password = "wanshubin0223"
    minConn = 5
    maxConn = 100
    globalTable = "global_table"
    branchTable = "branch_table"
    lockTable = "lock_table"
    queryLimit = 100
    maxWait = 5000
  }
```



#### 创建数据库

新建一个名为 `seata_server` 的数据库

在此数据库中使用如下语句建表

```sql
CREATE TABLE IF NOT EXISTS `global_table`
(
    `xid`                       VARCHAR(128) NOT NULL,
    `transaction_id`            BIGINT,
    `status`                    TINYINT      NOT NULL,
    `application_id`            VARCHAR(32),
    `transaction_service_group` VARCHAR(32),
    `transaction_name`          VARCHAR(128),
    `timeout`                   INT,
    `begin_time`                BIGINT,
    `application_data`          VARCHAR(2000),
    `gmt_create`                DATETIME,
    `gmt_modified`              DATETIME,
    PRIMARY KEY (`xid`),
    KEY `idx_gmt_modified_status` (`gmt_modified`, `status`),
    KEY `idx_transaction_id` (`transaction_id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

-- the table to store BranchSession data
CREATE TABLE IF NOT EXISTS `branch_table`
(
    `branch_id`         BIGINT       NOT NULL,
    `xid`               VARCHAR(128) NOT NULL,
    `transaction_id`    BIGINT,
    `resource_group_id` VARCHAR(32),
    `resource_id`       VARCHAR(256),
    `branch_type`       VARCHAR(8),
    `status`            TINYINT,
    `client_id`         VARCHAR(64),
    `application_data`  VARCHAR(2000),
    `gmt_create`        DATETIME(6),
    `gmt_modified`      DATETIME(6),
    PRIMARY KEY (`branch_id`),
    KEY `idx_xid` (`xid`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

-- the table to store lock data
CREATE TABLE IF NOT EXISTS `lock_table`
(
    `row_key`        VARCHAR(128) NOT NULL,
    `xid`            VARCHAR(96),
    `transaction_id` BIGINT,
    `branch_id`      BIGINT       NOT NULL,
    `resource_id`    VARCHAR(256),
    `table_name`     VARCHAR(32),
    `pk`             VARCHAR(36),
    `gmt_create`     DATETIME,
    `gmt_modified`   DATETIME,
    PRIMARY KEY (`row_key`),
    KEY `idx_branch_id` (`branch_id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;
```



#### 修改/script/ config-center/ config.txt

修改存储模式

```xml
store.mode=db
```

修改数据库配置

```xml
store.db.datasource=druid
store.db.dbType=mariadb
store.db.driverClassName=org.mariadb.jdbc.Driver
store.db.url=jdbc:mariadb://localhost:3719/seata_server?useUnicode=true
store.db.user=wanshubin
store.db.password=wanshubin0223
store.db.minConn=5
store.db.maxConn=30
store.db.globalTable=global_table
store.db.branchTable=branch_table
store.db.queryLimit=100
store.db.lockTable=lock_table
store.db.maxWait=5000
```



#### 运行脚本执行修改nacos配置

运行 `/script/ config-center/ nacos` 下的 `nacos-config.sh`



### 引入依赖

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-seata -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
    <version>2.2.5.RELEASE</version>
</dependency>
```



### 在各微服务对应的数据库中添加 undo_log 表

undo_log 中存储了修改之前的元数据和修改之后的元数据，如果要回滚的话就要从 undo_log 取出之前插入的语句然后逆向生成一个 sql 来回滚数据库

```sql
CREATE TABLE IF NOT EXISTS `undo_log`
(
    `branch_id`     BIGINT(20)   NOT NULL COMMENT 'branch transaction id',
    `xid`           VARCHAR(100) NOT NULL COMMENT 'global transaction id',
    `context`       VARCHAR(128) NOT NULL COMMENT 'undo_log context,such as serialization',
    `rollback_info` LONGBLOB     NOT NULL COMMENT 'rollback info',
    `log_status`    INT(11)      NOT NULL COMMENT '0:normal status,1:defense status',
    `log_created`   DATETIME(6)  NOT NULL COMMENT 'create datetime',
    `log_modified`  DATETIME(6)  NOT NULL COMMENT 'modify datetime',
    UNIQUE KEY `ux_undo_log` (`xid`, `branch_id`)
) ENGINE = InnoDB
  AUTO_INCREMENT = 1
  DEFAULT CHARSET = utf8 COMMENT ='AT transaction mode undo table';
```



### 配置事务组

在配置中心 config-center/ config.txt中，这个配置是事务分组，是异地机房停电容错机制

```xml
service.vgroupMapping.my_test_tx_group=default
```

通常如果机房在广州，那么这个配置会是

```xml
service.vgroupMapping.guangzhou=default
```

诸如此类

要将各个微服务client的组配置修改成和其相同的配置

```yaml
seata:
  tx-service-group: my_test_tx_group	#配置事务分组
```



### 配置各个微服务的seata注册中心和配置中心

以便让各个微服务从nacos中读取先前已配置好的设置

```yaml
seata:
  tx-service-group: my_test_tx_group
  registry: #配置seata的注册中心，告诉seata client怎么去访问seata server(TC)
    type: nacos
    nacos:
      server-addr: localhost:8848 #seata server所在的nacos服务地址
      application: seata-server #默认值，seata server的服务名
      username: nacos
      password: nacos
      group: SEATA_GROUP  #默认值，seata server所在的组
  config: #配置seata的配置中心，可以读取关于seata client的一些配置
    type: nacos
    nacos:
      server-addr: localhost:8848
      username: nacos
      password: nacos
      group: SEATA_GROUP
```



### 在微服务client的业务方法上加上全局事务注解

添加 `@GlobalTransactional` 注解

```java
@GetMapping("/order")
@LoadBalanced
@GlobalTransactional
public String order(){
    this.orderService.save();
    String msg = payFeignService.pay();
    return "success "+msg;
}
```



### 启动顺序

要先启动 nacos，再启动 seata-server，才能启动其他微服务





# SkyWalking	链路追踪监控

