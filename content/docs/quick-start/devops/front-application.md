+++
title = "7.1.创建一个前端应用"
description = ""
weight = 1
+++

# 创建一个前端应用
## 目标
本章节将在猪齿鱼研发项目下，以猪齿鱼前端Demo应用为例，从创建前端应用、开发前端应用、部署应用、配置网络、配置域名等方面介绍，让读者能够熟悉使用Choerodon创建前端应用的步骤和流程，并且学会如何利用Choerodon创建并部署前端应用。

## 前置条件
1. 已部署安装Choerodon 猪齿鱼，并使用默认的 admin 用户名和密码登录了 Choerodon 系统。
2. 默认的 admin 用户进入 Choerodon 系统后，默认拥有一个组织并为该组织的组织管理员角色。关于Choerodon角色的详情，请移步角色管理。

    |账号|角色|职责|
    |---|---|---|
    |admin|组织管理员|管理组织下的项目和成员（组织成员、项目管理员、项目成员）|
3. 已完成项目创建及团队成员建设。

## 创建前端应用
具体的操作步骤如下：

**第一步：** 使用拥有项目所有者或项目成员（环境成员）角色的用户登录Choerodon系统，选择项目猪齿鱼研发。

**第二步：** 选择应用管理模块，点击应用，进入应用管理页面。

**第三步：** 点击创建应用，系统会从右边滑出页面，在页面中输入应用编码、应用名称，应用模板，应用模板选择MicroServiceFront。点击创建按钮即可创建一个前端应用。

例如，

* 应用编码：choerodon-front-demo
* 应用名称：猪齿鱼前端Demo应用
* 选择应用模板：MicroServiceFront

![image](/docs/quick-start/devops/image/front-application-1.png)

