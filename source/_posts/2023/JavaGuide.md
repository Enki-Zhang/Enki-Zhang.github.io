---
title: JavaGuide
top: false
cover: false
toc: true
mathjax: true
date: 2023-03-11 12:57:31
password:
summary:
tags:
categories:
---

剖析面试最常见问题之 Spring Boot

市面上关于 Spring Boot 的面试题抄来抄去，毫无价值可言。

这篇文章，我会简单就自己这几年使用 Spring Boot 的一些经验，总结一些常见的面试题供小伙伴们自测和学习。少部分关于 Spring/Spring Boot 的介绍参考了官网，其他皆为原创。

1. 简单介绍一下 Spring?有啥缺点?

Spring 是重量级企业开发框架 Enterprise JavaBean（EJB） 的替代品，Spring 为企业级 Java 开发提供了一种相对简单的方法，通过 依赖注入 和 面向切面编程 ，用简单的 Java 对象（Plain Old Java Object，POJO） 实现了 EJB 的功能

虽然 Spring 的组件代码是轻量级的，但它的配置却是重量级的（需要大量 XML 配置） 。

为此，Spring 2.5 引入了基于注解的组件扫描，这消除了大量针对应用程序自身组件的显式 XML 配置。Spring 3.0 引入了基于 Java 的配置，这是一种类型安全的可重构配置方式，可以代替 XML。

尽管如此，我们依旧没能逃脱配置的魔爪。开启某些 Spring 特性时，比如事务管理和 Spring MVC，还是需要用 XML 或 Java 进行显式配置。启用第三方库时也需要显式配置，比如基于 Thymeleaf 的 Web 视图。配置 Servlet 和过滤器（比如 Spring 的 DispatcherServlet）同样需要在 web.xml 或 Servlet 初始化代码里进行显式配置。组件扫描减少了配置量，Java 配置让它看上去简洁不少，但 Spring 还是需要不少配置。

光配置这些 XML 文件都够我们头疼的了，占用了我们大部分时间和精力。除此之外，相关库的依赖非常让人头疼，不同库之间的版本冲突也非常常见。

2. 为什么要有 SpringBoot?

Spring 旨在简化 J2EE 企业应用程序开发。Spring Boot 旨在简化 Spring 开发（减少配置文件，开箱即用！）。

3. 说出使用 Spring Boot 的主要优点

1
开发基于 Spring 的应用程序很容易。
2
Spring Boot 项目所需的开发或工程时间明显减少，通常会提高整体生产力。
3
Spring Boot 不需要编写大量样板代码、XML 配置和注释。
4
Spring 引导应用程序可以很容易地与 Spring 生态系统集成，如 Spring JDBC、Spring ORM、Spring Data、Spring Security 等。
5
Spring Boot 遵循“固执己见的默认配置”，以减少开发工作（默认配置可以修改）。
6
Spring Boot 应用程序提供嵌入式 HTTP 服务器，如 Tomcat 和 Jetty，可以轻松地开发和测试 web 应用程序。（这点很赞！普通运行 Java 程序的方式就能运行基于 Spring Boot web 项目，省事很多）
7
Spring Boot 提供命令行接口(CLI)工具，用于开发和测试 Spring Boot 应用程序，如 Java 或 Groovy。
8
Spring Boot 提供了多种插件，可以使用内置工具(如 Maven 和 Gradle)开发和测试 Spring Boot 应用程序。

4. 什么是 Spring Boot Starters?

Spring Boot Starters 是一系列依赖关系的集合，因为它的存在，项目的依赖之间的关系对我们来说变的更加简单了。

举个例子：在没有 Spring Boot Starters 之前，我们开发 REST 服务或 Web 应用程序时; 我们需要使用像 Spring MVC，Tomcat 和 Jackson 这样的库，这些依赖我们需要手动一个一个添加。但是，有了 Spring Boot Starters 我们只需要一个只需添加一个 spring-boot-starter-web 一个依赖就可以了，这个依赖包含的子依赖中包含了我们开发 REST 服务需要的所有依赖。

5. Spring Boot 支持哪些内嵌 Servlet 容器？

Spring Boot 支持以下嵌入式 Servlet 容器:
Name
Servlet Version
Tomcat 9.0
4.0
Jetty 9.4
3.1
Undertow 2.0
4.0

您还可以将 Spring 引导应用程序部署到任何 Servlet 3.1+兼容的 Web 容器中。

