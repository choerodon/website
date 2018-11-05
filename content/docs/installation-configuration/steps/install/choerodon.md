+++
title = "方式一：一键部署Choerodon"
description = "方式一：一键部署Choerodon"
weight = 18
+++



# 一键部署Choerodon

## 前置条件

- 成功安装kubernetes集群
- 成功安装helm
- 每个节点安装nfs-utils
- 安装nfs-provisioner或自有nfs服务
- 正确解析域名到集群中

## 下载安装工具

如果您的主机没有配置kubernetes连接信息，则您需要到k8s服器中的master执行安装，如果您的主机已经配置了kubernetes的连接信息，并且可以正常执行`kubectl`命令，那么您可以在您的主机上执行安装，在安装之前您需要下载安装工具:

```bash
curl -L https://file.choerodon.com.cn/choerodon-install/c7n/0.1.0/c7n-0.1.0-`uname -s`-amd64.tar.gz | tar -xz && cd c7n-0.1.0
```

## 编辑配置文件

```bash
vim config.yml
```

粘贴以下内容，并将域名修改为你自己的域名

```yml
version: 0.10
metadata:
  name: install-choerodon 
  namespace: c7n-system  # 指定命名空间安装choerodon
spec:
  persistence:
#   nfs:                         
#      server: 98dbe4b804-yxc75.cn-zhangjiakou.nas.aliyuncs.com
#      rootPath: /u01
#   如果您有自己的nfs服务器并且支持v4版本，可以注释storageClassName并配置您的的nfs,某些NFS服务器可能存在程序无法获取到正确权限的问题
    storageClassName: nfs-provisioner
  resources:
    mysql:
      external: false
    gitlab:
      domain: gitlab.example.com
      external: false
      username: root     # gitlab 默认用户名为root，不能修改
      schema: http
    minio:
      domain: minio.example.com
      schema: http
    harbor:
      domain: harbor.example.com
      schema: https
      username: admin    # harbor 默认用户名为admin，不能修改
    chartmuseum:
      domain: chart.example.com
      schema: http
    api-gateway:
      domain: api.example.com
      schema: http
    notify-service:
      domain: notify.example.com
      schema: ws
    devops-service:
      domain: devops.example.com
      schema: ws
    choerodon-front:
      domain: c7n.example.com
      schema: http
      username: admin   # 前端 默认用户名为admin，暂不能修改
      password: admin   # 前端 默认密码为admin，暂不能修改
    xwiki:
      domain: wiki.example.com
```

## 开始部署

- 在安装过程中，会提示设置某些组件用户名及密码，注意保存
- 如果安装失败，再次执行命令即可
- 执行部署命令

```bash
./c7n install -c config.yml --no-timeout
```

- 参数解释

| 参数 | 作用 | 说明
| --- | --- |  ---
| --no-timeout | 取消默认任务等待超时| 微服务安装时需要执行初始化任务，默认超时时长为300秒，添加此参数将超时时长设置为24小时
| --debug | 输出调试信息 | 

## 后续步骤

- 安装完成后您可以访问您配置的`choerodon-front`域名，默认用户名和密码都为admin
- [设置Gitlab启用SSH协议(必须开启)](../parts/base/gitlab/#启用ssh协议)
- [设置Harbor证书(必须设置)](../parts/base/harbor/#证书配置)

## 启用runner

Gitlab Runner，用于代码提交后自动进行代码测试、构建服务的镜像及生成helm chart并将结果发回给Choerodon。它与GitLab CI一起使用，Gitlab CI是Gitlab中包含的开源持续集成服务，用于协调作业。安装程序可以快速启用一个runner:

```bash
./c7n config gitlab runner -c config.yml
```

如果您想获取更多关于runner的配置信息请参考[此处](../../gitlab-runner/)。

## 常见问题

- 停留在等待slaver启动过程中

  请确认每个节点都安装了nfs-utils