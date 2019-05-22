+++
title = "持续交付部署"
description = "持续交付部署"
weight = 20
+++

# 持续交付部署

<blockquote class="warning">
在此之前，应该准备好Mysql、Harbor、Gitlab、Minio，Chartmuseum这些组件的信息。按以下搭建顺序进行搭建，请不要随意调整搭建顺序。
</blockquote>

## 添加choerodon chart仓库

```
helm repo add c7n https://openchart.choerodon.com.cn/choerodon/c7n/
helm repo update
```

## 创建数据库

```
helm install c7n/mysql-client \
    --set env.MYSQL_HOST=c7n-mysql.c7n-system.svc \
    --set env.MYSQL_PORT=3306 \
    --set env.MYSQL_USER=root \
    --set env.MYSQL_PASS=password \
    --set env.SQL_SCRIPT="\
          CREATE USER IF NOT EXISTS 'choerodon'@'%' IDENTIFIED BY 'password';\
          CREATE DATABASE IF NOT EXISTS devops_service DEFAULT CHARACTER SET utf8;\
          CREATE DATABASE IF NOT EXISTS gitlab_service DEFAULT CHARACTER SET utf8;\
          GRANT ALL PRIVILEGES ON devops_service.* TO choerodon@'%';\
          GRANT ALL PRIVILEGES ON gitlab_service.* TO choerodon@'%';\
          FLUSH PRIVILEGES;" \
    --version 0.1.0 \
    --name create-c7ncd-db \
    --namespace c7n-system
```

## 部署devops service

<blockquote class="warning">
choerodon devops service需要与Chartmuseum共用存储，所以choerodon devops service的PVC与Chartmuseum的PVC必须一致。
</blockquote>

- 部署服务

    ``` 
    helm install c7n/devops-service \
        --set env.open.JAVA_OPTS="-XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap" \
        --set preJob.preConfig.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/manager_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
        --set preJob.preConfig.datasource.username=choerodon \
        --set preJob.preConfig.datasource.password=password \
        --set preJob.preInitDB.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/devops_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
        --set preJob.preInitDB.datasource.username=choerodon \
        --set preJob.preInitDB.datasource.password=password \
        --set env.open.SPRING_DATASOURCE_URL="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/devops_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
        --set env.open.SPRING_DATASOURCE_USERNAME=choerodon \
        --set env.open.SPRING_DATASOURCE_PASSWORD=password \
        --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
        --set env.open.SPRING_REDIS_HOST=c7n-redis.c7n-system.svc \
        --set env.open.SPRING_REDIS_DATABASE=11 \
        --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
        --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
        --set env.open.SERVICES_HARBOR_BASEURL="https://registry.example.choerodon.io" \
        --set env.open.SERVICES_HARBOR_USERNAME=admin \
        --set env.open.SERVICES_HARBOR_PASSWORD="Harbor12345" \
        --set env.open.SERVICES_HELM_URL="http://chart.example.com" \
        --set env.open.SERVICES_GITLAB_URL="http://gitlab.example.choerodon.io" \
        --set env.open.SERVICES_GITLAB_SSHURL="gitlab.example.choerodon.io:2289" \
        --set env.open.SERVICES_GITLAB_PASSWORD=password \
        --set env.open.SERVICES_GITLAB_PROJECTLIMIT=100 \
        --set env.open.SERVICES_GATEWAY_URL=http://api.example.choerodon.io \
        --set env.open.SECURITY_IGNORED="/ci\,/webhook\,/v2/api-docs\,/agent/**\,/ws/**\,/webhook/**" \
        --set env.open.AGENT_VERSION="0.15.1" \
        --set env.open.AGENT_REPOURL="https://openchart.choerodon.com.cn/choerodon/c7n/" \
        --set env.open.AGENT_SERVICEURL="ws://devops.example.choerodon.io/agent/" \
        --set env.open.TEMPLATE_VERSION="0.15.0" \
        --set env.open.TEMPLATE_URL="https://github.com/choerodon/choerodon-devops-templates.git" \
        --set env.open.AGENT_CERTMANAGERURL="https://openchart.choerodon.com.cn/choerodon/infra/" \
        --set ingress.enable=true \
        --set ingress.host=devops.example.choerodon.io \
        --set service.enable=true \
        --set persistence.enabled=true \
        --set persistence.existingClaim="chartmuseum-pvc" \
        --name devops-service \
        --version 0.15.2 \
        --namespace c7n-system 
    ```
    参数名 | 含义 
    --- |  --- 
    service.enable|是否创建service
    preJob.preConfig.datasource{}|初始化配置所需manager_service数据库信息
    preJob.preInitDB.datasource{}|初始化数据库所需数据库信息
    env.open.SPRING_DATASOURCE_URL|数据库链接地址
    env.open.SPRING_DATASOURCE_USERNAME|数据库用户名
    env.open.SPRING_DATASOURCE_PASSWORD|数据库密码
    env.open.SPRING_CLOUD_CONFIG_ENABLED|启用配置中心
    env.open.SPRING_CLOUD_CONFIG_URI|配置中心地址
    env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE|注册服务地址
    env.open.SERVICES_HARBOR_BASEURL|harbor地址
    env.open.SERVICES_HARBOR_USERNAME|harbor用户名
    env.open.SERVICES_HARBOR_PASSWORD|harbor密码
    env.open.SERVICES_HELM_URL|chartmuseum地址
    env.open.SERVICES_SONARQUBE_URL|sonarqube地址，若未部署请忽略
    env.open.SERVICES_GITLAB_URL|gitlab地址
    env.open.SERVICES_GITLAB_PASSWORD|通过choerodon平台创建的gitlab用户初始密码
    env.open.SERVICES_GITLAB_PROJECTLIMIT|通过choerodon平台创建的gitlab可创建项目上限
    env.open.AGENT_VERSION|与当前Devops Service相匹配的Agent版本，不需要修改
    env.open.AGENT_REPOURL|Agent Chart包远程仓库，不需要修改
    env.open.AGENT_SERVICEURL|Agent与Devops Service进行链接的webSocket地址，主机域名与ingress.host相同
    env.open.TEMPLATE_VERSION| 预定义微服务后端模板的版本
    env.open.TEMPLATE_URL| 微服务后端模板的地址
    persistence.enabled|启用持久化存储
    persistence.existingClaim|一定与chartmuseum挂载出来的目录相同
    service.enable|启用service
    ingress.enable|启用域名
    ingress.host|设置域名，这里不要加http前缀

