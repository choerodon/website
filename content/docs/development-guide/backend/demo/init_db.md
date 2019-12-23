+++
title = "初始化数据库"
date = "2018-04-26T15:38:28+08:00"
draft = false
weight = 3
+++

# 前置条件

在开发之前，请确保本地项目已经创建成功，详见 [新建项目](../create_project/)

## 介绍

项目创建成功之后，需要初始化本地数据库。

1. 创建用户
2. 创建数据库
3. 编写表结构对应的groovy脚本
4. 初始化表结构
5. 验证表结构
6. 项目数据库配置

## 创建用户

确保数据库启动成功，并创建项目访问的用户。
``` sql
CREATE USER 'choerodon'@'%' IDENTIFIED BY "123456";
```

## 创建数据库

用户创建成功之后，创建项目对应的数据库，并将新创建的数据库权限赋予用户。
```sql
CREATE DATABASE todo_service DEFAULT CHARACTER SET utf8mb4;
GRANT ALL PRIVILEGES ON todo_service.* TO choerodon@'%';
FLUSH PRIVILEGES;
```

## 编写表结构对应的groovy脚本

Choerodon 采用`Liquibase` + `groovy` 的方式对数据库管理。

更多有关Liguibase的资料见 [Liquibase 官网](http://www.liquibase.org/)。

创建`groovy`文件存储的文件夹，并且创建`groovy`文件。

``` bash
$ mkdir -p src/main/resources/script/db
$ cd src/main/resources/script/db
$ touch todo_user.groovy todo_task.groovy
```

```groovy
// todo_user.groovy
package script.db

databaseChangeLog(logicalFilePath: 'todo_user.groovy') {
    changeSet(id: '2019-05-30-todo_user', author: 'your.email@email.com') {
        createTable(tableName: "TODO_USER") {
            column(name: 'ID', type: 'BIGINT UNSIGNED', remarks: 'ID', autoIncrement: true) {
                constraints(primaryKey: true)
            }
            column(name: 'EMPLOYEE_NAME', type: 'VARCHAR(32)', remarks: '员工名')
            column(name: 'EMPLOYEE_NUMBER', type: 'VARCHAR(32)', remarks: '员工号') {
                constraints(unique: true)
            }
            column(name: 'EMAIL', type: 'VARCHAR(32)', remarks: '邮箱')

            column(name: "OBJECT_VERSION_NUMBER", type: "BIGINT", defaultValue: "1")
            column(name: "CREATED_BY", type: "BIGINT", defaultValue: "-1")
            column(name: "CREATION_DATE", type: "DATETIME", defaultValueComputed: "CURRENT_TIMESTAMP")
            column(name: "LAST_UPDATED_BY", type: "BIGINT", defaultValue: "-1")
            column(name: "LAST_UPDATE_DATE", type: "DATETIME", defaultValueComputed: "CURRENT_TIMESTAMP")
        }
    }
}
```

```groovy
// todo_task.groovy
package script.db

databaseChangeLog(logicalFilePath: 'todo_task.groovy') {
    changeSet(id: '2019-05-30-todo_task', author: 'your.email@email.com') {
        createTable(tableName: "TODO_TASK") {
            column(name: 'ID', type: 'BIGINT UNSIGNED', remarks: 'ID', autoIncrement: true) {
                constraints(primaryKey: true)
            }
            column(name: 'EMPLOYEE_ID', type: 'BIGINT', remarks: '员工ID')
            column(name: 'STATE', type: 'VARCHAR(36)', remarks: '状态')
            column(name: 'TASK_NUMBER', type: 'VARCHAR(64)', remarks: '任务编号') {
                constraints(unique: true)
            }
            column(name: 'TASK_DESCRIPTION', type: 'VARCHAR(256)', remarks: '任务描述')

            column(name: "OBJECT_VERSION_NUMBER", type: "BIGINT", defaultValue: "1")
            column(name: "CREATED_BY", type: "BIGINT", defaultValue: "-1")
            column(name: "CREATION_DATE", type: "DATETIME", defaultValueComputed: "CURRENT_TIMESTAMP")
            column(name: "LAST_UPDATED_BY", type: "BIGINT", defaultValue: "-1")
            column(name: "LAST_UPDATE_DATE", type: "DATETIME", defaultValueComputed: "CURRENT_TIMESTAMP")
        }
    }
}

```

## 初始化表结构

在根目录下，创建`init-local-database.sh` 文件。

``` bash
$ touch init-local-database.sh
```

修改初始化脚本。

```bash
#!/usr/bin/env bash

MAVEN_LOCAL_REPO=$(cd / && mvn help:evaluate -Dexpression=settings.localRepository -q -DforceStdout)
TOOL_GROUP_ID=io.choerodon
TOOL_ARTIFACT_ID=choerodon-tool-liquibase
TOOL_VERSION=${1:-0.11.0.RELEASE}
TOOL_JAR_PATH=${MAVEN_LOCAL_REPO}/${TOOL_GROUP_ID/\./\/}/${TOOL_ARTIFACT_ID}/${TOOL_VERSION}/${TOOL_ARTIFACT_ID}-${TOOL_VERSION}.jar
mvn org.apache.maven.plugins:maven-dependency-plugin:get \
 -Dartifact=${TOOL_GROUP_ID}:${TOOL_ARTIFACT_ID}:${TOOL_VERSION} \
 -Dtransitive=false

#java -Dspring.datasource.url="jdbc:oracle:thin:@127.0.0.1:1521:xe" \
java -Dspring.datasource.url="jdbc:mysql://localhost/todo_service?useUnicode=true&characterEncoding=utf-8&useSSL=false&useInformationSchema=true&remarks=true&allowMultiQueries=true&serverTimezone=Asia/Shanghai" \
 -Dspring.datasource.username=choerodon \
 -Dspring.datasource.password=123456 \
 -Ddata.drop=false -Ddata.init=true \
 -Ddata.dir=src/main/resources \
 -jar ${TOOL_JAR_PATH}
```

进入根目录执行如下命令：

```bash
$ sh init-local-database.sh [version]    #如未指定version，则默认使用tool version为0.11.0.
```

控制台打印出如下信息，则表示初始化成功。
```bash
数据库初始化任务完成
```
脚本执行程序会自动扫描`resources` 中的`groovy` 数据库初始化文件以及`excel` 初始化数据。

执行 `init-local-database.sh` 脚本，若出现错误：
```bash
Error: Unable to access jarfile
```
则需自行检查脚本中`TOOL_JAR_PATH`路径是否正确。

您也可以手动下载最新版本的 [choerodon-tool-liquibase.jar](https://oss.sonatype.org/content/groups/public/io/choerodon/choerodon-tool-liquibase)，自行指定`TOOL_JAR_PATH`路径进行数据初始化。 

## 验证表结构

登录数据库，查询现有的表结构。

```bash
mysql> use todo_service;
Database changed
mysql> show tables;
+------------------------+
| Tables_in_todo_service |
+------------------------+
| DATABASECHANGELOG      |
| DATABASECHANGELOGLOCK  |
| TODO_TASK              |
| TODO_USER              |
+------------------------+
4 rows in set (0.00 sec)
```

## 项目数据库配置

在`pom.xml` 文件中添加数据库依赖。
``` xml
<dependency>
    <groupId>io.choerodon</groupId>
    <artifactId>choerodon-starter-mybatis</artifactId>
    <version>${choerodon.starters.version}</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

在项目的`application.yml` 文件中添加数据库连接信息：
``` yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/todo_service?useUnicode=true&characterEncoding=utf-8&useSSL=false&useInformationSchema=true&remarks=true&allowMultiQueries=true&serverTimezone=Asia/Shanghai
    username: choerodon
    password: 123456
```

项目根目录下执行命令。项目正常启动，则数据库连接配置正常。
``` bash
$ mvn clean spring-boot:run
```