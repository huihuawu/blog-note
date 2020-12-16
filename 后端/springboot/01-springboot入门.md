# spring Boot
## 1. 从一个maven项目到一个springboot项目
### 1.1. 导入`springboot-parent`版本仲裁中心
- 首先创建一个maven项目，然后引入坐标，可以从springboot的官网上查找：
```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.9.RELEASE</version>
</parent>
```
首先springboot项目必须依赖一个叫`spring-boot-starter-parent`的父项目，我们可以点进去查看这个父项目中还依赖一个`spring-boot-dependencies`的项目，在该项目中我们可以看到这个dependencies中依赖了许多的项目，所以我们在我们自己的springboot的项目中时，引入其他的jar的依赖的时候可以不用指定版本，就使用默认的依赖，如果这个start没有引入的依赖还是需要我们自己加入版本号

### 1.2. 导入`spring-boot-starter-xxx`相关的的场景启动器： 
> 因为这个案例是一个web的应用，所以只需要导入web的场景启动器即可：

```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```
在我们开发springboot的项目时，我们使用什么样的场景，就只需要导入相关的依赖即可以，如我们需要开发一个web的项目，只需要导入`spring-boot-starter-`web的开发启动环境即可，它会自动导入mvc等其他必须要的web项目的依赖，==要用什么功能，只需要导入场景启动器，如果需要使用邮件开发，就导入`spring-boot-starter-mail`场景启动器==
#### 1.2.1 Spring Boot 的各种start
``` 
- spring-boot-starter  
　　Spring Boot核心starter，包含自动配置、日志、yal配置文件支持
- spring-boot-starter-actuator  
　　准生产特性、用于监控和管理应用
- spring-boot-starter-remote-shell  
　　提供基于ssh协议的监控和管理
- spring-boot-starter-amqp  
　　使用spring-rabbitlai zhichi AMQP
- spring-boot-starter-aop  
　　使用spring-aop和AspectJ支持面向切面编程
- spring-boot-starter-batch  
　　对Spring Batch的支持
- spring-boot-starter-cache
　　对Spring Cache的抽象支持  
- spring-boot-starter-cloud-connectors  
　　对云平台（Cloud Foundry、Heroku）提供的服务提供简化的连接方式
- spring-boot-starter-data-elasticsearch  
　　通过spring-data-elasticsearch对Elasticsearch支持
- spring-boot-starter-data-gemfire  
　　通过spring-data-gemfire对分布式存储GemFire的支持
- spring-boot-starter-data-jpa  
　　对JPA的支持，包含spring-data-jpa、spring-orm和hibernate
- spring-boot-starter-mongodb  
　　通过spring-data-mongodb,对mongodb的支持
- spring-boot-starter-rest  
　　通过spring-data-rest-webmvc将Spring Datarepository暴露为REST形式服务  
- spring-boot-starter-solr  
　　通过spring-data-solr对Apache Solr数据检索的支持  
- spring-boot-starter-freemarker  
　　对Freemarker模板引擎的支持  
- spring-boot-starter-groovy-templates  
　　通过spring-hateoas对基于HATEOAS的REST形式网络服务支持  
- spring-boot-starter-hornetq  
　　通过HornetQ对JMS的支持  
- spring-boot-starter-integration  
　　对系统集成框架spring-integration支持  
- spring-boot-starter-jdbc  
　　对JDBC数据库的支持  
- spring-boot-starter-jersey  
　　对Jersery REST形式网络服务支持  
- spring-boot-starter-jta-atomikos  
　　通过Atomikos对分布式事务的支持  
- spring-boot-starter-mail  
　　对javax.mail的支持  
- spring-boot-starter-mobile  
　　对spring-mobile的支持  
- spring-boot-starter-mustache  
　　对Mustache模板引擎的支持  
- spring-boot-starter-redis  
　　对Redis的支持，包含spring-redis    
- spring-boot-starter-security
　　对spring-security的支持  
- spring-boot-starter-social-facebook  
　　对Facebook支持  
- spring-boot-starter-social-linkedin  
　　对linkedin支持  
- spring-boot-starter-social-twitter  
　　对Twitter支持  
- spring-boot-starter-test  
　　对常用测试框架JUnit、Hamcrest和Mockito的支持，包含spring-test模块  
- spring-boot-starter-thymeleaf  
　　对Thymeleaf模板引擎的支持，包含于Spring整合的配置  
- spring-boot-starter-velocity  
　　对Velocity模板引擎的支持  
- spring-boot-starter-web  
　　对Web项目开发支持，包含Tomcat和Spring MVC  
- spring-boot-starter-Tomcat  
　　Spring Boot默认Servlet容器  
- spring-boot-starter-Jetty  
　　使用Jetty作为Servlet容器  
- spring-boot-starter-undertow  
　　使用Undertow作为Servlet容器  
- spring-boot-starter-logging  
　　Spring Boot默认日志框架logback  
- spring-boot-starter-log4j  
　　支持使用Log4J日志框架  
- spring-boot-starter-websocket  
　　对websocket的支持  
- spring-boot-starter-ws  
　　对Spring Web Service支持  
```
### 1.3. 编写一个主程序：
```
@SpringBootApplication // @SpringBootApplication用来标识这是一个springboot的应用的主程序
public class HelloApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloApplication.class,args);
    }
}
```
### 1.4. 编写相关的controller，service等
```
@Controller //标识可以处理请求的controller
public class HelloController {
    @GetMapping("/")
    @ResponseBody
    public String hello(){
        return "hello";
    }
}
```
### 1.5. 项目打包：
需要引入`maven-plugin`:
```
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```
【思考】为什么我们没有配置包扫描等信息，它会==自动==去扫描到controller的配置信息等等，是因为springboot应用会将被`@SpringBootApplication`标注的主配置类下的所有包以及所有子包的所有组件扫描到spring的容器中，所以我们在==开发时必须要将主配置类放在外面==
## 2. springboot的目录结构
- resources的目录结构：
  - static:保存所有的静态资源：js, css, images
  - templates:保存所有的模板页面：(springboot 默认jar包使用的是嵌入式的tomcat，默认不使用jsp页面)；可以使用模板引擎，如freemarker，thymeleaf...
  - application.properties: springboot的配置文件  


## 3. springboot的配置文件  
springboot的全局配置文件，作用是用来修改springboot自动配置的默认值，放在resources的目录下，文件名可以是`application.properties`和`application.yml`,编译后会放到类的路径下  
### 3.1. yml语法
以==空格==控制缩进,数据类型可以是字面值，数组，map对象等等  
### 3.2. 获取全局配置文件中的值
场景：现在有一个person对象，属性有name，age，date  
我们首先在`application.yml`全局配置文件中写入：  
```
person:
  name: wuhuihua
  age: 21
  date: 2020/01/02
```
或者在`application.properties`配置文件中：
```
person.name=wuhuihua
person.age=12
person.date=2017/2/12
```
#### 方式一：使用`@ConfigurationProperties`注解
然后编写对应的java类：
```
@Getter
@Setter
@ToString
public class Person {
    private String name;
    private int age;
    private Date date;
}
```
但是怎样这个Person对象会去自动去配置文件中对应起来呢？需要在Person对象上面添加一个注解：`@ConfigurationProperties(prefix = "person")`指定从配置文件中获取，但是配置文件中有很多的内容，还需要使用prefix来指定前缀来自动匹配，但是要想让springboot自动为你匹配，必须是spring容器中对象才会自动匹配，因此还需要再加上一个@Component注解将这个person对象放入到spring容器中：
```
@Getter
@Setter
@ToString
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    private String name;
    private int age;
    private Date date;
}
```
为了使得在编写配置文件时，可以有代码的提示功能，可以再加入下面的依赖：
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```
#### 方式二：使用`@Value`注解
`@Value`中支持3中赋值方式
- 字面量值：直接数字，字符串
- ${key}：从环境变量，配置文件中获取
- #{SPEL}：里面写spell的表达式，如：#{10*2}
```
@Getter
@Setter
@ToString
@Component
public class Person {
    @Value("${person.name}")
    private String name;
    @Value("#{20+4}")
    private int age;
    @Value("2017/01/2")
    private Date date;
}
```
#### 俩种方式的对比


|                      | @ConfigurationProperties | @Value     |
| -------------------- | ------------------------ | ---------- |
| 功能                 | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定（松散语法） | 支持                     | 不支持     |
| SpEL                 | 不支持                   | 支持       |
| JSR303数据校验       | 支持                     | 不支持     |
| 复杂类型封装         | 支持                     | 不支持     |


#### 编写测试代码：
```
@SpringBootTest
@RunWith(SpringRunner.class)
public class HelloTest {

