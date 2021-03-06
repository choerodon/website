+++
title = "7.6.部署一个应用服务"
description = ""
weight = 6
+++

# 部署一个应用服务
## 目标
部署应用服务是指将Choerodon中应用服务的某一个版本部署至指定环境的操作。本文档面向初次使用 Choerodon 猪齿鱼的用户，旨在引导新手用户在 Choerodon 猪齿鱼系统中部署一个应用服务。

## 前置条件
1. 已部署安装Choerodon 猪齿鱼，并使用默认的 admin 用户名和密码登录了 Choerodon 系统。
2. 默认的 admin 用户进入 Choerodon 系统后，默认拥有一个组织并为该组织的组织管理员角色。关于Choerodon角色的详情，请移步角色管理。

    |账号|角色|职责|
    |---|---|---|
    |admin|组织管理员|管理组织下的项目和成员（组织成员、项目所有者、项目成员）|
3. 已完成项目创建及团队成员建设。  
4. 应用服务经过开发流程中的CI阶段，已生成应用服务版本。
5. 集群和环境均创建成功，且都为“运行中”状态。
6. 应用服务所需要的数据库，kafka，注册中心，配置中心等都已经正常运行启动。


## 操作示例

1. 路径：部署->应用部署->部署，点击工具栏的`手动部署`按钮，右侧弹出手动部署界面；  
  ![image](/docs/quick-start/devops/image/deploy-1.png)  

2. 选择服务来源、选择应用服务`猪齿鱼Todo服务`、选择一个服务版本、选择环境；
  
    > 部署配置为可选项，若此处不选择，则会给出Charts包中默认的配置信息。  

3. 根据实际的配置，配置部署应用服务所需的配置信息。替换掉一些参数文件值。在这里，请先`preJob.preConfig.enable`和`preJob.preInitDb.enable`这个值改为`false`。

4. 最后，点击`部署`。信息提交完成后，会自动跳转到实例信息界面，可以在相同菜单下的环境下看到正在部署中的实例。    
  ![image](/docs/quick-start/devops/image/back-applications-8.png)  

5. 点击实例的实例详情，可以查看到阶段信息及日志。    
  ![image](/docs/quick-start/devops/image/back-applications-9.png)    
  
    **如何判断某版本的应用服务已经部署成功并通过健康检查？**

    >当实例出现在列表中，且实例名称后没有报错提示icon即为部署成功生成实例；

    >当新的实例状态为running，且容器状态都是绿色时，表示新部署的实例通过健康检查。

    ![image](/docs/quick-start/devops/image/back-applications-10.png)  

6. 完成上述操作后，若您需要通过外部网络对应用服务部署后产生的实例进行访问，还需为此实例创建`网络`和`域名`。

  （1）创建网络  
   a. 路径：部署->应用部署->资源->资源视图，选择部署应用服务所在的环境，并点击进入环境下的`网络`层级。  

  ![image](/docs/quick-start/devops/image/deploy-2.png)

   b. 点击顶部工具栏的`创建网络`按钮，右侧弹出创建网络的操作界面；   
        
  ![image](/docs/quick-start/devops/image/deploy-3.png)   

   c. 在网络的创建页面填入以下信息，例如：
     
    - 目标对象：选择实例  
    - 应用服务名称：猪齿鱼Todo服务  
    - 选择实例：choerodon-todo-service-1(此处选择为部署应用服务后产生的实例)  
    - 网络类型：ClusterIp   
    - 端口: 80（此处为网络的端口号，可填入0-65535内任意数字）  
    - 目标端口: 18080（此处的端口需填入对应实例的端口）   
    - 网络名称：choerodon-todo-servie-s1s1（网络名称使用默认生成的即可）   

   d. 最后，点击创建按钮，会回到网络列表界面，此时便能通过此网络的端口在集群内部访问到目标实例；若想通过外部网络访问到目标实例，还需再已有网络的基础上，创建一个`域名`。


  （2）创建域名  

   a. 路径：部署->应用部署->资源->资源视图，选择部署应用服务所在的环境，并点击进入环境下的`域名`层级。  

  ![image](/docs/quick-start/devops/image/deploy-4.png)

   b. 点击顶部工具栏的`创建域名`按钮，右侧弹出创建域名的操作界面； 
        
  ![image](/docs/quick-start/devops/image/deploy-5.png)   

   c. 在域名的创建页面填入以下信息，例如：

    - 域名名称：c7n-todo-ingress  
    - 网络协议：普通协议  
    - 域名地址：c7n-todo.demo.example.com（请填写所属环境关联集群主机绑定的域名及其子域名地址）  
    - 路径：默认为根目录/，遵循Ant path的规范  
    - 网络: choerodon-todo-servie-s1s1（选择与实例关联的已创建的网络）  
    - 端口: 80（此处端口为选择的网络的端口）        

`网络`和`域名`创建成功后，便可通过外部的域名地址访问到部署的应用服务了。

## 下一步  
[集群配置](../../../quick-start/devops/cluster-config)