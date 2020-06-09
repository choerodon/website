+++
title ="SonarQube部署"
description ="SonarQube部署"
weight = 60
+++

# SonarQube部署

<blockquote class="note">
SonarQube并非猪齿鱼运行必要基础组件，你可以选择性进行安装。
</blockquote>

## 预备知识

如果你不知道SonarQube是做什么的，那么请参考下面链接（包括但不限于）进行学习：

- [SonarQube](https://docs.sonarqube.org/7.6/)

## 仓库设置

## 添加choerodon chart仓库并同步

```
helm repo add c7n https://openchart.choerodon.com.cn/choerodon/c7n/
helm repo update
```

## 部署SonarQube

<blockquote class="warning">
注意：本事例中 PostgreSql 数据库搭建仅为快速体验 SonarQube 而编写，由于使用了NFS存储故并不能保证其稳定运行或数据不丢失，您可以参照 PostgreSql 官网进行搭建。
</blockquote>

<blockquote class="warning">
注意：当前choerodon版本为0.19.x及以上版本时，SonarQube插件版本为sonar-auth-choerodonoauth-plugin-1.5.2-RELEASE.jar；
当前choerodon版本为0.18.x及以下版本时，SonarQube插件版本为sonar-auth-choerodonoauth-plugin-1.4-RELEASE.jar；
</blockquote>

```
helm upgrade --install sonarqube c7n/sonarqube \
    --set persistence.enabled=true \
    --set persistence.storageClass=nfs-provisioner \
    --set postgresql.persistence.storageClass=nfs-provisioner \
    --set ingress.enabled=true \
    --set ingress.'hosts[0]'=sonarqube.example.choerodon.io \
    --set plugins.'install[0]'=https://file.choerodon.com.cn/choerodon-install/sonarqube/sonar-auth-choerodonoauth-plugin-1.5.2-RELEASE.jar \
    --version 0.15.0-3 \
    --namespace c7n-system
```

- 更多参数及含义请参考[SonarQube Chart](https://github.com/helm/charts/tree/155659de436be352b0e8fd12d4954d82c62c7068/stable/sonarqube#sonarqube)

## 安装SoanrQube插件
- 此步骤用于之前已经安装过SonarQube，只需安装插件的情况（如已经执行过上一步可跳过此步骤）
- 进入SonarQube安装目录，下载https://file.choerodon.com.cn/choerodon-install/sonarqube/sonar-auth-choerodonoauth-plugin-1.5.2-RELEASE.jar 插件到\data\sonarqube\extensions\plugins目录
- 重启SoanrQube服务

## 验证部署

- 访问设置的SonarQube域名出现以下界面即部署成功

    ![](/docs/installation-configuration/image/sonarqube.png)

## 配置 Choerodon 认证

<blockquote class="warning">
  <ul>
  <li>以下操作须将Choerodon搭建完成后再继续进行，若未搭建，请跳过。</li>
  </ul>
</blockquote>

<blockquote class="warning">
0.19以前的base-service的数据库为iam_service,0.19以后更名为base_service,对于配置文件中是使用iam_service还是base_service遵从一下标准：
如果是新安装的版本，就使用base_service，如果是升级上来的版本，原版本数据库使用的是什么数据库名称，配置文件中就配置对应的数据库名称
</blockquote>

### 添加Choerodon Client
- 记得修改`http://sonarqube.example.choerodon.io`为实际的SonarQube地址
  
    ```
    helm upgrade --install sonarqube-client c7n/mysql-client \
        --set env.MYSQL_HOST=c7n-mysql.c7n-system.svc \
        --set env.MYSQL_PORT=3306 \
        --set env.MYSQL_USER=root \
        --set env.MYSQL_PASS=password \
        --set env.SQL_SCRIPT="\
            INSERT INTO base_service.oauth_client ( \
            name\,organization_id\,resource_ids\,secret\,scope\,\
            authorized_grant_types\,web_server_redirect_uri\,\
            access_token_validity\,refresh_token_validity\,\
            additional_information\,auto_approve\,object_version_number\,\
            created_by\,creation_date\,last_updated_by\,last_update_date)\
            VALUES('sonar'\,1\,'default'\,'sonarsonar'\,'default'\,\
            'password\,implicit\,client_credentials\,authorization_code\,refresh_token'\,\
            'http://sonarqube.example.choerodon.io/oauth2/callback/choerodon'\,3600\,3600\,'{}'\,'default'\,1\,0\,NOW()\,0\,NOW());" \
        --version 0.1.0 \
        --namespace c7n-system
    ```

### 配置用户权限

<blockquote class="note">
默认管理员用户名：admin，密码：admin
</blockquote>

- 使用管理员用户登录 SoanrQube
- 配置默认新建项目为`Private`, 进入 `Administration` -> `Projects` -> `Management`
    ![](/docs/installation-configuration/image/sonarqube_1.png)

### 配置认证插件
- 使用管理员用户登录 SoanrQube
- 进入 `Administration` -> `Configuration` ->`choerodon`
- 更改 `Enabled` 为启用
- 更改 `Choerodon url` 为当前使用的 `choerodon api getaway` 地址；默认地址为：`http://api.example.choerodon.io`
    ![](/docs/installation-configuration/image/sonarqube_4.png)

- 更改 `sonar url` 为当前使用的SonarQube实际地址
- 退出登录，测试使用choerodon登录,出现如下界面
    ![](/docs/installation-configuration/image/sonarqube_5.png)

## Choerodon应用关联SonarQube项目

- 部署devops-service时添加SonarQube环境变量

    ```
    SERVICES_SONARQUBE_URL: http://sonarqube.example.choerodon.io
    SERVICES_SONARQUBE_USERNAME: admin
    SERVICES_SONARQUBE_PASSWORD: admin
    ```

- 在.gitlab-ci.yml文件build阶段添加

    ```
        - >-
          mvn --batch-mode  verify sonar:sonar
          -Dsonar.host.url=$SONAR_URL -Dsonar.login=$SONAR_LOGIN
          -Dsonar.gitlab.project_id=$CI_PROJECT_PATH
          -Dsonar.gitlab.commit_sha=$CI_COMMIT_SHA
          -Dsonar.gitlab.ref_name=$CI_COMMIT_REF_NAME
          -Dsonar.analysis.serviceGroup=$GROUP_NAME
          -Dsonar.analysis.commitId=$CI_COMMIT_SHA
          -Dsonar.projectKey=${GROUP_NAME}:${PROJECT_NAME}
    ```

- sonar.projectKey=${GROUP_NAME}:${PROJECT_NAME}不可更改；否则，在查看代码质量时将获取不到对应数据
- GROUP_NAME和PROJECT_NAME是devops-service内置的环境变量， GROUP_NAME=当前项目所在组织编码-当前项目编码，PROJECT_NAME=当前应用编码
- 如果手动创建SonarQube项目，项目命名规则为：当前项目所在组织编码-当前项目编码:当前应用编码
