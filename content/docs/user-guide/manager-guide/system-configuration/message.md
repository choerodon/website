+++
title = "消息服务"
weight = 11
description = "对系统与用户之间必要交互的方式、内容进行管理，主要有创建消息模板、发送设置 "
+++

# 消息服务

## 1. 概况
消息途径分为邮件、短信、站内信、webhook4大类别。它包含了激发系统与用户交互的触发点类型，不同触发点在触发之后给用户反馈的消息模板，发送邮箱和短信的服务器配置信息。

消息服务用来选择某一个触发点对应选择的邮件模板、短信模板、站内信模板以及发送模式。用户可以根据需要，通过此功能，可以在忘记密码、添加/导入新用户、系统添加新功能、修改密码等过程中向不同成员发送邮件提醒。同时可以设置不同的邮件通知模式。

![消息](/docs/user-guide/manager-guide/image/message.jpg)

## 2. 消息服务列表

列表字段：

- **消息类型**：由接收方的某些行为触发邮件发送的触发点类型。比如忘记密码、添加/导入新用户、项目停用、修改密码等。
- 说明：对触发类型的触发情景、适用场景的描述。
- 层级：发送设置生效的层级，包括`平台层`,`组织层`,`项目层`三个层级。
- 状态：消息类型的状态，分为`启用`,`停用`两种。启用状态下的消息类型会根据发送消息。
- 是否允许配置接收：设置是否允许用户自定义是否接收此类型的消息。

## 3. 修改发送设置

1. 进入平台管理，点击菜单“设置>通知>消息服务”，进入消息服务页面。
2. 选择你要修改发送设置的消息类型，点击操作栏的修改发送设置按钮，进入修改发送设置侧开页。

    发送设置：
    - 选择发送方式：您可以选择邮件、站内信、webhook、短信等4种发送方式。
    - 是否允许配置接收：设置是否允许用户自定义是否接收此类型的消息。
    - 重发次数：自动重发的次数，默认重发次数为3次。
    - 是否即时发送：选择触发该消息类型是否立刻发送。
    - 是否允许手动重发：邮件发送失败后，是否允许用户在消息日志面点击`重发`。
    - 是否为待办提醒：消息是否为待办。
3. 点击保存，保存修改。

## 3. 修改邮件模板

1. 点击点击操作栏的修改邮件模板按钮，打开修改邮件模板页面。

    必填字段：
    - 邮件主题：发送时邮件的主题。
    - 邮件内容：邮件的内容。
    填写模板所需的文字和参数。模板中的参数变量通过`${}`进行替换，在发送时，将`${}`替换成对应的实际变量。