这就是你为什么可以通过直接像运行 普通 Java 项目一样运行 SpringBoot 项目。这样的确省事了很多，方便了我们进行开发，降低了学习难度。

6. 如何在 Spring Boot 应用程序中使用 Jetty 而不是 Tomcat?

Spring Boot （spring-boot-starter-web）使用 Tomcat 作为默认的嵌入式 servlet 容器, 如果你想使用 Jetty 的话只需要修改 pom.xml(Maven)或者 build.gradle(Gradle)就可以了。

Maven:

Gradle:

说个题外话，从上面可以看出使用 Gradle 更加简洁明了，但是国内目前还是 Maven 使用的多一点，我个人觉得 Gradle 在很多方面都要好很多。

7. 介绍一下@SpringBootApplication 注解

可以看出大概可以把 @SpringBootApplication 看作是 @Configuration、@EnableAutoConfiguration、@ComponentScan 注解的集合。根据 SpringBoot 官网，这三个注解的作用分别是：

●
@EnableAutoConfiguration：启用 SpringBoot 的自动配置机制
●
@ComponentScan： 扫描被@Component (@Service,@Controller)注解的 bean，注解默认会扫描该类所在的包下所有的类。
●
@Configuration：允许在上下文中注册额外的 bean 或导入其他配置类

8. Spring Boot 的自动配置是如何实现的?

这个是因为@SpringBootApplication 注解的原因，在上一个问题中已经提到了这个注解。我们知道 @SpringBootApplication 看作是 @Configuration、@EnableAutoConfiguration、@ComponentScan 注解的集合。

●
@EnableAutoConfiguration：启用 SpringBoot 的自动配置机制
●
@ComponentScan： 扫描被@Component (@Service,@Controller)注解的 bean，注解默认会扫描该类所在的包下所有的类。
●
@Configuration：允许在上下文中注册额外的 bean 或导入其他配置类

@EnableAutoConfiguration 是启动自动配置的关键，源码如下(建议自己打断点调试，走一遍基本的流程)：

@EnableAutoConfiguration 注解通过 Spring 提供的 @Import 注解导入了 AutoConfigurationImportSelector 类（@Import 注解可以导入配置类或者 Bean 到当前类中）。

AutoConfigurationImportSelector 类中 getCandidateConfigurations 方法会将所有自动配置类的信息以 List 的形式返回。这些配置信息会被 Spring 容器作 bean 来管理。

自动配置信息有了，那么自动配置还差什么呢？

@Conditional 注解。@ConditionalOnClass(指定的类必须存在于类路径下),@ConditionalOnBean(容器中是否有指定的 Bean)等等都是对@Conditional 注解的扩展。

拿 Spring Security 的自动配置举个例子:SecurityAutoConfiguration 中导入了 WebSecurityEnablerConfiguration 类，WebSecurityEnablerConfiguration 源代码如下：

WebSecurityEnablerConfiguration 类中使用@ConditionalOnBean 指定了容器中必须还有 WebSecurityConfigurerAdapter 类或其实现类。所以，一般情况下 Spring Security 配置类都会去实现 WebSecurityConfigurerAdapter，这样自动将配置就完成了。

9. 开发 RESTful Web 服务常用的注解有哪些？

Spring Bean 相关：

●
@Autowired : 自动导入对象到类中，被注入进的类同样要被 Spring 容器管理。
●
@RestController : @RestController 注解是@Controller 和@ResponseBody 的合集,表示这是个控制器 bean,并且是将函数的返回值直 接填入 HTTP 响应体中,是 REST 风格的控制器。
●
@Component ：通用的注解，可标注任意类为 Spring 组件。如果一个 Bean 不知道属于哪个层，可以使用@Component 注解标注。
●
@Repository : 对应持久层即 Dao 层，主要用于数据库相关操作。
●
@Service : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
●
@Controller : 对应 Spring MVC 控制层，主要用于接受用户请求并调用 Service 层返回数据给前端页面。

处理常见的 HTTP 请求类型：

●
@GetMapping : GET 请求、
●
@PostMapping : POST 请求。
●
@PutMapping : PUT 请求。
●
@DeleteMapping : DELETE 请求。

前后端传值：

