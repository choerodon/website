+++
title = "7.6.部署一个应用服务"
description = ""
weight = 6
+++

# 部署一个应用服务
## 目标
部署应用服务是指将Choerodon中应用服务的某一个版本部署至指定环境的操作。本文档面向初次使用 Choerodon 猪齿鱼的用户，引导新手用户在 Choerodon 猪齿鱼系统中部署一个应用服务。

## 前置条件
1. 已部署安装Choerodon 猪齿鱼，并使用默认的 admin 用户名和密码登录了 Choerodon 系统。
2. 默认的 admin 用户进入 Choerodon 系统后，默认拥有一个组织并为该组织的组织管理员角色。关于Choerodon角色的详情，请移步角色管理。

    |账号|角色|职责|
    |---|---|---|
    |admin|组织管理员|管理组织下的项目和成员（组织成员、项目所有者、项目成员）|
3. 已完成项目创建及团队成员建设。
4. 集群和环境均配置完毕。

## 操作示例

1. 创建部署配置；路径：部署->环境配置。点击工具栏 `创建部署配置`，右侧会弹出创建部署配置的页面，填写部署配置名称、描述、选择应用服务、修改配置信息；

    ![image](/docs/user-guide/deploy/image/environment-06.png)

2. 路径：部署->应用部署，点击工具栏的`手动部署`，右侧弹出手动部署界面；  


3. 选择服务来源、选择服务、选择需要部署的服务版本、选择环境、选择部署配置；

4. 点击`部署`即可。

    ![image](/docs/user-guide/deploy/app-deploy/images/deployment-operation-01.png)

## 下一步

- [知识库快速入门](../../../quick-start/knowledge/)
- [敏捷快速入门](../../../quick-start/agile/)
- [测试管理快速入门](../../../quick-start/test/)