    @Autowired
    private Person person;

    @Test
    public void test01(){
        System.out.println(person);
    }
}
```
### 3.3. `@PropertySource`和`@ImportResource`读取指定配置文件
#### `@PropertySource`
`@ConfigurationProperties`默认只能读取的是springboot全局的配置文件，但如果我们想要读取自己的配置文件呢？我们不可能将上面例子的Person的配置也放在全局的配置文件中，在全局的配置文件中应该放的都是springboot相关的配置信息，我们可以使用`@PropertySource`（只能用于Properties文件）注解来读取指定文件  
实现编写一个自己的配置文件`my.properties`：
```
person.name=吴慧
person.age=12
person.date=2017/2/12
```
然后在编写一个java的类，使用`@PropertySource`注解在读取`my.properties`自己的配置文件：
```
@Getter
@Setter
@ToString
@Component
@PropertySource(value = {"classpath:my.properties"})
public class Person {
    @Value("${person.name}")
    private String name;
    @Value("${person.age}")
    private int age;
    @Value("${person.date}")
    private Date date;
}
```
#### `@ImportResource`
导入spring的配置文件，让我们的配置文件里面的内容生效，如果我们自己随便写一个xml文件，然后在里面加上bean的标签：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="helloService" class="top.wuhuihua.services.HelloService"></bean>
</beans>
```
默认是不会加入到spring的容器中的，所以并不是写一个spring的配置文件就自动识别，要想这个spring的配置文件生效，必须使用`@ImportResource`标注在一个配置类上，我们可以标注在主配置类上面：
```
@ImportResource(locations = {"classpath:bean.xml"})
@SpringBootApplication
public class HelloApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloApplication.class,args);
    }
}
```
这样这个spring的配置文件才会生效  
然后在后来开发的时候我们通常往容器中添加组件的时候，不会这样去写一个配置文件，然后添加进来，springboot推荐给容器中添加组件的方式是使用spring配置类的方式：
```
@Configuration
public class MyAppConfig {
    @Bean
    public HelloService helloService(){
        return new HelloService();
    }
}
```
使用上面的配置类的方式来代替之前的配置文件的方式，默认id就是方法名
### 3.4. 在配置文件中的占位符
- 随机数
- 占位符获取之前配置的值，如果没有可以用:指定默认值
```
person.name=吴慧${random.uuid}_numberOne
person.age=12
person.other=${person.name:test} //如果有person.name变量就用，如果没有就用后面的默认的
person.date=2017/2/12
```
### 3.5. 配置文件加载位置
springboot 启动会扫描以下位置的`application.properties`或者`application.yml`文件作为`Spring boot`的默认全局配置文件
> 当前项目下/config/
>
> 当前项目/
>
> 当前类路径下/config/
>
> 当前类路径下/

优先级由高到底，高优先级的配置会覆盖低优先级的配置；SpringBoot会从这四个位置全部加载主配置文件；**互补配置**；  
==我们还可以通过spring.config.location来改变默认的配置文件位置==  
**【注意】项目打包好以后，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置；指定配置文件和默认加载的这些配置文件共同起作用形成互补配置；**  
`java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --spring.config.location=G:/application.properties`
### 3.6. 外部配置加载顺序

**==SpringBoot也可以从以下位置加载配置； 优先级从高到低；高优先级的配置覆盖低优先级的配置，所有的配置会形成互补配置==**

> **1.命令行参数**:所有的配置都可以在命令行上进行指定  
> `java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --server.port=8087 --server.context-path=/abc`  
>
> 2.来自java:comp/env的JNDI属性
>
> 3.Java系统属性（System.getProperties()）
>
> 4.操作系统环境变量
>
> 5.RandomValuePropertySource配置的random.*属性值
>
>
> ==**优先加载带profile**==  
> ==**由jar包外向jar包内进行寻找；**==  
> **6.jar包外部的application-{profile}.properties或application.yml(带spring.profile)配置文件**
>
> **7.jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件**
>
>
> ==**再来加载不带profile**==
>
> **8.jar包外部的application.properties或application.yml(不带spring.profile)配置文件**
>
> **9.jar包内部的application.properties或application.yml(不带spring.profile)配置文件**
>
> 10.@Configuration注解类上的@PropertySource
>
> 11.通过SpringApplication.setDefaultProperties指定的默认属性  
>
> 所有支持的配置加载来源；
>
> [参考官方文档](https://docs.spring.io/spring-boot/docs/1.5.9.RELEASE/reference/htmlsingle/#boot-features-external-config)  

## 4. Profile 多环境开发
Profile是spring对不同环境提供不同配置功能的支持，可以通过激活，指定参数等方式快速切换环境
### 4.1. 方式一：多Profile文件方式
创建多个全局的配置文件，springboot支持多种配置文件的方式，命名格式是`application-<标识符>.properties/yml`,默认起作用的是application.properties/yml，如果想指定其他的全局配置文件起作用，可以在默认的全局配置文件中使用`spring.profiles.active=<标识符>`的方式来激活  
### 4.2. 方式二：使用yml的多文档块的方式
```
#默认的生产环境
server:
  port: 8080
#激活指定的环境
spring:
  profiles:
    active: dev
---
server:
     port: 8081
spring:
  profiles: dev
---
server:
  port: 8082
spring:
  profiles: prod
```
除了上面的在配置文件中激活指定的环境，我们还可以使用命令行的方式，运行的时候后面增加参数 `--spring.profiles.active=dev`   
此外，还可以指定虚拟机的参数：  
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/D33374E4B7AA45B9B8C6D7B18BF9B394/10209)  
## 5. `@Conditional`注解
作用：必须是`@Conditional`指定的条件成立，才给容器中添加组件，配置文件里面配置的内容才生效；springboot为`@Conttional`扩展了许多的注解：  

| @Conditional扩展注解            | 作用（判断是否满足当前指定条件）                 |
| ------------------------------- | ------------------------------------------------ |
| @ConditionalOnJava              | 系统的java版本是否符合要求                       |
| @ConditionalOnBean              | 容器中存在指定Bean；                             |
| @ConditionalOnMissingBean       | 容器中不存在指定Bean；                           |
| @ConditionalOnExpression        | 满足SpEL表达式指定                               |
| @ConditionalOnClass             | 系统中有指定的类                                 |
| @ConditionalOnMissingClass      | 系统中没有指定的类                               |
| @ConditionalOnSingleCandidate   | 容器中只有一个指定的Bean，或者这个Bean是首选Bean |
| @ConditionalOnProperty          | 系统中指定的属性是否有指定的值                   |
| @ConditionalOnResource          | 类路径下是否存在指定资源文件                     |
| @ConditionalOnWebApplication    | 当前是web环境                                    |
| @ConditionalOnNotWebApplication | 当前不是web环境                                  |
| @ConditionalOnJndi              | JNDI存在指定项                                   |