●
@RequestParam 以及@Pathvairable ：@PathVariable 用于获取路径参数，@RequestParam 用于获取查询参数。
●
@RequestBody ：用于读取 Request 请求（可能是 POST,PUT,DELETE,GET 请求）的 body 部分并且 Content-Type 为 application/json 格式的数据，接收到数据之后会自动将数据绑定到 Java 对象上去。系统会使用 HttpMessageConverter 或者自定义的 HttpMessageConverter 将请求的 body 中的 json 字符串转换为 java 对象。

详细介绍可以查看这篇文章：《Spring/Spring Boot 常用注解总结》 。

10. Spirng Boot 常用的两种配置文件

我们可以通过 application.properties 或者 application.yml 对 Spring Boot 程序进行简单的配置。如果，你不进行配置的话，就是使用的默认配置。

11. 什么是 YAML？YAML 配置的优势在哪里 ?

YAML 是一种人类可读的数据序列化语言。它通常用于配置文件。与属性文件相比，如果我们想要在配置文件中添加复杂的属性，YAML 文件就更加结构化，而且更少混淆。可以看出 YAML 具有分层配置数据。

相比于 Properties 配置的方式，YAML 配置的方式更加直观清晰，简介明了，有层次感。

但是，YAML 配置的方式有一个缺点，那就是不支持 @PropertySource 注解导入自定义的 YAML 配置。

12. Spring Boot 常用的读取配置文件的方法有哪些？

我们要读取的配置文件 application.yml 内容如下：

12.1. 通过 @value 读取比较简单的配置信息

使用 @Value("${property}") 读取比较简单的配置信息：

需要注意的是 @value 这种方式是不被推荐的，Spring 比较建议的是下面几种读取配置信息的方式。

12.2. 通过@ConfigurationProperties 读取并与 bean 绑定

LibraryProperties 类上加了 @Component 注解，我们可以像使用普通 bean 一样将其注入到类中使用。

这个时候你就可以像使用普通 bean 一样，将其注入到类中使用：

控制台输出：

12.3. 通过@ConfigurationProperties 读取并校验

我们先将 application.yml 修改为如下内容，明显看出这不是一个正确的 email 格式：

ProfileProperties 类没有加 @Component 注解。我们在我们要使用 ProfileProperties 的地方使用@EnableConfigurationProperties 注册我们的配置 bean：

具体使用：

因为我们的邮箱格式不正确，所以程序运行的时候就报错，根本运行不起来，保证了数据类型的安全性：

我们把邮箱测试改为正确的之后再运行，控制台就能成功打印出读取到的信息：

12.4. @PropertySource 读取指定的 properties 文件

使用：

13. Spring Boot 加载配置文件的优先级了解么？

Spring 读取配置文件也是有优先级的，直接上图：

更对内容请查看官方文档：https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config

14. 常用的 Bean 映射工具有哪些？

我们经常在代码中会对一个数据结构封装成 DO、SDO、DTO、VO 等，而这些 Bean 中的大部分属性都是一样的，所以使用属性拷贝类工具可以帮助我们节省大量的 set 和 get 操作。

常用的 Bean 映射工具有：Spring BeanUtils、Apache BeanUtils、MapStruct、ModelMapper、Dozer、Orika、JMapper 。

由于 Apache BeanUtils 、Dozer 、ModelMapper 性能太差，所以不建议使用。MapStruct 性能更好而且使用起来比较灵活，是一个比较不错的选择。

15. Spring Boot 如何监控系统实际运行状况？

我们可以使用 Spring Boot Actuator 来对 Spring Boot 项目进行简单的监控。

集成了这个模块之后，你的 Spring Boot 应用程序就自带了一些开箱即用的获取程序运行时的内部状态信息的 API。

比如通过 GET 方法访问 /health 接口，你就可以获取应用程序的健康指标。

16. Spring Boot 如何做请求参数校验？

数据的校验的重要性就不用说了，即使在前端对数据进行校验的情况下，我们还是要对传入后端的数据再进行一遍校验，避免用户绕过浏览器直接通过一些 HTTP 工具直接向后端请求一些违法数据。

Spring Boot 程序做请求参数校验的话只需要 spring-boot-starter-web 依赖就够了，它的子依赖包含了我们所需要的东西。

16.1. 校验注解

JSR 提供的校验注解:

