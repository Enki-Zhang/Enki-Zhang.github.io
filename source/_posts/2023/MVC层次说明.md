---
title: MVC层次说明
top: false
cover: false
toc: true
mathjax: true
date: 2023-02-16 21:02:08
password:
summary:
tags:
categories:
---

# 常见的三层架构和 MVC

## 三层架构

1. view 视图层
2. service 业务层
3. Dao 持久层
   > View 层：用于接收用户提交请求的代码在这里编写。
   > Service 层：系统的业务逻辑主要在这里编写。
   > Dao 层：直接操作数据库的代码在这里编写。

dao 层只是访问数据库，业务逻辑跟它没关系，它只会访问数据库读取数据，不知道这些数据是什么意思、做什么用的。
service 层，就是操作业务逻辑，它调取 dao 层的数据，对数据按照业务逻辑进行解释。

dao 层不能有业务逻辑，service 层可以调取一个 dao 层或者多个 dao 来实现业务逻辑。如果 dao 层加了业务逻辑进去，不通过 service 层实现业务逻辑，不仅拖累查询效率，还让 dao 层代码难以复用，导致代码臃肿。

## MVC

1. Model
2. View
3. Controller
   > View：视图，为用户提供使用界面，与用户直接进行交互。
   > Model：模型，承载数据，并对用户提交请求进行计算的模块。其分为两类，一类称为数据承载 Bean，一类称为业务处理 Bean。所谓数据承载 Bean 是指实体类，专门承载业务数据的，如 Student、User 等。而业务处理 Bean 则是指 Service 或 Dao 对象，专门用于处理用户提交请求的。
   > Controller：控制器，用于将用户请求转发给相应的 Model 进行处理，并处理 Model 的计算结果向用户提供相应响应。

## MVVC 和三层架构关系

MVC 中的 V 和 C 均属于三层架构的 View 层。
MVC 中的 M（Model）包括了数据承载 Bean 和业务处理 Bean，其中业务处理 Bean 分为 Service 或 Dao 对象，分别对应业务逻辑处理和数据库操作，它们对应的是三层架构中的 Service 层和 Dao 层。

## SSM 和三层架构的关系

> SpringMVC：作为 View 层的实现者，完成用户的请求接收功能。SpringMVC 的 Controller 作为整个应用的控制器，完成用户请求的转发及对用户的响应。
> MyBatis：作为 Dao 层的实现者，完成对数据库的增、删、改、查功能。
> Spring：以整个应用大管家的身份出现。整个应用中所有的 Bean 的生命周期行为，均由 Spring 来管理。即整个应用中所有对象的创建、初始化、销毁，及对象间关联关系的维护，均由 Spring 进行管理。

## spring springMVC 配置文件