**自动配置类必须在一定的条件下才能生效**；我们怎么知道哪些自动配置类生效；  
**==我们可以通过启用  debug=true属性；来让控制台打印自动配置报告==**，这样我们就可以很方便的知道哪些自动配置类生效；
```java
=========================
AUTO-CONFIGURATION REPORT
=========================


Positive matches:（自动配置类启用的）
-----------------

   DispatcherServletAutoConfiguration matched:
      - @ConditionalOnClass found required class 'org.springframework.web.servlet.DispatcherServlet'; @ConditionalOnMissingClass did not find unwanted class (OnClassCondition)
      - @ConditionalOnWebApplication (required) found StandardServletEnvironment (OnWebApplicationCondition)
        
    
Negative matches:（没有启动，没有匹配成功的自动配置类）
-----------------

   ActiveMQAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required classes 'javax.jms.ConnectionFactory', 'org.apache.activemq.ActiveMQConnectionFactory' (OnClassCondition)

   AopAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required classes 'org.aspectj.lang.annotation.Aspect', 'org.aspectj.lang.reflect.Advice' (OnClassCondition)
```
## 6. 日志使用
### 6.1. springboot的默认配置
SpringBoot默认帮我们配置好了日志；选用日志门面(抽象，如jdbc)是SL4j，日志实现是logback,所以在开发中不应该调用日志的实现类中的方法，而是调用日志抽象层中的方法，所以在导入的时候是导入SL4j和logback的实现jar    
### 6.2. springboot统一日志环境
虽然springboot给我们默认配置了日志框架，但我们知道市面上有好多的日志框架，当我们集成其他的框架的时候，难免会有一些框架并不是使用SL4j和logback，导致项目里面会有好多种日志，springboot为了统一的管理，使得最终程序员都是直接面向SL4j和logback日志来开发，我们可以引入springboot的日志的starter:
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
</dependency>
```
这样我们在使用的是时候相当于项目中只会使用SL4j和logback，其他框架底层就算不是使用这2个也是不会报错的
### 6.3. springboot日志的使用
```
public void test01(){
    Logger logger = LoggerFactory.getLogger(getClass()); //导入slf4j的日志记录器
    //日志的级别，由低到高：trace->debug->info->warn->error
    logger.trace("这是用来跟踪轨迹信息");
    logger.debug("这是debug日志信息");
    logger.info("这是info信息");
    logger.warn("这是警告信息");
    logger.error("这是错误信息");
}
```
==为什么要分这么多的级别呢？==  
因为我们在开发中在不同的环境中可能需要的信息不同，如我们在开发的时候可以需要debug以上信息，而在上线的时候我们只需要warn和error的信息，所以我们可以在不同环境开发中指定日志的打印级别，项目只会打印指定级别和以上级别的日志  
上面程序输出：
```
2020-02-06 18:17:51.000  INFO 27620 --- [main] top.wuhuihua.HelloTest: 这是info信息
2020-02-06 18:17:51.000  WARN 27620 --- [main] top.wuhuihua.HelloTest: 这是警告信息
2020-02-06 18:17:51.000 ERROR 27620 --- [main] top.wuhuihua.HelloTest: 这是错误信息
```
发现springboot默认的日志级别是info级别，所以只会打印info及以上级别的日志  
我们可以自己配置日志的级别，格式等等配置  
```
logging.level.top.wuhuihua = trace
#指定日志top.wuhuihua包下的级别全是trace级别
#logging.path=
# 不指定路径在当前项目下生成springboot.log日志
# 可以指定完整的路径；
#logging.file=G:/springboot.log

# 在当前磁盘的根路径下创建spring文件夹和里面的log文件夹；使用 spring.log 作为默认文件
logging.path=/spring/log

#  在控制台输出的日志的格式
logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
# 指定文件中日志输出的格式
logging.pattern.file=%d{yyyy-MM-dd} === [%thread] === %-5level === %logger{50} ==== %msg%n
```
下面是指定日志的输入文件
| 是否指定文件名(logging.file) | 是否指定文件目录(logging.path) | 例子     | Description                        |
| ---------------------------- | ------------------------------ | -------- | ---------------------------------- |
| (none)                       | (none)                         |          | 只在控制台输出                     |
| 指定文件名                   | (none)                         | my.log   | 输出日志到my.log文件               |
| (none)                       | 指定目录                       | /var/log | 输出到指定目录的 spring.log 文件中 |

## 7. springboot与Web开发
### 7.1. 使用springboot开发web项目的步骤： 
> - 创建SpringBoot应用，选中我们需要的模块；  
> - SpringBoot已经默认将这些场景配置好了，只需要在配置文件中指定少量配置就可以运行起来  
> - 自己编写业务代码
>

### 7.2. springboot对静态页面的规则
#### 添加原理代码
```java
WebMvcAuotConfiguration类中下面方法配置静态资源
	@Override
	public void addResourceHandlers(ResourceHandlerRegistry registry) {
		if (!this.resourceProperties.isAddMappings()) {
			logger.debug("Default resource handling disabled");
			return;
		}
		Integer cachePeriod = this.resourceProperties.getCachePeriod();
		if (!registry.hasMappingForPattern("/webjars/**")) {
			customizeResourceHandlerRegistration(
					registry.addResourceHandler("/webjars/**")
							.addResourceLocations(
									"classpath:/META-INF/resources/webjars/")
					.setCachePeriod(cachePeriod));
		}
		String staticPathPattern = this.mvcProperties.getStaticPathPattern();
      	//静态资源文件夹映射
		if (!registry.hasMappingForPattern(staticPathPattern)) {
			customizeResourceHandlerRegistration(
					registry.addResourceHandler(staticPathPattern)
							.addResourceLocations(
									this.resourceProperties.getStaticLocations())
					.setCachePeriod(cachePeriod));
		}
	}

    //配置欢迎页映射
	@Bean
	public WelcomePageHandlerMapping welcomePageHandlerMapping(
			ResourceProperties resourceProperties) {
		return new WelcomePageHandlerMapping(resourceProperties.getWelcomePage(),
				this.mvcProperties.getStaticPathPattern());
	}

   //配置喜欢的图标
	@Configuration
	@ConditionalOnProperty(value = "spring.mvc.favicon.enabled", matchIfMissing = true)
	public static class FaviconConfiguration {

		private final ResourceProperties resourceProperties;

		public FaviconConfiguration(ResourceProperties resourceProperties) {
			this.resourceProperties = resourceProperties;
		}

		@Bean
		public SimpleUrlHandlerMapping faviconHandlerMapping() {
			SimpleUrlHandlerMapping mapping = new SimpleUrlHandlerMapping();
			mapping.setOrder(Ordered.HIGHEST_PRECEDENCE + 1);
          	//所有  **/favicon.ico 
			mapping.setUrlMap(Collections.singletonMap("**/favicon.ico",
					faviconRequestHandler()));
			return mapping;
		}

		@Bean
		public ResourceHttpRequestHandler faviconRequestHandler() {
			ResourceHttpRequestHandler requestHandler = new ResourceHttpRequestHandler();
			requestHandler
					.setLocations(this.resourceProperties.getFaviconLocations());
			return requestHandler;
		}

	}

```
#### 添加静态资源文件的方式
##### webjars的方式
> - webjars：以jar包的方式引入静态资源；  
> 页面中引入 /webjars/** 的请求，都去 `classpath:/META-INF/resources/webjars/` 找对应的资源；
> 可以在官网下查找对应哪些是常用的webjar  
> [webjar 官网](http://www.webjars.org/)  
>
> 如引入jquery，我们可以在webjar官网搜索，然后加入依赖：
```
<dependency>
	<groupId>org.webjars</groupId>
	<artifactId>jquery</artifactId>
	<version>3.3.1</version>
</dependency>
```
> 在访问的时候只需要写webjars下面资源的名称即可，如`localhost:8080/webjars/jquery/3.3.1/jquery.js`

##### "/**" 访问当前项目的任何资源(自己写的js,css等文件)
> 在项目中写"/**" 访问当前项目的任何资源，都去静态资源的文件夹找映射文件，如`localhost:8080/abc`：  
> springboot的静态资源的文件夹包含下面几个文件夹

```
"classpath:/META-INF/resources/", 
"classpath:/resources/",
"classpath:/static/", 
"classpath:/public/" 
"/"：当前项目的根路径
//还可以自己在配置文件中使用 spring.resources.static-location=classpath:/hello,classpath:/wuhuihua 自己来指定
```

#####  欢迎页
> 静态资源文件夹下的所有index.html页面；被"/**"映射localhost:8080/

##### 所有的 **/favicon.ico 
> 都是在静态资源文件下找
>

### 7.3. 模板引擎Thymeleaf
> JSP、Velocity、Freemarker、Thymeleaf等等  
> SpringBoot推荐的Thymeleaf；语法更简单，功能更强大；

#### 引入thymeleaf
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
我们还可以自己切换thymeleaf版本，在pom文件加入下面
<properties>
	<thymeleaf.version>3.0.9.RELEASE</thymeleaf.version>
	<!-- 布局功能的支持程序  thymeleaf3主程序  layout2以上版本 -->
	<!-- thymeleaf2   layout1-->
	<thymeleaf-layout-dialect.version>2.2.2</thymeleaf-layout-dialect.version>
</properties>
```
#### Thymeleaf使用
Thymeleaf的默认规则
```
@ConfigurationProperties(prefix = "spring.thymeleaf")
public class ThymeleafProperties {

	private static final Charset DEFAULT_ENCODING = Charset.forName("UTF-8");

	private static final MimeType DEFAULT_CONTENT_TYPE = MimeType.valueOf("text/html");

	public static final String DEFAULT_PREFIX = "classpath:/templates/";

	public static final String DEFAULT_SUFFIX = ".html";
  	//
```
只要我们把HTML页面放在`classpath:/templates/`，thymeleaf就能自动渲染；
- ==禁用掉Thymeleaf的缓存==  
在我们开发的时候，如果我们修改掉页面的元素，我们不重新启动项目，页面的元素还是原来的代码，如果我们想要在我们修改页面的时候，浏览器就会修改对应的代码的话，我们的第一步就需要禁用掉缓存：  