```
    例如：
    模板:`${username}`您好。
    发送参数：`username=张三`。
    发送信息：张三您好。  
```
    更多的变量信息，请查看[变量说明](#var)
2. 点击保存，完成修改。


## 4. 修改站内信模板

1. 点击点击操作栏的修改站内信模板按钮，打开修改站内信模板页面。

    必填字段：
    - 站内信主题：发送时站内信的标题。
    - 站内信内容：站内信的内容。
    填写模板所需的文字和参数。模板中的参数变量通过`${}`进行替换，在发送时，将`${}`替换成对应的实际变量。

    ```
    例如：
    模板:`${username}`您好。
    发送参数：`username=张三`。
    发送信息：张三您好。  
    ```

    -  更多的变量信息，请查看[变量说明](#var)

2. 点击保存，完成修改模板。


## 5. 修改短信模板

1. 点击点击操作栏的修改短信模板按钮，打开修改短信模板页面。

    必填字段：
    - 短信内容：短信的内容。
2. 点击保存，完成修改模板。

## 6. 修改webhook模板

1. 点击点击操作栏的修改webhook模板按钮，打开修改webhook模板页面。

    必填字段：
    - webhook主题：发送时webhook的标题。
    - webhook容：webhook的内容。（仅支持text或者markdown文本格式）
    填写模板所需的文字和参数。模板中的参数变量通过`${}`进行替换，在发送时，将`${}`替换成对应的实际变量。

    ```
    例如：
    模板:`${username}`您好。
    发送参数：`username=张三`。
    发送信息：张三您好。  
    ```

    -  更多的变量信息，请查看[变量说明](#var)

2. 点击保存，完成修改模板。


## 6. 变量声明

<div id="var"></div>

以下是邮件、短信、站内信模板中可以使用的变量参数。

| 变量名 | 说明 | 试用范围 | 示例 |
| --- | --- | --- | --- |
| ${loginName} | 登录名 | 所有触发类型 | 模板：您的登录名为`${loginName}`。<br>发送参数：`loginName=张三`<br>发送信息：您的登录名为张三。 |
| ${userName} | 用户名 | 所有触发类型 | 模板：`${userName}`,您好。<br>发送参数：`userName=张三`<br>发送信息：张三，您好。 |
| ${defaultPassword} | 初始密码 | 组织重置用户密码 | 模板：您的初始密码为`${defaultPassword}`。<br>发送参数：`defaultPassword=abc123`<br>发送信息：您的初始密码为abc123。 |
| ${verifyCode} | 验证码 | 忘记密码 |  模板：您的验证码为`${verifyCode}`。<br>发送参数：`verifyCode=123456`<br>发送信息：您的验证码为123456。 |
| ${addCount} | 添加的用户数量 | 添加用户 | 模板：您已成功添加`${addCount}`名用户。<br>发送参数：`addCount=1`<br>发送信息：你已成功添加1名用户。 |
| ${assigneeName} | issue的操作者（创建者、被分配者） | 问题创建、问题分配、问题已创建 | 模板：issue`${summary}`已由`${assigneeName}`解决。<br>发送参数：`summary=No-1 编写变量说明文档`，`${assigneeName}=张三`<br>发送信息：No-1 编写变量说明文档已由张三解决。 |
| ${summary} | 敏捷管理的issue编码名称 | 问题创建、问题分配、问题已解决 | 模板：`${summary}`已解决。<br>发送参数：`summary=No-1 编写变量说明文档`<br>发送信息：No-1 编写变量说明文档已解决。 |
| ${organizationName} | 组织名称 | 组织启用、组织停用 | 模板：`${organizationName}`组织已启用。<br>发送参数：`organizationName=Choerodon`<br>发送信息：Choerodon组织已启用。 |
| ${projectName} | 项目名称 | 项目启用、项目停用| 模板：`${projectName}`项目已启用。<br>发送参数：`organizationName=Choerodon`<br>发送信息：Choerodon项目已启用。 |
| ${title} | 您在系统公告页填写公告时编写的系统公告标题 | 系统公告 | 模板：`${title}`<br>发送参数：`${title}=版本更新`<br>发送信息：版本更新（发送后标题为版本更新） |
| ${content} | 您在系统公告页编写的公告内容 | 系统公告 | 模板：`${content}` <br>发送参数：`${content}= 请在此输入系统公告内容`<br>发送信息：请在此输入系统公告内容（发送后系统公告为此内容） |
| ${jobName} | 任务名称 | 平台任务状态通知、组织任务状态通知、项目任务状态通知 | 模板：`${jobName}` 任务已结束。<br>发送参数：`${jobName}=定时执行任务`<br>发送信息：定时执行任务已结束。 |
| ${jobStatus} | 任务执行状态（可渲染的状态有启用、停用、结束） | 平台任务状态通知、组织任务状态通知、项目任务状态通知 | 模板：定时执行任务已`${jobStatus}`。 <br>发送参数：`${jobStatus}= 启用` <br>发送信息：定时执行任务已启用。|
| ${sagaInstanceId} | 事务实例的ID | 事务实例失败通知 |模板：编号为`${sagaInstanceId}`的事务实例运行失败。<br>发送参数：`${sagaInstanceId}= 1000` <br>发送信息：编号为1000的事务实例运行失败。 |
| ${sagaCode} | 事务定义的编码 | 事务实例失败通知 | 模板：编号为1000的`${sagaCode}`事务实例运行失败。<br>发送参数：`${sagaCode}=iam-disable-project` <br>发送信息：编号为1000的iam-disable-project事务实例运行失败。 |
| ${level} |触发事务实例的层级（平台、组织、项目） | 事务实例失败通知 |模板：编号为1000的`${level}`层事务实例运行失败。<br>发送参数：`${level}=平台` <br>发送信息：编号为1000的平台层事务实例运行失败。 |
| ${pipelineName} | 流水线名称 | 流水线失败、流水线成功、流水线任务待审核、流水线被终止通知、流水线或签任务通过通知 | 模板：流水线`${pipelineName}`执行成功。<br>发送参数：`${pipelineName}=test` <br>发送信息：流水线test执行成功。 |
| ${pipelineId} | 流水线Id | 流水线失败、流水线成功、流水线任务待审核、流水线被终止通知、流水线或签任务通过通知 | 模板：ID为`${pipelineId}`的流水线执行成功。<br>发送参数：`${pipelineId}=1` <br>发送信息：ID为1的流水线执行成功。 |
| ${pipelineName}，${pipelineId}，${pipelineRecordId}，${projectId}，${projectName}，${organizationId} | 流水线名称，流水线Id，流水线记录Id，项目Id，项目名称，组织Id | 流水线失败、流水线成功 | 模板：流水线“${pipelineName}”执行失败。查看详情（查看详情的链接为：`#/devops/pipeline-record/detail/${pipelineId}/${pipelineRecordId}?type=project&id=${projectId}&name=${projectName}&category=undefined&organizationId=${organizationId}`）<br>发送参数：`pipelineName=通知测试002`，`pipelineId=197`，`pipelineRecordId=672`，`projectId=490`，`projectName=testpro0110`，`organizationId=803`<br>发送信息：流水线“通知测试002”执行失败。 查看详情|
| ${pipelineName}，${stageName}，${pipelineId}，${pipelineRecordId}，${projectId}，${projectName}，${organizationId} | 流水线名称，阶段名称，流水线Id，流水线记录Id，项目Id，项目名称，组织Id | 流水线任务待审核 | 模板：流水线“${pipelineName}”目前暂停于【${stageName}】阶段，需要您进行审核。查看详情（查看详情的链接为：`#/devops/pipeline-record/detail/${pipelineId}/${pipelineRecordId}?type=project&id=${projectId}&name=${projectName}&category=undefined&organizationId=${organizationId}`）<br>发送参数：`pipelineName=通知测试002`，`stageName=阶段一`，`pipelineId=200`，`pipelineRecordId=677`，`projectId=875`，`projectName=project0517`，`organizationId=1027`<br>发送信息：流水线“通知测试001”目前暂停于【阶段一】需要您进行审核。查看详情 |
| ${pipelineName}，${stageName}，${auditName}，${realName}，${pipelineId}，${pipelineRecordId}，${projectId}，${projectName}，${organizationId} | 流水线名称，阶段名称，终止人员工号，终止人员名称，流水线Id，流水线记录Id，项目Id，项目名称，组织Id | 流水线被终止 | 模板：流水线“${pipelineName}”在【${stageName}】阶段被${auditName}:${realName}终止。查看详情（查看详情的链接为：`#/devops/pipeline-record/detail/${pipelineId}/${pipelineRecordId}?type=project&id=${projectId}&name=${projectName}&category=undefined&organizationId=${organizationId}`） |
| ${pipelineName}，${stageName}，${auditName}，${realName}，${pipelineId}，${pipelineRecordId}，${projectId}，${projectName}，${organizationId} | 流水线名称，阶段名称，或签审核人员工号，或签审核人员名称，流水线Id，流水线记录Id，项目Id，项目名称，组织Id | 流水线或签任务通过通知 | 模板：流水线“${pipelineName}”在【${stageName}】阶段中的或签任务已被${auditName}:${realName}审核。查看详情（查看详情的链接为：`#/devops/pipeline-record/detail/${pipelineId}/${pipelineRecordId}?type=project&id=${projectId}&name=${projectName}&category=undefined&organizationId=${organizationId}`）<br>发送参数：`pipelineName=通知测试002`，`stageName=阶段二`，`auditName=m1`，`realName=m1`，`pipelineId=197`，`pipelineRecordId=672`，`projectId=490`，`projectName=testpro0110`，`organizationId=803`<br>发送信息：流水线“通知测试002”在【阶段二】被m1:m1终止。查看详情" |
| ${user}，${env}，${object}，${objectName}，${captcha} | 操作者名称，环境名称，删除对象类型，删除对象名称，验证码 | 删除资源对象（网络、域名、实例、证书、配置映射、密文）| 模板：您好，${user}正在${env}环境下执行删除${object}""${objectName}"的操作，验证码为：${captcha}；确认后，需将此验证码提供给操作者${user}完成删除操作。验证码10分钟内有效。<br>参数：`user=m1`，`env=testshell`，`object=证书`，`objectName=cert-0604-7`，`captcha=215694`<br>发送信息：您好，m1正在testshell环境下执行删除证书""cert-0604-7""的操作，验证码为：215694；确认后，需将此验证码提供给操作者m1完成删除操作。验证码10分钟内有效。 |


模板中的参数变量通过`${}`进行替换，在发送时，将`${}`替换成对应的实际变量。

例如：

    邮件标题：验证邮件
    邮件内容：
    ${userName}，您好！您正在进行邮箱验证，您本次请求的验证码为：${verifyCode}。
    此验证码用于重置密码，XXX内有效。


## 7. 更多操作

- [使用Choerodon进行通知配置](../message-config)
- [查看消息发送日志](../email-log)
- [如何发送系统公告](../system-notice)