●
@Null 被注释的元素必须为 null
●
@NotNull 被注释的元素必须不为 null
●
@AssertTrue 被注释的元素必须为 true
●
@AssertFalse 被注释的元素必须为 false
●
@Min(value) 被注释的元素必须是一个数字，其值必须大于等于指定的最小值
●
@Max(value) 被注释的元素必须是一个数字，其值必须小于等于指定的最大值
●
@DecimalMin(value) 被注释的元素必须是一个数字，其值必须大于等于指定的最小值
●
@DecimalMax(value) 被注释的元素必须是一个数字，其值必须小于等于指定的最大值
●
@Size(max=, min=) 被注释的元素的大小必须在指定的范围内
●
@Digits (integer, fraction) 被注释的元素必须是一个数字，其值必须在可接受的范围内
●
@Past 被注释的元素必须是一个过去的日期
●
@Future 被注释的元素必须是一个将来的日期
●
@Pattern(regex=,flag=) 被注释的元素必须符合指定的正则表达式

Hibernate Validator 提供的校验注解：

●
@NotBlank(message =) 验证字符串非 null，且长度必须大于 0
●
@Email 被注释的元素必须是电子邮箱地址
●
@Length(min=,max=) 被注释的字符串的大小必须在指定的范围内
●
@NotEmpty 被注释的字符串的必须非空
●
@Range(min=,max=,message=) 被注释的元素必须在合适的范围内

使用示例：

Java
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Person {

    @NotNull(message = "classId 不能为空")
    private String classId;

    @Size(max = 33)
    @NotNull(message = "name 不能为空")
    private String name;

    @Pattern(regexp = "((^Man$|^Woman$|^UGM$))", message = "sex 值不在可选范围")
    @NotNull(message = "sex 不能为空")
    private String sex;

    @Email(message = "email 格式不正确")
    @NotNull(message = "email 不能为空")
    private String email;

}

16.2. 验证请求体(RequestBody)

我们在需要验证的参数上加上了@Valid 注解，如果验证失败，它将抛出 MethodArgumentNotValidException。默认情况下，Spring 会将此异常转换为 HTTP Status 400（错误请求）。

Java
1
2
3
4
5
6
7
8
9
@RestController
@RequestMapping("/api")
public class PersonController {

    @PostMapping("/person")
    public ResponseEntity<Person> getPerson(@RequestBody @Valid Person person) {
        return ResponseEntity.ok().body(person);
    }

}

16.3. 验证请求参数(Path Variables 和 Request Parameters)

一定一定不要忘记在类上加上 Validated 注解了，这个参数可以告诉 Spring 去校验方法参数。

Java
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
@RestController
@RequestMapping("/api")
@Validated
public class PersonController {

    @GetMapping("/person/{id}")
    public ResponseEntity<Integer> getPersonByID(@Valid @PathVariable("id") @Max(value = 5,message = "超过 id 的范围了") Integer id) {
        return ResponseEntity.ok().body(id);
    }

    @PutMapping("/person")
    public ResponseEntity<String> getPersonByName(@Valid @RequestParam("name") @Size(max = 6,message = "超过 name 的范围了") String name) {
        return ResponseEntity.ok().body(name);
    }

}

更多内容请参考我的原创： 如何在 Spring/Spring Boot 中做参数校验？你需要了解的都在这里！

17. 如何使用 Spring Boot 实现全局异常处理？

可以使用 @ControllerAdvice 和 @ExceptionHandler 处理全局异常。

更多内容请参考我的原创 ：Spring Boot 异常处理在实际项目中的应用

18. Spring Boot 中如何实现定时任务 ?

我们使用 @Scheduled 注解就能很方便地创建一个定时任务。

Java
1
2
3
4
5
6
7
8
9
10
11
12
13
14
@Component
public class ScheduledTasks {
private static final Logger log = LoggerFactory.getLogger(ScheduledTasks.class);
private static final SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss");

    /**
     * fixedRate：固定速率执行。每5秒执行一次。
     */
    @Scheduled(fixedRate = 5000)
    public void reportCurrentTimeWithFixedRate() {
        log.info("Current Thread : {}", Thread.currentThread().getName());
        log.info("Fixed Rate Task : The time is now {}", dateFormat.format(new Date()));
    }

}

单纯依靠 @Scheduled 注解 还不行，我们还需要在 SpringBoot 中我们只需要在启动类上加上@EnableScheduling 注解，这样才可以启动定时任务。@EnableScheduling 注解的作用是发现注解 @Scheduled 的任务并在后台执行该任务。
