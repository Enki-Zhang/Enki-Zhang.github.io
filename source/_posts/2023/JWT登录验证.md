---
title: JWT登录验证
top: false
cover: false
toc: true
mathjax: true
date: 2023-04-17 21:24:07
password:
summary:
tags:
categories:
---

# JWT 登录验证

## JWT（json web token ） 是什么

JWT 是一种在双方进行加密的 json 对象协议，加密方式使用数字签名，JWT 重点关注令牌签名。令牌签名可以验证声明的完整性，加密令牌可以对其他方隐藏声明。

## 使用场景

Authorization 特别适合单点登录场景 用户在其中一个网站上进行登录后，在访问另一个网站也能自动登录。
我们可以将 session 进行持久化，每次请求都想数据库请求。
当时持久层完蛋，也就完蛋了。
或者将数据保存在客户端中，每次请求都发回服务器（JWT）

## JWT 使用过程

服务器验证通过后，在客户端保存加密过后的 json 对象保存在用户客户端中，下次请求后直接发送 json 对象
服务器依靠该对象进行用户验证，

## 使用 分布式 Session 和 JWT 的区别

分布式 Session 将 session 存放在 redis 中
session 是服务器存储用户登录信息的方式，每个用户创建唯一标识 sessionID
用户请求并验证后，服务器存储用户信息到 session 中并将 sessionID 发送到 cookie 中，该用户每次发送请求后 cookie 中都会携带 sessionID 供服务器进行验证

### 分布式 session 实现逻辑

在用户登录验证通过后将生成 token 存放在 redis 中，并将 token 返回到前端，前端利用拦截器将 token 放入到请求头中，以后每次请求都会携带 token,服务器会对 token 进行验证查询 redis 中的对应的 token 信息是否一致。也可以将 token 写入到前端的 cookie 中。
写入到请求头和 cookie 中的区别：

- 安全性：将 Token 写入请求头可以提高安全性，因为 Cookie 存在一定的安全风险。比如，如果网站存在 XSS 攻击漏洞，攻击者可以窃取用户的 Cookie，并冒充用户登录网站。而将 Token 写入请求头，则不会受到这种攻击方式的影响。
- 大小限制：Cookie 的大小通常有限制（通常是 4KB），而请求头的大小一般没有明确限制。因此，如果需要传递较大的 Token，写入请求头可能更为合适。
