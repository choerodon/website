+++
title = "一键部署Choerodon"
description = "一键部署Choerodon"
weight = 7
+++

# 一键部署Choerodon

## 前置条件

- 成功安装kubernetes集群
- 成功安装helm
- 可以使用kubectl
- 每个节点安装nfs-utils
- 安装nfs-provisioner或自有nfs服务
- 正确解析域名到集群中

## 下载安装工具

您的主机已经配置了kubeconfig信息，并且可以正常执行`kubectl`命令，那么您可以在您的主机上执行安装，在安装之前您需要下载安装工具:

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
- [设置Gitlab启用SSH协议(必须开启)](../../parts/base/gitlab/#启用ssh协议)
- [设置Harbor证书(必须设置)](../../parts/base/harbor/#证书配置)