- 验证部署
    - 验证命令

        ```
        curl -s $(kubectl get po -n c7n-system -l choerodon.io/release=devops-service -o jsonpath="{.items[0].status.podIP}"):8061/health | jq -r .status
        ```
    - 出现以下类似信息即为成功部署
        ```
        UP
        ```

## 部署gitlab service
- 部署服务

    -  如何获取GITLAB_PRIVATETOKEN请点击[此处](http://forum.choerodon.io/t/topic/1155/2)

    ```
    helm install c7n/gitlab-service \
        --set env.open.JAVA_OPTS="-XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap" \
        --set preJob.preConfig.mysql.host=c7n-mysql.c7n-system.svc \
        --set preJob.preConfig.mysql.port=3306 \
        --set preJob.preConfig.mysql.database=manager_service \
        --set preJob.preConfig.mysql.username=choerodon \
        --set preJob.preConfig.mysql.password=password \
        --set preJob.preInitDB.mysql.host=c7n-mysql.c7n-system.svc \
        --set preJob.preInitDB.mysql.port=3306 \
        --set preJob.preInitDB.mysql.database=gitlab_service \
        --set preJob.preInitDB.mysql.username=choerodon \
        --set preJob.preInitDB.mysql.password=password \
        --set env.open.SPRING_DATASOURCE_URL="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/gitlab_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
        --set env.open.SPRING_DATASOURCE_USERNAME=choerodon \
        --set env.open.SPRING_DATASOURCE_PASSWORD=password \
        --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
        --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
        --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
        --set env.open.GITLAB_URL="http://gitlab.example.choerodon.io" \
        --set env.open.GITLAB_PRIVATETOKEN="GEuRhgb6kG9y3prFosSb" \
        --name gitlab-service \
        --version 0.15.0 \
        --namespace c7n-system
    ```
    参数名 | 含义 
    --- |  --- 
    preJob.preConfig.mysql{}|初始化配置所需manager_service数据库信息
    preJob.preInitDB.mysql{}|初始化数据库所需数据库信息
    env.open.SPRING_DATASOURCE_URL|数据库链接地址
    env.open.SPRING_DATASOURCE_USERNAME|数据库用户名
    env.open.SPRING_DATASOURCE_PASSWORD|数据库密码
    env.open.SPRING_CLOUD_CONFIG_ENABLED|启用配置中心
    env.open.SPRING_CLOUD_CONFIG_URI|配置中心地址
    env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE|注册服务地址
    env.open.GITLAB_URL|gitlab地址
    env.open.GITLAB_PRIVATETOKEN|gitlab 具有api、read_use、sudo权限的用户token，如何获取请[查阅](http://forum.choerodon.io/t/topic/1155/2)

- 验证部署
    - 验证命令

        ```
        curl -s $(kubectl get po -n c7n-system -l choerodon.io/release=gitlab-service -o jsonpath="{.items[0].status.podIP}"):8071/health | jq -r .status
        ```
    - 出现以下类似信息即为成功部署
        ```
        UP
        ```