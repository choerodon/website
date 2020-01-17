﻿+++
title = "认证体系"
description = "介绍 Choerodon 的认证体系， Choerodon 采用 Spring Cloud Security 和 Spring Cloud OAuth2 作为认证"
weight = 2
+++

# Choerodon 认证体系

身份验证的目的是增强微服务和保证微服务之间的通信安全。

主要负责如下功能：

- 提供给网关一个统一的认证入口。
- 提供微服务通信的统一的用户 session 保证。
- 提供 token 管理系统，用来生成 token 、存储 token 以及撤销 token 。

## 架构

下图介绍了Choerodon 认证体系的架构。

![架构](/img/docs/security/Choerodon_architecture.png)

## 组成

### 认证服务器

认证服务器，即专门用来处理认证的服务器。

### 资源服务器

资源服务器，即存放用户生成的资源的服务器。它与认证服务器，可以是同一台服务器，也可以是不同的服务器。

在 Choerodon 中，资源服务器主要指系统中的一个个微服务。

## 工作流

认证流程包含三个阶段， 授权阶段，鉴权阶段和使用阶段。

### 授权阶段

1. 资源拥有者根据用户名和密码，调用授权服务器。
2. 授权服务器校验用户名和密码，生成对应的 token 返回给客户端，并将 token 存储下来，用于之后的鉴权。
3. 客户端将 token 存储在本地，用于之后每次请求的鉴权。

### 鉴权阶段

1. 客户端根据本地的 token 请求资源。
2. 网关将根据 token 去授权服务器鉴权。
3. 授权服务器将网关传来的 token 和存储的 token 进行对比，来确定本次操作是否是合法的。

### 使用阶段

1. 鉴权通过，网关将 token 转化成包含用户信息的 JWT token，并将 JWT token 传给资源服务器。
2. 资源服务器根据 JWT token 解析成用户信息，执行资源操作。