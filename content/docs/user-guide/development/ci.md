+++
title = "CI 流水线"
description = "CI流水线通过使用 GitLab CI 工具，实现了CI流程执行的自动化与CI任务编排的可视化。"
weight = 3
+++

# CI流水线

## 1. 概述

CI流水线通过使用 GitLab CI 工具，实现了CI流程执行的自动化与CI任务编排的可视化。用户可在此页面预设CI流水线中的各个阶段与任务，使整个流程更加灵活高效。目前CI流水线中支持配置的任务类型包括：构建、代码检查发布Chart以及自定义任务。

## 2. CI流水线管理

### 2.1 创建CI流水线
路径：开发->流水线。

1. 点击页面顶部操作栏的`创建流水线`，进入创建流水线页面。

    ![image](/docs/user-guide/development/image/ci-pipeline-01.png)
 
2. 填写`CI流水线名称`，该名称在项目下唯一；
 
3. 选择`关联应用服务`；且此处的应用服务应满足以下条件：
 
    - 应用服务状态应为“启用”；
    - 应用服务须有master分支；
    - 应用服务尚未关联CI流水线；
    - 当前用户拥有权限的应用服务；
       
     <blockquote class="note"> 
        此处默认最多展示出20个满足以上条件的应用服务，但支持通过模糊搜索来选择项目下其他满足条件的应用服务。
     </blockquote>

4. 高级设置；可直接使用其中的默认设置。此处给出了默认的`流水线Runner镜像`；同时支持用户在此自定义该条CI流水线的Runner镜像；   

    <blockquote class="note"> 
    此处定义的流水线Runner镜像会作为整条流水线中所有构建类型任务中的默认Runner镜像，在构建任务中同样支持自定义任务Runner镜像。
 </blockquote>

5. 添加阶段；点击阶段之间的添加按钮，即可成功添加一个阶段；点击某个阶段后的修改按钮，会弹出修改界面，支持修改该阶段的名称；而点击阶段后的删除按钮，确认后，便能删除此阶段；  


6. 添加任务；点击对应阶段下的`添加任务`，会从右侧弹出任务添加框，首先需要选择任务类型，目前支持`构建`、`代码检查`、`发布Chart`以及`自定义`类型的任务；  

    ![image](/docs/user-guide/development/image/ci-pipeline-02.png)

    - 构建类型任务：通过预置各种常用语言的构建模板，为用户提供高效、易配置的构建能力，从而提高构建效率；
         - 选择添加此类型任务后，首先需要填写任务名称、选择或填写触发分支类型；
             > 默认可选master、feature、bugfix、hotfix、release以及tag类型，此外还支持用户在此输入自定义的分支类型。若不填写，则默认触发分支为所有类型的分支与Tag 。  

         - 接着还需确定该任务的Runner镜像；
             > 可直接使用高级设置中默认的Runner镜像，也可输入自定义的任务Runner镜像。  

         - 确定是否使用`共享目录设置` ；共享设置用于将任务中产生的工件上传至预置的共享目录，或者从共享目录中下载本条流水线中最近一个任务上传的工件进行使用。 
             > 上传共享目录choerodon-ci-cache：若勾选上传共享目录，则会将此任务中产生的工件或其它文件上传至共享目录，供之后的任务下载使用。            
             > 下载共享目录choerodon-ci-cache: 若勾选下载共享目录，则会在此任务中下载使用该条流水线中已上传至共享目录中的工件，用户便可在此任务中使用。

         - 最后，选择对应的构建模板；目前预置了Maven模板、Npm模板、Go模板，用以支持常用语言的构建。若不选择构建模板，也可按照需求自主组合构建步骤。  

             （1）Maven模板：该模板中包含了以下几个步骤：Maven构建->Docker构建；
        
              步骤一：Maven构建；执行mvn package命令或其他shell命令。还支持用户在此配置公有或私有的依赖库。

              步骤二：Docker构建；执行docker build命令，用于生成Docker镜像。注意：由于该步骤中kaniko指令的限制，建议将此步骤置于同任务中最后一个步骤。


             （2）Npm模板：该模板中包含了以下几个步骤：Npm构建->Docker构建；    
            
              步骤一：Npm构建；执行npm install、npm build run以及npm publish命令。  

              步骤二：Docker构建；执行docker build命令，用于生成Docker镜像。注意：由于该步骤中kaniko指令的限制，建议将此步骤置于同任务中最后一个步骤。  

                

             （3）Go模板：
        
              步骤一：Docker构建；执行docker build命令，用于生成Docker镜像。同时其中包含了Go语言构建所需的命令。     

             （4）自定义步骤：直接点击图中添加步骤的按钮，选择步骤即可。

             

        
    - 发布Chart：即Chart构建；执行chart build命令，用于生成helm chart包。  
         - 选择添加此类型任务后，首先需要填写任务名称、选择或填写触发分支类型；
             > 默认可选master、feature、bugfix、hotfix、release以及tag类型，此外还支持用户在此输入自定义的分支类型。  

        - 接着还需确定该任务的Runner镜像；
             > 可直接使用高级设置中默认的Runner镜像，也可输入自定义的任务Runner镜像。     

    - 代码检查：借助SonarQube实现代码检查的功能。  
         - 选择添加此类型任务后，首先需要填写任务名称、选择或填写触发分支类型；
             > 默认可选master、feature、bugfix、hotfix、release以及tag类型，此外还支持用户在此输入自定义的分支类型。      
         - 接着还需确定该任务的Runner镜像；
             > 可直接使用高级设置中默认的Runner镜像，也可输入自定义的任务Runner镜像。     

        - 最后，还需为此任务配置SonarQube。目前支持2种方式：

        
             （1）用户名与密码；选择此种方式后，需要输入SonarQube的用户名、密码以及地址；测试连接通过后，即可成功创建此任务。   
             
             （2）Token；选择此种方式后，需输入SonarQube的地址和对应的Token。

    - 发布Chart：支持粘贴YAML格式内容创建自定义任务；使得任务添加更加灵活。  

         - 选择添加此类型任务后，需要确定该任务的Runner镜像；
             > 可直接使用流水线中定义的默认Runner镜像，也可输入自定义的任务Runner镜像。  


    - 自定义任务：支持粘贴YAML格式内容创建自定义任务；使得任务添加更加灵活。   
         
         > 按照界面中给的格式，粘贴YAML格式内容来创建自定义任务。

        