> 在配置文件中加上：`spring.thymeleaf.cache=false`
> 还需要在每次修改完之后按 Ctrl+F9 重新编译下
>


- 导入thymeleaf的名称空间
```xml
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```
- 使用thymeleaf语法；
```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>成功！</h1>
    <!--th:text 将div里面的文本内容设置为 -->
    <div th:text="${hello}">这是显示欢迎信息</div>
</body>
</html>
```
#### 7.3. 语法规则
#####  属性
> th: text；改变当前元素里面的文本内容；
>
> th：任意html属性；来替换原生属性的值
>
> ![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/06B73DCA17454E7F951B8E2020076BB2/10235)
##### 表达式

```
表达式语法：${},*{}:获取值; 
方式一：Variable Expressions: ${...}：获取变量值；OGNL；
    (1). 获取对象的属性、调用方法
        <h2 th:each="">每次遍历都会生成当前这个标签
    (2). 使用内置的基本对象：
    	#ctx : the context object.
    	#vars: the context variables.
        #locale : the context locale.
        #request : (only in Web Contexts) the HttpServletRequest object.
        #response : (only in Web Contexts) the HttpServletResponse object.
        #session : (only in Web Contexts) the HttpSession object.
        #servletContext : (only in Web Contexts) the ServletContext object.
        ${session.foo}
    (3). 内置的一些工具对象：
        #execInfo : information about the template being processed.
        #messages : methods for obtaining externalized messages inside variables expressions, in the same way as they would be obtained using #{…} syntax.
        #uris : methods for escaping parts of URLs/URIs
        #conversions : methods for executing the configured conversion service (if any).
        #dates : methods for java.util.Date objects: formatting, component extraction, etc.
        #calendars : analogous to #dates , but for java.util.Calendar objects.
        #numbers : methods for formatting numeric objects.
        #strings : methods for String objects: contains, startsWith, prepending/appending, etc.
        #objects : methods for objects in general.
        #bools : methods for boolean evaluation.
        #arrays : methods for arrays.
        #lists : methods for lists.
        #sets : methods for sets.
        #maps : methods for maps.
        #aggregates : methods for creating aggregates on arrays or collections.
        #ids : methods for dealing with id attributes that might be repeated (for example, as a result of an iteration).

方式二：Selection Variable Expressions: *{...}：选择表达式：和${}在功能上是一样；
    补充：配合 th:object="${session.user}：
    <div th:object="${session.user}">
        <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
        <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
        <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
    </div>
    上面代码的功能就类似于
    <div> 
        <p>Name: <span th:text="${session.user.firstName}">Sebastian</span>.</p>
        <p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p>
        <p>Nationality: <span th:text="${session.user.nationality}">Saturn</span>.</p> 
    </div>
方式三： Message Expressions: #{...}：获取国际化内容
方式四： Link URL Expressions: @{...}：定义URL；
    		@{/order/process(execId=${execId},execType='FAST')}
方式五： Fragment Expressions: ~{...}：片段引用表达式
    		<div th:insert="~{commons :: main}">...</div>
    		
Literals（字面量）
      Text literals: 'one text' , 'Another one!' ,…
      Number literals: 0 , 34 , 3.0 , 12.3 ,…
      Boolean literals: true , false
      Null literal: null
      Literal tokens: one , sometext , main ,…
Text operations:（文本操作）
    String concatenation: +
    Literal substitutions: |The name is ${name}|
Arithmetic operations:（数学运算）
    Binary operators: + , - , * , / , %
    Minus sign (unary operator): -
Boolean operations:（布尔运算）
    Binary operators: and , or
    Boolean negation (unary operator): ! , not
Comparisons and equality:（比较运算）
    Comparators: > , < , >= , <= ( gt , lt , ge , le )
    Equality operators: == , != ( eq , ne )
Conditional operators:条件运算（三元运算符）
    If-then: (if) ? (then)
    If-then-else: (if) ? (then) : (else)
    Default: (value) ?: (defaultvalue)
Special tokens:
    No-Operation: _ 
```
#### 7.4. 公共页面抽取
> 一个同类型的网页中有着大量的重复的内容,我们需要把重复的内容抽取出来,这样就会显得网页不是那么臃肿.  
##### 方式一：`{templatename::fragmentname}:模板名::片段名`

- 抽取公共片段

> 使用标签 th:fragment="xxx" 如：`<nav th:fragment="topbar">`
>

- 引入公共片段
> `<div th:replace="dashboard::topbar"></div>`  
> 其中`dashboard`指公共片段的文件名，`topbar`指公共片段所在文件的标签的`fragment`的名称  

##### 方式二：`{templatename::selector}:模板名::选择器`
- 抽取公共片段
> `<nav class="xxx" id="xxx">`

- 引入公共片段
> `<div th:replace="~{dashboard::#sidebar}"></div>`

##### 三种引用属性的区别
```
<footer th:fragment="copy">
&copy; 2011 The Good Thymes Virtual Grocery
</footer>

引入方式
<div th:insert="footer :: copy"></div>
<div th:replace="footer :: copy"></div>
<div th:include="footer :: copy"></div>

效果
<div>
    <footer>
    &copy; 2011 The Good Thymes Virtual Grocery
    </footer>
</div>

<footer>
&copy; 2011 The Good Thymes Virtual Grocery
</footer>

<div>
&copy; 2011 The Good Thymes Virtual Grocery
</div>
```

### 8. 国际化
#### 8.1 自动根据浏览器的请求头来自动选择
在以前使用springmvc要实现国际化，需要使用下面的步骤：
> 1. 编写国际化配置文件
> 2. 使用ResourcesBundleMessageSource管理国际化资源文件
> 3. 在页面使用fmt:message取出国际化的内容

但现在使用springboot，许多的配置已经帮我们配置好了，使用springboot来国际化的步骤：  
> 将每个页面要显示的内容编写到国际化配置文件中，抽取页面中需要显示的国际化消息  
>

下面以一个登录页面为例：  
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/07DC135467AC494BACC54C36107DC0BB/10233)  

- 首先编写默认的页面元素的配置文件，编写`login.properties`，因为要配置中文和英文，所以还需要编写`login_zh_CN.properties`(语言加国家)，和英文的`login_en_US.properties`但是现在只需要`login_en.properties`就可以了    
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/41EB6723C3A640DA879597E51484008A/10241)    
- 然后就可以编写配置文件的内容：  
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/FCA37DFC411E47FDB79EA60BFCBE76A6/10240)   
- 然后在HTML文件中引用即可
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/F399E62FE370466F9142546422AAC5D2/10243)  
- 然道我们新建的`i18n`文件夹就会被`springboot`给识别吗？我们可以通过查看源码：查看`MessageSourceAutoConfiguration`,我们可以发现springboot已经自动帮我们自动配置了国际化的组件：  
```
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnMissingBean(
    name = {"messageSource"},
    search = SearchStrategy.CURRENT
)
@AutoConfigureOrder(-2147483648)
@Conditional({MessageSourceAutoConfiguration.ResourceBundleCondition.class})
@EnableConfigurationProperties
public class MessageSourceAutoConfiguration {
     public ConditionOutcome getMatchOutcome(ConditionContext context, AnnotatedTypeMetadata metadata) {
            String basename = context.getEnvironment().getProperty("spring.messages.basename", "messages");
            ConditionOutcome outcome = (ConditionOutcome)cache.get(basename);
            if (outcome == null) {
                outcome = this.getMatchOutcomeForBasename(context, basename);
                cache.put(basename, outcome);
            }

            return outcome;
        }
}
```
> `String basename = context.getEnvironment().getProperty("spring.messages.basename", "messages");`
>

