+++
title = "方式二：分步部署Choerodon"
description = "方式二：分步部署Choerodon"
date = 2018-03-30T13:06:38+08:00
draft = false
weight = 20
+++

# 部署与配置

Choerodon采用Spring Cloud作为微服务框架，运行在Docker上，以Kubernetes作为容器的编排工具。理论上只要服务器资源允许，可以运行Kubernetes，就可以运行Choerodon。Choerodon不是一个单体应用系统，而是一个包含多个微服务的分布式系统，所以部署相对比较复杂。目前，我们提供基于Helm的部署方式，以提高部署效率。

---
<blockquote class="warning">
  <ul>
  <li>部署时请逐个确认环境变量</li>
  <li>部署时请确认设置的域名是否已映射到将要部署的集群中</li>
  </ul>
</blockquote>

## 前置要求与约定

- 硬件要求：
  
  - 节点数量：3
  - 单节点内存信息：16G及以上
  - 单节点处理器信息：4核4线程及以上
  - 单节点硬盘：40G及以上（如使用NFS存储，NFS服务节点建议存储不小于512G）
  <blockquote class="note">
  只要现有节点内存与CPU总和大于上述节点要求即可。
  </blockquote>

- 软件要求：
  - 系统版本：CentOS7.4及以上
  - Kubernetes：1.10及以上
  - Helm：2.10及以上(tiller版本请一定与helm版本一致)

- 约定：部署教程以NFS类型的PV为例进行创建，所有非集群级对象都创建在`c7n-system`命名空间下。

## 开始部署

1. [Chartmuseum部署](./base/chartmuseum)
1. [Minio部署](./base/minio)
1. [Redis部署](./base/redis)
1. [Mysql部署](./base/mysql)
1. [Harbor部署](./base/harbor)
1. [Gitlab部署](./base/gitlab)
1. [微服务开发框架部署](./choerodon)
1. [持续交付部署](./choerodon-devops)
1. [敏捷管理部署](./choerodon-agile)
1. [整合前端部署](./choerodon-front)