+++
title ="Gitlab部署"
description ="Gitlab部署"
weight = 55
+++

# Gitlab部署

## 预备知识

如果你不知道Gitlab是做什么的，那么请参考下面链接（包括但不限于）进行学习：

- [Gitlab](https://about.gitlab.com/what-is-gitlab/)

## 仓库设置

## 添加choerodon chart仓库并同步

```
helm repo add c7n https://openchart.choerodon.com.cn/choerodon/c7n/
helm repo update
```

## 部署Gitlab

### 部署数据库

<blockquote class="warning">
注意：本 PostgreSql 数据库搭建教程仅为快速体验Gitlab而编写，由于使用了NFS存储故并不能保证其稳定运行或数据不丢失，您可以参照 PostgreSql 官网进行搭建。
</blockquote>

```shell
helm install c7n/postgresql \
    --set persistence.enabled=true \
    --set persistence.storageClass=nfs-provisioner \
    --set image.tag=9.6.11 \
    --set postgresqlPassword=password \
    --set postgresqlDatabase=gitlabhq_production \
    --set initdbScripts.'init\.sql'='
        \\c gitlabhq_production;
        CREATE EXTENSION IF NOT EXISTS pg_trgm;' \
    --version 3.18.4-1 \
    --name gitlab-postgresql \
    --namespace c7n-system
```

### 部署gitlab所需Redis

```shell
helm install c7n/persistentvolumeclaim \
    --set accessModes={ReadWriteOnce} \
    --set requests.storage=256Mi \
    --set storageClassName=nfs-provisioner \
    --version 0.1.0 \
    --name gitlab-redis-pvc \
    --namespace c7n-system
```

```shell
helm install c7n/redis \
    --set persistence.enabled=true \
    --set persistence.existingClaim=gitlab-redis-pvc \
    --set service.enabled=true \
    --version 0.2.2 \
    --name gitlab-redis \
    --namespace c7n-system
```

### 创建gitlab所需PV和PVC

```shell
helm install c7n/persistentvolumeclaim \
    --set accessModes={ReadWriteOnce} \
    --set requests.storage=2Gi \
    --set storageClassName=nfs-provisioner \
    --version 0.1.0 \
    --name gitlab-pvc \
    --namespace c7n-system
```

### 部署gitlab

```shell
helm install c7n/gitlab \
    --set persistence.enabled=true \
    --set persistence.existingClaim=gitlab-pvc \
    --set env.config.GITLAB_EXTERNAL_URL=http://gitlab.example.choerodon.io \
    --set env.config.GITLAB_TIMEZONE=Asia/Shanghai \
    --set env.config.CHOERODON_OMNIAUTH_ENABLED=false \
    --set env.config.GITLAB_DEFAULT_CAN_CREATE_GROUP=true \
    --set env.config.DB_ADAPTER=postgresql \
    --set env.config.DB_HOST=gitlab-postgresql-postgresql.c7n-system.svc \
    --set env.config.DB_PORT=5432 \
    --set env.config.DB_USERNAME=postgres \
    --set env.config.DB_PASSWORD=password \
    --set env.config.DB_DATABASE=gitlabhq_production \
    --set env.config.REDIS_HOST=gitlab-redis.c7n-system.svc \
    --set env.config.SMTP_ENABLE=false \
    --set env.config.SMTP_ADDRESS=smtp.mxhichina.com \
    --set env.config.SMTP_PORT=465 \
    --set env.config.SMTP_USER_NAME=git.sys@example.choerodon.io \
    --set env.config.SMTP_PASSWORD=password \
    --set env.config.SMTP_DOMAIN=smtp.mxhichina.com \
    --set env.config.SMTP_AUTHENTICATION=login \
    --set env.config.GITLAB_EMAIL_FROM=git.sys@example.choerodon.io \
    --set env.config.SMTP_ENABLE_STARTTLS_AUTO=true \
    --set env.config.SMTP_TLS=true \
    --set env.config.PROMETHEUS_ENABLE=false \
    --set env.config.NODE_EXPORTER_ENABLE=false \
    --set env.config.UNICORN_WORKERS=3 \
    --set env.config.UNICORN_TIMEOUT=60 \
    --set service.enabled=true \
    --set service.ssh.nodePort=32222 \
    --set ingress.enabled=true \
    --version 0.5.3 \
    --name gitlab \
    --namespace c7n-system
```

- 参数

    参数 | 含义 
    --- |  --- 
    persistence.enabled|是否启用持久化存储
    persistence.existingClaim|PVC的名称
    env.config.GITLAB_EXTERNAL_URL|gitlab的域名
    env.config.GITLAB_TIMEZONE|时区
    env.config.GITLAB_DEFAULT_CAN_CREATE_GROUP|用户是否可以创建组 
    env.config.CHOERODON_OMNIAUTH_ENABLED|是否开启第三方认证 
    env.config.OMNIAUTH_BLOCK_AUTO_CREATED_USERS|是否自动创建用户 
    env.config.CHOERODON_API_URL|choerodon的Api地址
    env.config.CHOERODON_CLIENT_ID|在choerodon上申请的client id
    env.config.DB_ADAPTER|数据库类型 
    env.config.DB_HOST|数据库地址 
    env.config.DB_PORT|数据库端口号 
    env.config.DB_USERNAME|数据库用户名 
    env.config.DB_PASSWORD|数据库用户密码 
    env.config.REDIS_HOST|redis地址 
    env.config.REDIS_PORT|redis端口号
    env.config.SMTP_ENABLE|是否开启smtp，若为false，以下SMTP参数都不生效
    env.config.SMTP_ADDRESS|smtp地址
    env.config.SMTP_PORT|smtp端口号 
    env.config.SMTP_USER_NAME|stmp用户
    env.config.SMTP_PASSWORD|stmp用户密码 
    env.config.SMTP_DOMAIN|smtp地址
    env.config.SMTP_AUTHENTICATION|认证方式 
    env.config.GITLAB_EMAIL_FROM|设置发件人email
    env.config.SMTP_ENABLE_STARTTLS_AUTO|是否自动启用TLS 
    env.config.SMTP_TLS|是否启用TLS 
    env.config.PROMETHEUS_ENABLE|是否开启prometheus
    env.config.NODE_EXPORTER_ENABLE|是否开启node_exporter_enable
    env.config.UNICORN_WORKERS|unicorn workers的数量
    env.config.UNICORN_TIMEOUT|设置unicorn workers进程的超时时间，单位秒
    service.enabled|是否开启service
    service.ssh.nodePort|ssh 服务 node port
    ingress.enabled|是否开启ingress 

### 验证部署

- 访问设置的Gitlab域名出现以下界面即部署成功

    ![](/docs/installation-configuration/image/gitlab.png)

## 配置Choerodon Oauth认证

<blockquote class="warning">
  <ul>
  <li>以下操作须将Choerodon搭建完成后再继续进行，若未搭建，请跳过。</li>
  <li>配置Choerodon的Oauth认证后Gitlab的root用户是无法再通过Gitlab自有的界面进行登录的了。</li>
  </ul>
</blockquote>

<blockquote class="note">
如标题所描述，进行此步配置就是使用Choerodon的Oauth认证进行登陆Gitlab。这是Gitlab与Choerodon进行整合的必要操作。
</blockquote>

### 更新Gitlab配置

    helm upgrade gitlab c7n/gitlab \
        -f <(helm get values gitlab) \
        --set env.config.CHOERODON_OMNIAUTH_ENABLED=true \
        --set env.config.OMNIAUTH_AUTO_SIGN_IN_WITH_PROVIDER=oauth2_generic \
        --set env.config.OMNIAUTH_BLOCK_AUTO_CREATED_USERS=false \
        --set env.config.CHOERODON_API_URL=http://api.example.choerodon.io \
        --set env.config.CHOERODON_CLIENT_ID=gitlab \
        --set env.config.CHOERODON_CLIENT_SECRET=secret \
        --version 0.5.3 \
        --namespace c7n-system

### 添加Gitlab Client

<blockquote class="warning">
0.19以前的base-service的数据库为iam_service,0.19以后更名为base_service,对于配置文件中是使用iam_service还是base_service遵从一下标准：
如果是新安装的版本，就使用base_service，如果是升级上来的版本，原版本数据库使用的是什么数据库名称，配置文件中就配置对应的数据库名称
</blockquote>

- 在执行里面前请根据实际情况修改参数
- 记得修改`http://gitlab.example.choerodon.io`的地址为实际的gitlab地址

    ```
    helm install c7n/mysql-client \
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
            VALUES('gitlab'\,1\,'default'\,'secret'\,'default'\,\
            'password\,implicit\,client_credentials\,authorization_code\,refresh_token'\,\
            'http://gitlab.example.choerodon.io'\,3600\,3600\,'{}'\,'default'\,1\,0\,NOW()\,0\,NOW());" \
        --version 0.1.0 \
        --name gitlab-client \
        --namespace c7n-system
    ```

### 添加管理员用户关联

{{< warning >}}执行完添加管理员用户关联步骤前请不要去Gitlab界面进行登录操作{{< /warning >}}

- 执行下面语句进行关联:

    ```
    helm install c7n/postgresql-client \
        --set env.PG_HOST=gitlab-postgresql-postgresql.c7n-system.svc \
        --set env.PG_PORT=5432 \
        --set env.PG_USER=postgres \
        --set env.PG_PASS=password \
        --set env.PG_DBNAME=gitlabhq_production \
        --set env.SQL_SCRIPT="\
            INSERT INTO identities(extern_uid\, provider\, user_id\, created_at\, updated_at) \
            VALUES ('1'\, 'oauth2_generic'\, 1\, NOW()\, NOW());" \
        --version 0.1.0 \
        --name gitlab-user-identities \
        --namespace c7n-system
    ```

### 验证更新

- 访问设置的Gitlab域名出现以下界面即更新成功

    ![](/docs/installation-configuration/image/gitlab-oauth.png)