发现springboot帮我们默认配置的`basename`名字是`message`,所以如果我们直接在类路径下写`message.properties`文件的话，springboot就会自动为我们识别这是一个配置文件，但如果我们是自己指定的话，是需要使用`spring.messages.basename`来修改springboot的默认配置的  
- 所以要想使得我们之前编写的3个配置文件生效，我们需要自己在配置文件中修改默认配置：
```
spring.messages.basename=i18n.login
# login是去掉语言和国家代码余下的基本字符串
```
#### 8.2 用户自己点击选择切换语言
原理分析：所有的国际化的信息都是由一个叫`LocaleResolver`的组件来管理区域信息对象,获取区域信息对象，我们可以先去查看`springMVC`里面的默认配置`WebMvcAutoConfiguration`类中查看是否有配置了`LocaleResolver`组件：  
```
@Bean
@ConditionalOnMissingBean //如果用户没有自己配置就用这个，所以如果我们自己往容器中添加LocaleResolver组件的话就可以修改springboot默认的了
@ConditionalOnProperty(
    prefix = "spring.mvc",
    name = {"locale"}
)
public LocaleResolver localeResolver() {
    if (this.mvcProperties.getLocaleResolver() == org.springframework.boot.autoconfigure.web.servlet.WebMvcProperties.LocaleResolver.FIXED) {
        return new FixedLocaleResolver(this.mvcProperties.getLocale());
    } else {
        AcceptHeaderLocaleResolver localeResolver = new AcceptHeaderLocaleResolver();
        //AcceptHeaderLocaleResolver 就是通过请求头来获取区域信息
        localeResolver.setDefaultLocale(this.mvcProperties.getLocale());
        return localeResolver;
    }
}
```
所以如果用户自己点击选择切换语言，就需要把这个`LocaleResolver`区域解析器给换掉，用我们自己的：  
- 首先我们需要自己编写一个`LocaleResolver`组件(可以新建一个compoent)，实现我们自己的逻辑：
```
public class MyLocaleResolver implements LocaleResolver {
    @Override
    public Locale resolveLocale(HttpServletRequest httpServletRequest) {
        String l = httpServletRequest.getParameter("l");
        //从请求参数中获取我们需要指定的语言信息
        Locale locale = Locale.getDefault();//如果参数中没有带就用操作系统默认的
        if(!StringUtils.isEmpty(l)){
            //带过来区域信息
            String[] s = l.split("_");
            if(s.length == 1){
                locale = new Locale(s[0]);
                //可以用语言的信息来初始化一个Locale对象
            }else if(s.length == 2){
                locale = new Locale(s[0],s[1]);
                //可以用语言和国家的信息来初始化一个Locale对象
            }
            Locale.setDefault(locale);
            // 设置默认的Locale，与上面的getDefault()配对使用，防止因为没有带参数而始终使用默认的Locale
            // 这样就不需要每一次都需要携带l参数了，就算使用默认的也是用户自己选择的
        }
        return locale;
    }
    @Override
    public void setLocale(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Locale locale) {

    }
}
```
- 所以我们需要在HTML文件的链接中指定要切换的参数：
```
<a class="btn btn-sm" th:href="@{/index.html(l='zh_CN')}">中文</a>
<a class="btn btn-sm" th:href="@{/index.html(l='en')}">English</a>
```
- 最重要的是我们需要将我们自己的`LocaleResolver`的放入到容器中，这样springboot默认配置的`@ConditionalOnMissingBean`才会生效，可以新建一个config包专门用来放配置文件：
```
@Configuration
public class MyConfig {
    @Bean
    public LocaleResolver localeResolver(){
        return new MyLocaleResolver();
    }
    
}
```
### 9. 登录功能

#### 9.1. 解决登陆成功后刷新页面表单重复提交的问题
我们在输入用户名和密码，成功进入到主页面中的时候，如果我们重新刷新页面，会出现表单重复提交的问题，原因是因为我们在点击提交按钮的时候，是请求转发的方式来转发到主页面，所以在主页面中地址栏上面还是/login的请求地址，所以我们刷新页面会重复提交，要解决我们可以使用重定向的解决办法：  
```
@Controller
public class LoginController {

    @PostMapping(value = "/user/login")
    public String login(@RequestParam("username") String username,
                        @RequestParam("password") String password,
                        Map<String, Object> map){
        if(!StringUtils.isEmpty(username) && "123456".equals(password)){
            // 登录成功，使用重定向
            return "redirect:/main.html";
        }
        map.put("messsage", "用户名密码错误");
        return "login";
    }
    
    @RequestMapping("/main.html")
    public String main(){
        return "/dashboard";//跳转到主页
    }
}
```
> ==`redirect:/main.html`表示客户端再次发送`/main.html`的请求，所以地址栏会变成`/main.html`==,所以即使我们刷新页面也是客户端浏览器发送/main.html的请求   
> 还需要在加上`/main.html`的请求映射   
>

#### 9.2. 登录后页面的提示消息

> `<p style="color: red" th:text="${messsage}" th:if="${not #strings.isEmpty(messsage)}"></p>`

## 11. springboot 缓存
`JSR-107`的规范: `Java Caching` 定义了5个核心接口，分别是`CachingProvider`, `CacheManager`, `Cache`, `Entry`, `Expiry`.  
- `CachingProvider`: 定义了创建,配置,获取,管理和控制多个`CacheManager`,一个应用可以在运行期访问多个`CacheProvider`.  
- `CacheManager`: 定义了创建，配置，获取，管理和控制多个唯一的`cache`, 这些`cache`存在于`CacheManager`的上下文中。一个`CacheManager`仅被一个`CachingProvider`所拥有。  
- `Cache`: 是一个类似`Map`的数据结构并临时储存以`key`为索引的值。一个`cache`仅被一个`CacheManager`所拥有。  
- `Entry`: 是一个储存在`Cache`中`key-value`对.  
- `Expiry`: 每一个储存在`cache`中的条目有一个定义的有效期，一旦超过这个时间，条目为过期的状态。一旦过期，条目将不可访问，更新和删除。缓存有效期可以通过`ExpiryPolicy`来设置.    

### 快速体验缓存
- 开启基于注解的缓存 `@EnableCaching`
```
@SpringBootApplication
@EnableCaching
public class SpringbootCacheApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootCacheApplication.class, args);
    }
}
```
- 标注缓存注解即可 `@Cachable`, `@CacheEvict`, `@CachePut`
```
@Cacheable(cacheNames = "emp", key = "#id")
//@Cacheable:将方法的运行结果进行缓存，以后再要相同的数据，可以从缓存中获取
//几个属性:
//  cacheNames/value: 指定缓存的名字
//  key: 缓存数据使用的key，可以用它来指定，默认是方法的参数
public Employee getEmp(Integer id){
    System.out.println("查询id为 " + id + " 的员工");
    return employeeMapper.getEmployeeById(id);
}
```
### springboot与redis

## 12. springboot与消息
> 在大多数的应用，可以通过消息服务中间件来提升系统的异步通信，扩展解耦能力，在消息服务中有2个重要的概念： 
> 1. 消息代理
> 2. 目的地  

> 当消息发送者发送消息以后，将由消息代理接管，消息代理保证消息传递到指定目的地  

> 消息队列主要有2种形式的目的地  
> 1. 队列：点对点消息通信----消息发送者发送消息，消息代理将其放入一个队列中，消息接收者从队列中获取到消息的内容，消息读取后就会被移除队列，所以消息只有唯一的发送者和接收者，但并不能说只有一个接受者，可以有多个，只不过其他的接收不到而已    
> 2. 主题：发布/订阅消息通信----发送者发送消息到主题，多个接受者监听这个主题，那么就会在消息到达的时候同时接收到消息    

- 消息的来钟规范

> 1. `JMS`(`Java Message Service`)`Java`消息服务：基于`JVM`消息代理的规范，`ActiveMQ`，`HornetMQ`是`JMS`的实现  
> 2. `AMQP`(`Advanced Message Queuigng Protocol`)：高级消息协议，兼容`JMS`,`RabbitMQ`就是`AMQP`的实现  