> 当应用模板不符合您的需求，您可手动[创建一个应用模板](http://v0-18.choerodon.io/zh/docs/quick-start/admin/application-template/)，在此之前您必须拥有组织管理员角色权限。

**第四步：** 当应用创建成功，可在应用管理 -> 应用 界面查看到新建的应用。

刚创建的应用状态为创建中，操作不可用，等待一会，状态变为启用，操作可用。

**第五步：** 在创建应用的同时，系统会对应在Gitlab中创建一个仓库。

选择 开发流水线 -> 代码仓库界面，找到创建好的应用，点击仓库地址，链接到Gitlab新建的仓库。

![image](/docs/quick-start/devops/image/front-application-2.png)

进入仓库地址，gitlab仓库里会生成相应模板的相关文件。

![image](/docs/quick-start/devops/image/front-application-3.png)

Choerodon 平台与 Gitlab 名词关系如下：

| Choerodon 名词   | 对应 Gitlab 名词   | 举例   | 
|:----|:----|:----|
| 项目名   | 组名   | 猪齿鱼研发   | 
| 应用编码   | 项目名   | choerodon-front-demo   | 
| 应用名称   | 无   | 猪齿鱼前端Demo应用   | 

## 开发前端应用
应用创建完成之后，开发前端应用。

具体的操作步骤如下：

**第一步：创建分支**

分支是将开发工作从主线上分离开来，以免影响主线。 更多相关信息参考[分支管理](http://v0-18.choerodon.io/zh/docs/user-guide/development-pipeline/branch/)。

在 开发流水线 -> 分支 界面，选择应用猪齿鱼前端Demo应用，点击创建分支。选择对应的issue 问题，分支来源选择master，选择相应分支类型，填写分支名称，如feature-choerodon-dev-1。点击创建按钮，即可创建一个分支。

![image](/docs/quick-start/devops/image/front-application-4.png)

> Choerodon提供master、feature、bugfix、release、hotfix、custom六种分支类型，可根据问题选择对应分支类型。

分支创建成功后，可在 开发流水线 -> 分支 界面查看创建的分支。

**第二步：拉取代码仓库**

在开发流水线 -> 代码仓库 找到choerodon-front-demo 的仓库地址。通过git 命令拉取生成的项目代码。然后切换到对应分支进行本地开发。

$ git clone `仓库地址`

$ cd ./choerodon-front-demo

$ git checkout feature-choerodon-dev-1

**第三步：本地开发**

将代码克隆到本地后，就可以在本地进行开发。

* Choerodon 提供的MicroServiceFront 应用模板本身生成的应用可以直接运行在平台上。更多具体的开发参考[前端开发手册](http://v0-18.choerodon.io/zh/docs/development-guide/front/)。

**第四步：修改ci 文件**

模板中包含.gitlab-ci.yml文件。有关.gitlab-ci.yml 的编写参考[应用模板](http://v0-18.choerodon.io/zh/docs/user-guide/application-management/application-template/)。

* .gitlab-ci.yml定义 Gitlab CI 的阶段，Choerodon 缺省的 CI 流程包含了编译，打包，生成镜像，生成helm 包几个阶段。


**第五步：修改charts 文件**

模板中包含charts 文件夹。有关charts 的编写参考[应用模板](http://v0-18.choerodon.io/zh/docs/user-guide/application-management/application-template/)。

* charts模块用于创建应用时生成创建 k8s 对象，包含了部署的模板，chart values。

**第六步：修改Dockerfile 文件**

Choerodon 使用docker 来运行应用。有关Dockerfile 的编写参考[应用模板](http://v0-18.choerodon.io/zh/docs/user-guide/application-management/application-template/)。

* 你可以通过修改Dockerfile 来修改应用的运行环境。

**第七步：提交代码**

当本地修改结束后，需要将本地仓库的代码提交到远程分支上。

$ git add .

$ git commit -m "[ADD] init choerodon-front-todo"

$ git push origin feature-choerodon-dev-1

提交的commit信息建议遵循Choerodon 的规范：

* [IMP] 提升改善正在开发或者已经实现的功能
* [FIX] 修正BUG
* [REF] 重构一个功能，对功能重写
* [ADD] 添加实现新功能
* [REM] 删除不需要的文件

**第八步：代码集成**

提交代码后，根据gitlab中.gitlab-ci.yml文件定义的阶段，生成一条 CI 流水线，可在开发流水线 -> 持续集成页面查看持续集成。

选择猪齿鱼前端Demo应用应用，查看持续集成，点击阶段跳转到Gitlab 查看 CI 进度。

![image](/docs/quick-start/devops/image/front-application-5.png)

![image](/docs/quick-start/devops/image/front-application-6.png)

**第九步：合并分支**

当 CI 执行通过以后，可以将feature-choerodon-dev-1分支合并到master分支上。合并后，分支可选择删除或保留继续开发。

在开发流水线 -> 合并请求 页面，选择应用猪齿鱼前端Demo应用。点击创建合并请求，跳转到Gitlab。

分别选择源分支为feature-choerodon-dev-1 ，目标分支为master，并提交合并请求。

![image](/docs/quick-start/devops/image/front-application-7.png)

等待ci流水线通过后，点击合并分支。

当master 分支的ci流水线 通过以后。在应用管理 -> 应用版本 可以找到猪齿鱼前端Demo应用生成的对应版本。

![image](/docs/quick-start/devops/image/front-application-8.png)

gitlab中docker_build阶段日志也可查看相应版本。

![image](/docs/quick-start/devops/image/front-application-9.png)

## 部署应用
提供可视化、一键式部署应用，支持并行部署和流水线无缝集成，实现部署环境标准化和部署过程自动化。更多相关信息以及详细操作步骤参考[部署一个应用](http://v0-18.choerodon.io/zh/docs/quick-start/project-member/application-deployment/)。

具体的操作步骤如下：

**第一步：** 使用项目所有者的角色登录Choerodon系统，选择项目猪齿鱼研发。

**第二步：** 进入部署流水线模块，选择应用部署 进入应用部署界面。

**第三步：** 选择应用猪齿鱼前端Demo应用，按照步骤条完成信息选择。选择新建实例。如果此应用在该环境中已有部署的实例，则可以选择替换实例，替换实例会更新该实例的镜像及配置信息，未修改配置信息或版本相同不可选择替换实例。

![image](/docs/quick-start/devops/image/front-application-10.png)

> 配置信息可修改，并提供实时校验yaml格式

**第四步：** 点击部署按钮后，页面自动跳转到实例页面。

>**如何判断某版本的应用已经部署成功并通过？**
>查看实例列表，实例状态为运行中且实例名称后无错误提示即为部署成功生成实例；
>当容器状态条为绿色，表示新部署的实例通过健康检查。
## 配置网络
前端应用部署成功以后，需要创建对应的网络，才能够被外部访问。更多相关信息以及详细操作步骤参考[配置网络和域名](http://v0-18.choerodon.io/zh/docs/quick-start/project-member/config-service-and-domain/)。

具体的操作步骤如下：

**第一步：** 使用项目所有者的角色登录Choerodon系统，选择项目猪齿鱼研发。

**第二步：** 进入部署流水线模块，选择网络。

**第三步：** 点击创建网络按钮，选择环境、应用、实例，输入如下信息，创建网络。

例如，

* 环境名称：猪齿鱼环境
* 应用名称：猪齿鱼前端Todo应用
* 实例：choerodon-front-demo-627fb
* 端口: 80
* 目标端口: 80
* 网络名称：choerodon-front-demo-16dc

![image](/docs/quick-start/devops/image/front-application-11.png)

**第四步：** 网络创建成功，可在部署流水线 -> 网络页面查看创建的网络。

## 配置域名
创建对应的网络后，同时需要给网络指定外网域名，这样外部可以直接通过域名访问到前端。更多相关信息以及详细操作步骤参考[配置网络和域名](http://v0-18.choerodon.io/zh/docs/quick-start/project-member/config-service-and-domain/)。

具体的操作步骤如下：

**第一步：** 使用项目所有者的角色登录Choerodon系统，选择项目猪齿鱼研发。

**第二步：** 进入部署流水线模块，选择域名。

**第三步：** 点击创建域名按钮 ，选择环境，输入域名名称、域名地址、路径、网络、端口，创建域名

例如，

* 环境名称：猪齿鱼环境
* 域名名称：choerodon-front-demo
* 域名地址：choerodon-front-demo.alpha.saas.hand-china.com（请填写所属环境集群主机绑定的域名及其子域名地址）
* 路径：默认为根目录/，遵循Ant path的规范
* 网络：选择上一步创建的网络
* 端口：80

![image](/docs/quick-start/devops/image/front-application-12.png)

**第四步：** 域名创建成功，可在部署流水线 -> 域名界面查看已创建的域名。

上述步骤完成后，就成功创建一个前端应用了，创建成功后，您就可以继续相关开发了。

## 相关文档



