---
title: Spring
top: false
cover: false
toc: true
mathjax: true
date: 2023-09-08 15:10:48
password:
summary:
tags:
categories:
---

### SpringBoot 的优点

简化 Spring 配置
有强大的生态 JDBC Spring Securtiy
内置 Tomcat 服务器
拥有多种插件 lombok maven

## IOC

IOC 控制反转；在 spring 中用于管理对象之间的关系实现对象之间的松耦合和可维护性。IOC 在 Spring 中的主要任务：

1. 实例化对象
2. 注入依赖
3. bean 对象生命周期的管理

主要表现为
beanFactory ：提供 Spring 最基本的接口和 IOC 功能。延迟加载对象，只有在实际需要时才创建对象
ApplicationContext：对 BeanFactory 的拓展。在启动时加载所有 bean

### 常用注解

@Component：通用的注解，可标注任意类为 Spring 组件
@Repository : 对应持久层注解
@Service : 对应服务层，
@Controller : 对应 Spring MVC 控制层

> @SpringBootApplication：@Configuration(允许在上下文中注册额外的 bean 或导入其他配置类)
> +@EnableAutoConfiguration(启用 SpringBoot 的自动配置机制)+@ComponentScan(扫描被 Component 注解的 bean)
> 用于标识一个 Spring Boot 应用程序的启动类。

### Bean 的生命周期

一般分为实例化、属性赋值、初始化、销毁这 4 个大阶段
实例化： 创建 bean 的实例
属性配置：将任何配置的依赖通过 setter 方法、构造函数等方式注入到 bean 中,为 bean 设置相关的属性和依赖
初始化 ：

### 设计模式

工厂模式:通过 BeanFactory（延迟加载对象）或则 ApplicationContext（预加载所有 bean ）创建对象
单例模式：日志对象。线程池，不需要频繁的创建对象，节约资源提高响应速率。Spring 中的 Bean 默认是单例的
代理模式:在 AOP 切面编程中应用多，SpringAOP 一般是利用动态代理来实现，当要代理的对象实现了某个接口，AOP 使用 JDK Proxy 创建代理对象。当没有实现的接口对象就需要使用 Cglib 对被代理对象生成一个子类作为代理

### application.yml 和 bootstrap.yml 执行顺序，以及区别

bootstrap.yml 在配置加载时优先级高，通常用于配置引导和初始化阶段的配置。
application.yml 用于配置应用程序的核心设置，通常在应用程序正常运行时加载。

### .yml、yaml、properties 执行顺序，以及区别

yml > yaml > properties 三个配置文件配置的信息都有效果并且若配置了相同的属性，会按照加载顺序进行加载后加载的文件会覆盖掉之前加载的信息。
yml 和 Yaml 实际上是相同的是 天然的树形结构，相同的前缀只需要写一次。可读性更好
properties 是传统的配置文件格式 每一行是一个配置使用=描述属性
properties 邮

### springboot 如何切换测试、开发等模式，配置是什么样的

我们可以为不同的环境配置不同的 properties 环境，并在 apliaction.properties 中通过 spring.profiles.active 指定不同的环境配置文件
或者是再 yml 中配置不同环境并指定后
或者使用 VM options 指定参数式启动某个环境的配置文件

### Spring 中的线程安全问题

- Bean 是单例的被所有线程共享，Spring 中的单例 Bean 不是线程安全的。全局只有一个 Bean 被所有线程共享。对于无状态的 Bean 即不对其进行修改操作的是安全的。否则则是线程不安全的
  常见的解决办法：
  使用 ThreadLocal 对线程进行隔离，将需要的成员变量保存在 ThreadLocal 中，或者使用锁也可以解决

### Spring 中事务失效的场景（\*\*）

- 事务方法的权限：spring 要求被代理方法必须是 public 的。或者将事务方法定义为 final 也会导致事务失效。因为事务底层使用了 AOP 动态代理，final 修饰方法无法进行重写不能添加事务
- 方法的内部直接调用
- 未被 Spring 管理
- 事务不能抛出异常，导致事务无法回滚