### RabbitMQ
#### 核心概念
- `Message`:消息，消息是不具名的，它由消息头和消息体组成. 消息体是不透明的，而消息头则由一系列的可选属性组成。  
- `Publisher`: 消息的生产者，也是一个向交换器发布消息的客户端的应用程序  
- `Exchange`: 交换器，用来接收生产者发送的消息并将这些消息路由给服务器中的队列，有4种类型：`direct`(默认，类似于点对点的模型，根据一个`key`来区分不同的队列),`fanout`(类似于广播的模式，每个与该转换器上绑定的队列都会接收到),`topic`(支持模糊匹配,可以选择性的指定哪些队列可以接受，支持通配符,如下图)和`headers`,不同类型的`Exchange`转发消息的策略有所不同     
![image](https://note.youdao.com/yws/public/resource/9c4d4367ee472235b29630a0ea6dceca/xmlnote/D2D28CBCE47E473894C6EF8AECB149FB/17746)   
- `Queue`: 消息队列，用来保存消息，直到发送给消费者。他是消息的容器，也是消息的终点，一个消息可投入一个或多个队列。消息一直在队列中，等待消费者连接到这个队列将其取走。  
- `Binding`: 绑定，用于消息队列和交换器之间的关联。一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则，所以可以将交换器理解成一个绑定构成的路由表，`Exchange`和`Queue`的绑定可以是多对多的关系.  
- `Connection`: 网络连接，比如一个`TCP`连接  
- `Channel`: 用来解决多路复用的。  
![image](https://note.youdao.com/yws/public/resource/9c4d4367ee472235b29630a0ea6dceca/xmlnote/A6F863F06EAA4EC285B02A8FD57919F4/17726)    

#### docker的安装
- 使用`docker`的方式进行安装,可以去官网下载  
- 运行`rabbitMQ`
```
docker run -d -p 5672:5672 -p 15672:15672 --name myRabbitmq [imageid]
// -d：表示在后台运行
// -p: 表示暴露端口，rabbitMQ的管理后台有俩个端口，5672(通信)和15672(管理页面的端口)  
// --name: 给当前的容器起一个名字
```
- 访问：`http://ip:15672/`  
- 登录：用户名：`guest`， 密码：`guest`  

#### 测试小案例
##### 通过前端页面方式操作
创建下列的场景  
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/A197D23F22934871986C4FDD1785861C/17786)  
- 创建一个`exchange.direct`交换器  
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/ACAB3FB60B5947D5BB925389523830A1/17793)  
- 然后按同样的方法创建其他的几个交换器  
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/BB4BB16BC5FB475AB9A2AA26505A5558/17798)   
- 添加消息队列
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/9C4E488A6F634D2282F0D59CC93E2970/17803)  
- 按照上面的规则将队列绑定到交换器  
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/55FA9A8F95C5439F888603A962653DF4/17813)  
`topic`的绑定情况：  
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/A21C6A301ADC472A840ECA4897B6EF60/17820)  
- 在`exchange.direct`交换器上向`atguigu`交换器上发送`message`:  
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/5E7D5F1C901F4454A615977551A7B0CC/17829)  
然后发现在`atguigu`的消息队列上有对应的消息数据  
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/84F4F0A6029D40F8BC16B1588127454F/17835)  
上面是使用`direct`的交换器上发送，下面是`fanout`的方式进行发送  
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/39BCBB333B354EC388401E064FBC0A74/17843)   
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/B56E8C87843F46E59B5B602CD7B789B5/17847)  

##### 通过Java工程方式操作
- `pom.xml`
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```
- 测试点对点的消息

> 发送消息

```
@Test
void contextLoads() {
    // 方式一：rabbitTemplate.send(Exchange,Routekey,Message);
    // 需要传递交换器，路由键,和一个自定义的message对象
    // 方式二：rabbitTemplate.convertAndSend(Exchange,Routekey,Object);
    // 需要传递交换器，路由键,和发送的对象，自动序列化,Object默认当成消息体
    Map<String, Object> map = new HashMap<>();
    map.put("msg", "第一个消息");
    map.put("data", Arrays.asList("hello", "wuhuihua", 123, true));
    rabbitTemplate.convertAndSend("exchange.direct", "atguigu.news", map);
}
```
结果：  
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/D9089D21903F4FB1B8E553DB1C077B72/17859)   
如果直接发送的话，默认使用的是`Java`内置的序列化机制进行序列化，我们可以自己指定序列化的工具，需要进行配置指定自己的配置
```
@Configuration
public class MyRabbitMQConfig {
    @Bean
    public MessageConverter messageConverter(){
        return new Jackson2JsonMessageConverter();
    }
}
```

> 接收消息

```
@Test
public void receive(){
    Object o = rabbitTemplate.receiveAndConvert("atguigu.news");
    // 接收消息只需要提供一个queue的名称即可
    System.out.println("接收到的数据类型：" + o.getClass());
    System.out.println(o);
}
```
结果：
```
接收到的数据类型：class java.util.HashMap
{msg=第一个消息, data=[hello, wuhuihua, 123, true]}
上面发送的是一个map对象，以后经常我们发送的是其他数据库对应的bean对象  
```

- 其他的`fanout`和`public`的方式和上面的类似    

#### rabbitMQ监听
场景：一个订单和一个库存系统，某一个人下单之后，将消息放入到消息队列中，然后库存系统实时的监听队列的内容，一旦有新的订单下来，就会触发相应的操作  
```
@Service
public class BookService {

    @RabbitListener(queues = "atguigu.news")
    // 指定如果atguigu.news的队列中接收到消息，这个方法就会执行
    // 要使@RabbitListene有效,必须在主类上面使用@EnableRabbit 开启注解的rabbitmq模式
    public void receive(String book){
        System.out.println("收到一个book的订单消息:" + book);
    }
}
```
如果还想要获取到消息的头信息等，可以使用`message`的对象：
```
@RabbitListener(queues = "atguigu.news")
// 指定如果atguigu.news的队列中接收到消息，这个方法就会执行
public void receiveMessage(Message book){
    System.out.println("收到一个book消息body:" + book.getBody());
    System.out.println("收到一个book消息头消息:" + book.getMessageProperties());
}
```
#### 在程序中创建Exchange,queue  
在上面的程序中，`Exchange`,`queue`，以及他们之间的关联信息都是通过管理页面来实现的，但有的时候需要通过程序来实现，这个时候就需要使用到`AmqpAdmin`对象，由他来创建和删除`Queue`,`exchange`,`banding`规则  
```
@Test
public void createExchange(){
    // 创建一个Direct的Exchange，并指定的名称
    // amqpAdmin.declareExchange(new DirectExchange("exchange.amqpadmin"));
    // 创建一个queue,指定一个名称和持久化
    // amqpAdmin.declareQueue(new Queue("queue.amqpadmin", true));
    // 创建绑定的规则
    // amqpAdmin.declareBinding(new Binding("queue.amqpadmin", Binding.DestinationType.QUEUE, "exchange.amqpadmin","amqp.haha", null));
}
```

## 13. springboot与检索
`ElasticSearch`: `ElasticSearch`是一个分布式搜索的服务，提供`Restful API`, 底层基于`Lucene`,采用多`shard(分片)`的方式保证数据的安全，并且提供自动`Resharding`的功能，`github`等大型的站点也是采用`ElasticSearch`作为其搜索服务  
### Docker安装ElasticSearch
```
// 先查看elasticsearch
root@ubuntu:/usr/local/softwares# docker search elasticsearch
// 结果
NAME                                 DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
elasticsearch                        Elasticsearch is a powerful open source sear…   4543                [OK]                
nshou/elasticsearch-kibana           Elasticsearch-7.7.0 Kibana-7.7.0                119                                     [OK]
itzg/elasticsearch                   Provides an easily configurable Elasticsearc…   70                                      [OK]
mobz/elasticsearch-head              elasticsearch-head front-end and standalone …   65                                      
elastichq/elasticsearch-hq           Official Docker image for ElasticHQ: Elastic…   61                                      [OK]
elastic/elasticsearch                The Elasticsearch Docker image maintained by…   37                                      
bitnami/elasticsearch                Bitnami Docker Image for Elasticsearch          34                                      [OK]
taskrabbit/elasticsearch-dump        Import and export tools for elasticsearch       23                                      [OK]
lmenezes/elasticsearch-kopf          elasticsearch kopf                              18                                      [OK]
barnybug/elasticsearch               Latest Elasticsearch 1.7.2 and previous rele…   17                                      [OK]
justwatch/elasticsearch_exporter     Elasticsearch stats exporter for Prometheus     15                                      
esystemstech/elasticsearch           Debian based Elasticsearch packing for Lifer…   15                                      
blacktop/elasticsearch               Alpine Linux based Elasticsearch Docker Image   13                                      [OK]
monsantoco/elasticsearch             ElasticSearch Docker image                      11                                      [OK]
mesoscloud/elasticsearch             [UNMAINTAINED] Elasticsearch                    9                                       [OK]
centerforopenscience/elasticsearch   Elasticsearch                                   4                                       [OK]
dtagdevsec/elasticsearch             elasticsearch                                   3                                       [OK]
barchart/elasticsearch-aws           Elasticsearch AWS node                          3                                       
bitnami/elasticsearch-exporter       Bitnami Elasticsearch Exporter Docker Image     2                                       [OK]
axway/elasticsearch-docker-beat      "Beat" extension to read logs of containers …   1                                       [OK]
phenompeople/elasticsearch           Elasticsearch is a powerful open source sear…   1                                       [OK]
jetstack/elasticsearch-pet           An elasticsearch image for kubernetes PetSets   1                                       [OK]
wreulicke/elasticsearch              elasticsearch                                   0                                       [OK]
18fgsa/elasticsearch                 Built from https://github.com/docker-library…   0                                       
18fgsa/elasticsearch-ha              Built from https://github.com/18F/kubernetes…   0                                       
// 安装官方的版本
root@ubuntu:/usr/local/softwares# docker pull elasticsearch  

