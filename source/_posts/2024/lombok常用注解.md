---
title: lombok常用注解
top: false
cover: false
toc: true
mathjax: true
date: 2024-12-09 09:11:46
password:
summary:
tags:
categories:
---

## lombok 常用注解

对于 lombok 而言, 百分之 90 就是这 4 个 : @Data, @NoArgsConstructor, @AllArgsConstructor, @Builder

- @RequiredArgsConstructor 替代@Autowired 构造器注入

  对于依赖注入而言构造函数注入是相比于字段注入更推荐的方式，使用 lombok 可以选择使用 Lombok 的 @AllArgsConstructor 或 @RequiredArgsConstructor 注解，替代 @Autowired 注解；

  其中@RequiredArgsConstructor 注解会自动将 final 字段或者带有 @NotNull 注解的字段生成构造函数，而不影响其他的字段

  @AllArgsConstructor 会将类中的所有字段都生成对应的构造函数无论是可变字段还是临时数据，有可能会出现误注入的问题

- @Data
  自动生成 Getter 和 Setter：toString equals() 和 hashCode()，canEqual() 无参构造函数 全参构造器
- @NoArgsConstructor
  @NoArgsConstructor：Lombok 的 @NoArgsConstructor 注解确保即使定义了其他构造函数，也会生成一个无参构造函数，这在某些框架或库要求必须有无参构造函数时特别有用。