### 2.2 修改CI流水线

在树结构中选择某条CI流水线，点击进入该流水线的主页，然后点击顶部的`修改`按钮，右侧弹出CI流水线的修改界面。

此界面支持修改CI流水线的Runner镜像以及其中各个阶段与任务。 

![image](/docs/user-guide/development/image/ci-pipeline-03.png)  

### 2.3 全新执行CI流水线

在树结构中选择某条CI流水线，然后点击该条流水线后面的三点图标按钮，点击选择`全新执行`按钮，此时界面中弹出全新执行界面，需要为此次执行选择目标分支。确认后，点击执行，即可使用该分支上最近的一次提交来执行CI流水线。而流水线中满足触发分支条件的任务才会被触发。

> 目标分支仅能从该应用服务下已有的分支中进行选择。


![image](/docs/user-guide/development/image/ci-pipeline-04.png)  



### 2.4 停用/启用CI流水线

- 在树结构中选择一条启用状态的CI流水线，点击对应的三点图标，选择`停用`，确认后，即可停用CI流水线。
   
     > 若流水线已停用，则仅能进行以下操作：启用；删除。  

- 在树结构中选择一条停用状态的CI流水线，点击对应的三点图标，选择`启用`，确认后，即可启用CI流水线。


### 2.4 删除CI流水线

在树结构中选择一条CI流水线，点击对应的三点图标，选择`删除`，确认后，即可删除CI流水线。    



## 3. 查看CI流水线执行记录  


![image](/docs/user-guide/development/image/ci-pipeline-05.png)  

- 在页面左侧的树结构中，展示了该项目下所有的CI流水线及其产生的记录。对应的CI流水线下方，列出了所有与之对应的执行记录；每条执行记录都有其唯一的编号，树结构中还展示了各条记录的状态与执行时间。不同的执行结果还对应了不同的操作，目前执行记录的执行结果存在以下几种情况：    

    执行结果（状态）  |含义 |对应操作
    |-----|----|----|
    成功|流水线中所有任务均执行成功| 无
    失败|流水线中有任务执行失败| 重试  
    准备中|流水线还未开始执行，处于pendind状态|取消执行
    执行中|流水线中有任务正在运行|取消执行
    已取消|流水线被取消执行后的状态|重试
     
- 点击树结构中某条记录后，右侧主页面中显示出该条记录的所有详情，其中包括：此次执行记录的基本信息以及各阶段、各任务的执行情况。
   - 点击顶部操作栏的`流水线记录详情`，便可查看此次执行的基本详情信息：流水线名称、关联的应用服务、执行结果、触发人员、执行时间、流程耗时、以及提交信息。  
   - 在执行记录主页面，可以查看到各阶段、各任务的执行状态与执行耗时。对于不同类型的任务，成功执行后，界面中支持的操作也有所不同。    

     任务类型  | 成功执行后支持操作
|-----|----|
构建|查看日志、重试
代码检查|查看日志、重试、查看代码质量报告 
自定义任务| 查看日志、重试
  
   - CI流水线中所有类型的任务，只有处于`成功`、`失败`或`已取消`状态时，才允许执行`重试`操作。

## 4. 阅读更多

- [应用服务](../application-service)
- [代码管理](../code-manage)