// 启动
root@ubuntu:/usr/local/softwares# docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9200:9200 -p 9300:9300 --name ES01 5acf0e8da90b
// elasticsearch默认初始会占用2个G的内存，所以为了运行成功，可以限制
// 使用 -e命令限制内存的使用大小，-Xms256m：初始的堆内存大小，-Xmx256m：最大的使用的堆内存大小  
// elasticsearch默认与外部通信使用9200端口，当分布式各个节点的通信使用的是9300端口
```
然后就可以通过浏览器访问： `http://192.168.139.129:9200/`
```
{
  "name" : "MxjXPPS",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "lBqYkxiFRleFartSTuNitw",
  "version" : {
    "number" : "5.6.12",
    "build_hash" : "cfe3d9f",
    "build_date" : "2018-09-10T20:12:43.732Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
// 出现上面的情况就表示安装成功
```
###  elasticsearch的基本知识
可以查看 [官方文档](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)  

- 小案例：索引雇员文档  
业务需求：储存雇员数据，这将会以文档的方式的形式储存，一个文档代表一个雇员，储存到`Elasticsearch`的==行为==叫做索引，但在索引一个文档之前，需要确定将文档储存在哪里。  
一个`Elasticsearch`集群可以包含多个索引，相应的每个索引可以包含多个类型，这些不同的类型储存着多个文档，且每一个文档又有多个属性。  

```
每个员工索引一个文档，文档包含该员工的所有信息。
每个文档都将是 employee 类型 。
该类型位于 索引 megacorp 内。
该索引保存在我们的 Elasticsearch 集群中。
```

> 与数据库类似地方：   
> 索引 -> 数据库  
> 类型 -> 表 
> 文档 -> 表中的数据  
> 属性 -> 列    
> ![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/D6F55859A4764BB188AFF763CD4064F1/17984)   

- 储存数据：打开`postman`工具  
```
PUT /megacorp/employee/1
{
    "first_name" : "John",
    "last_name" :  "Smith",
    "age" :        25,
    "about" :      "I love to go rock climbing",
    "interests": [ "sports", "music" ]
}
```
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/8F6B72E1A9BD47219A9535A9AEBE5C8F/17994)   
返回：
```
{
    "_index": "megacorp",
    "_type": "employee",
    "_id": "1",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "created": true
}
```
- 检索文档  
目前我们已经在 `Elasticsearch` 中存储了一些数据， 接下来就能专注于实现应用的业务需求了。第一个需求是可以检索到单个雇员的数据。  
这在 `Elasticsearch` 中很简单。简单地执行 一个 `HTTP GET` 请求并指定文档的地址——索引库、类型和`ID`。 使用这三个信息可以返回原始的 `JSON` 文档：   
```
GET /megacorp/employee/1
```
因为 `Elasticsearch` 是支持 `restful` 风格的 `API`, 所以在使用的时候，`put`是添加，`get`是获取,`delete`是删除，`head`是查看是否存在等等,如果想要更新，可以再次使用`put`的命令，然后对应的数据会进行更新，然后对应的`version`会增加1   

#### 轻量搜索
一个 `GET` 是相当简单的，可以直接得到指定的文档。现在尝试点儿稍微高级的功能，比如一个简单的搜索！   
第一个尝试的几乎是最简单的搜索了。我们使用下列请求来搜索所有雇员：
```
GET /megacorp/employee/_search
```
可以看到，我们仍然使用索引库`megacorp`以及类型`employee`，但与指定一个文档 `ID` 不同，这次使用`_search`。返回结果包括了所有三个文档，放在数组 `hits` 中。一个搜索默认返回十条结果。  
```
{
   "took":      6,
   "timed_out": false,
   "_shards": { ... },
   "hits": {
      "total":      3,
      "max_score":  1,
      "hits": [
         {
            "_index":         "megacorp",
            "_type":          "employee",
            "_id":            "3",
            "_score":         1,
            "_source": {
               "first_name":  "Douglas",
               "last_name":   "Fir",
               "age":         35,
               "about":       "I like to build cabinets",
               "interests": [ "forestry" ]
            }
         },
         {
            "_index":         "megacorp",
            "_type":          "employee",
            "_id":            "1",
            "_score":         1,
            "_source": {
               "first_name":  "John",
               "last_name":   "Smith",
               "age":         25,
               "about":       "I love to go rock climbing",
               "interests": [ "sports", "music" ]
            }
         },
         {
            "_index":         "megacorp",
            "_type":          "employee",
            "_id":            "2",
            "_score":         1,
            "_source": {
               "first_name":  "Jane",
               "last_name":   "Smith",
               "age":         32,
               "about":       "I like to collect rock albums",
               "interests": [ "music" ]
            }
         }
      ]
   }
}
```
接下来，尝试下搜索姓氏为 ``Smith`` 的雇员。为此，我们将使用一个 高亮 搜索，很容易通过命令行完成。这个方法一般涉及到一个 查询字符串 （`query-string`） 搜索，因为我们通过一个`URL`参数来传递查询信息给搜索接口：
```
GET /megacorp/employee/_search?q=last_name:Smith
// 字段：值
```
- 使用查询表达式搜索
```
http://192.168.139.129:9200/megacorp/employee/_search

{
    "query" : {
        "match" : {
            "last_name" : "Smith"
        }
    }
}
```
- 更复杂的搜索  
现在尝试下更复杂的搜索。同样搜索姓氏为`Smith`的员工，但这次我们只需要年龄大于 `30`的。查询需要稍作调整，使用过滤器`filter`，它支持高效地执行一个结构化查询。  
```
GET /megacorp/employee/_search
{
    "query" : {
        "bool": {
            "must": {
                "match" : {
                    "last_name" : "smith" 
                    // 这部分与我们之前使用的 match 查询 一样。
                }
            },
            "filter": {
                "range" : {
                    "age" : { "gt" : 30 } 
                    // 	这部分是一个 range 过滤器 ，它能找到年龄大于 30 的文档，其中 gt 表示_大于_(great than)。
                }
            }
        }
    }
}
```
#### 全文检索
搜索下所有喜欢攀岩（`rock climbing`）的员工：  
```
GET /megacorp/employee/_search
{
    "query" : {
        "match" : {
            "about" : "rock climbing"
        }
    }
}
```
显然我们依旧使用之前的 `match` 查询在`about` 属性上搜索 `rock climbing`。得到两个匹配的文档：  
```
{
   ...
   "hits": {
      "total":      2,
      "max_score":  0.16273327,
      "hits": [
         {
            ...
            "_score":         0.16273327, 
            // 相关性得分
            "_source": {
               "first_name":  "John",
               "last_name":   "Smith",
               "age":         25,
               "about":       "I love to go rock climbing",
               "interests": [ "sports", "music" ]
            }
         },
         {
            ...
            "_score":         0.016878016, 
            "_source": {
               "first_name":  "Jane",
               "last_name":   "Smith",
               "age":         32,
               "about":       "I like to collect rock albums",
               "interests": [ "music" ]
            }
         }
      ]
   }
}
```
`Elasticsearch` 默认按照相关性得分排序，即每个文档跟查询的匹配程度。第一个最高得分的结果很明显：`John Smith` 的 `about` 属性清楚地写着 `rock climbing` 。

但为什么 `Jane Smith` 也作为结果返回了呢？原因是她的 `about` 属性里提到了 `rock` 。因为只有 `rock` 而没有 `climbing` ，所以她的相关性得分低于 `John` 的。

这是一个很好的案例，阐明了 `Elasticsearch` 如何 在 全文属性上搜索并返回相关性最强的结果。`Elasticsearch`中的 ==相关性== 概念非常重要，也是完全区别于传统关系型数据库的一个概念，数据库中的一条记录要么匹配要么不匹配。  

#### 短语搜索
发现上面`Jane Smith`，只有 `rock` 而没有 `climbing`也会被搜索出来，但有的时候，我们并不希望这也会匹配，这个时候，我们可以使用短语搜索`match_phrase`的方式:  
```
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    }
}
```
- 高亮搜索  
许多应用都倾向于在每个搜索结果中高亮部分文本片段，以便让用户知道为何该文档符合查询条件。在`Elasticsearch` 中检索出高亮片段也很容易。  
再次执行前面的查询，并增加一个新的 `highlight` 参数：
```
GET /megacorp/employee/_search
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    },
    "highlight": {
        "fields" : {
            "about" : {}
        }
    }
}
```
当执行该查询时，返回结果与之前一样，与此同时结果中还多了一个叫做 `highlight` 的部分。这个部分包含了 `about` 属性匹配的文本片段，并以` HTM`L 标签 `<em></em>` 封装：  
```
{
   ...
   "hits": {
      "total":      1,
      "max_score":  0.23013961,
      "hits": [
         {
            ...
            "_score":         0.23013961,
            "_source": {
               "first_name":  "John",
               "last_name":   "Smith",
               "age":         25,
               "about":       "I love to go rock climbing",
               "interests": [ "sports", "music" ]
            },
            "highlight": {
               "about": [
                  "I love to go <em>rock</em> <em>climbing</em>" 
               ]
            }
         }
      ]
   }
}
```
#### springboot整合Elasticsearch
- `springBoot`默认支持俩种技术来和 `ES` 交互  

> 1.`Jest`(默认不生效，需要导入)  
> 2.`SpringData Elasticsearch`  

`springboot`会自动配置了下面的
> 1) `Client`节点消息： `clusterNodes` `clusterName  `
> 2) `ElasticsearchTemplate`来操作`ES`  
> 3) 也可以自己来编写`ElasticSearchRepository`的子接口来操作`ES`

##### 使用 SpringData 的方式来操作
- `pom.xml`
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
</dependency>
```
==【注意版本==】引入上面的版本以后，必须保证和本地的`elasticsearch`的版本相同:  
![image](https://note.youdao.com/yws/public/resource/1e6ee5fcebcf2116f178e6b3036d49f7/xmlnote/7B444F3BF7AB45DA81AD451586C0504B/18095)  
如果不同，可以自己在`propertise`中自己指定`elasticsearch`的版本
```
<elasticsearch.version>7.6.2</elasticsearch.version>
```
- 使用编写配置类的方式来配置`elasticsearch`
```
@Configuration
public class EsConf {
    @Bean
    public RestHighLevelClient restHighLevelClient() {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(
                        new HttpHost("192.168.139.129",9200,"http")));
        return client;
    }
}
```
- 操作索引：
```
@Autowired
private RestHighLevelClient restHighLevelClient;

/**
 * 测试索引的创建
 */
@Test
void contextLoads() throws IOException {
    //1.创建索引请求
    CreateIndexRequest indexRequest = new CreateIndexRequest("test");
    //2.执行请求
    CreateIndexResponse indexResponse = restHighLevelClient.indices().create(indexRequest, RequestOptions.DEFAULT);
    System.out.println(indexResponse);
}

/**
 * 判断索引是否存在
 */
@Test
void testIndexExist() throws IOException {
    GetIndexRequest request = new GetIndexRequest("test");
    boolean exists = restHighLevelClient.indices().exists(request, RequestOptions.DEFAULT);
    System.out.println("test是否存在："+exists);
}

/**
 * 删除索引
 */
@Test
void testDeleteIndex() throws IOException {
    DeleteIndexRequest request = new DeleteIndexRequest("test");
    AcknowledgedResponse delete = restHighLevelClient.indices().delete(request, RequestOptions.DEFAULT);
    System.out.println("test是否删除："+delete);
}
```
- 操作文档
```
@Autowired
private RestHighLevelClient restHighLevelClient;

/**
 * 测试添加文档
 */
@Test
void testAddDocument() throws IOException {
    Book book01 = new Book(1, "book01");
    // 创建一个请求，指定放到哪个索引里面
    IndexRequest indexRequest = new IndexRequest("test");
    indexRequest.type("aa");
    indexRequest.id("1");
    // 将数据放入请求
    indexRequest.source(JSON.toJSONString(book01), XContentType.JSON);
    // 客户端发送请求
    IndexResponse index = restHighLevelClient.index(indexRequest, RequestOptions.DEFAULT);

    System.out.println(index.toString() + "----" + index.status());
}

/**
 * 获取文档消息
 */
@Test
void testGetDocument() throws IOException {
    GetRequest request = new GetRequest("test","1");
    GetResponse getResponse = restHighLevelClient.get(request, RequestOptions.DEFAULT);
    System.out.println(getResponse.getSourceAsString());
}

/**
 * 测试更新文档消息
 */
@Test
void testUpdateDocument() throws IOException {
    UpdateRequest request = new UpdateRequest("test","1");
    request.timeout("1s");
    Book book = new Book(1, "wuhuihua");
    request.doc(JSON.toJSONString(book), XContentType.JSON);

    UpdateResponse updateResponse = restHighLevelClient.update(request, RequestOptions.DEFAULT);
}

/**
 * 删除文档消息
 */
@Test
void testDeleteDocument() throws IOException {
    DeleteRequest deleteRequest = new DeleteRequest("test", "1");
    deleteRequest.timeout("1s");
    DeleteResponse delete = restHighLevelClient.delete(deleteRequest, RequestOptions.DEFAULT);
    System.out.println(delete.toString() + "----" + delete.status());
}
```

==【注意】== 发现在操作文档的时候，并没有指定`type`,那是因为`elasticsearch7`默认不在支持指定索引类型，默认索引类型是`_doc`，如果想改变，则配置`include_type_name: true` 即可(这个没有测试，官方文档说的，无论是否可行，建议不要这么做，因为`elasticsearch8`后就不在提供该字段)。[官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/removal-of-types.html)   

- 批量操作数据
```
/**
 * 批量插入数据
 */
@Test
void testPiAdd() throws IOException {
    BulkRequest bulkRequest = new BulkRequest();
    bulkRequest.timeout("10s");
    ArrayList<Book> books = new ArrayList<>();
    books.add(new Book(2,"wu"));
    books.add(new Book(3,"hui"));
    books.add(new Book(4,"hua"));
    // 创建一个批处理请求
    for (int i = 0; i < books.size(); i++) {
        bulkRequest.add(new IndexRequest("test").id("" +(i+1)).source(JSON.toJSONString(books.get(i)), XContentType.JSON));
    }
    BulkResponse bulkItemResponses = restHighLevelClient.bulk(bulkRequest, RequestOptions.DEFAULT);
    System.out.println(bulkItemResponses.toString() + "----" + bulkItemResponses.status());
}
```
- 搜索
```
/**
 * search
 */
@Test
void testSearch() throws IOException {
    SearchRequest searchRequest = new SearchRequest("test");
    SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
    // 构建搜索条件,termQuery表示精确匹配 name == "wu"
    // .. 可以用QueryBuilders自己匹配
    TermQueryBuilder queryBuilder = QueryBuilders.termQuery("name", "wu");
    // 将查询条件放入到SearchSourceBuilder，进行查询
    sourceBuilder.query(queryBuilder);
    // 下面可以设置分页
    // sourceBuilder.from();
    // sourceBuilder.size();
    sourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));
    searchRequest.source(sourceBuilder);

    SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);
    System.out.println(JSON.toJSONString(searchResponse.getHits()));
    System.out.println("==========================");
    for (SearchHit documentFields : searchResponse.getHits()){
        System.out.println(documentFields.getSourceAsMap());
    }
}
```