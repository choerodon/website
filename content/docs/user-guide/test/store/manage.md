+++
title = "管理测试用例"
description = ""
weight = 3
+++

# 管理测试用例

## 1. 概述

创建完测试用例后，即可查看测试用例列表、详情、步骤及测试执行结果。

通过此页面您将了解到如何查看测试用例步骤、具体执行次数、执行结果，以及如何与团队展开关于测试用例的合作。

## 2. 编辑测试用例

### 2.1 测试步骤操作

点击任一测试用例，右侧会显示出用例详情页面，您可以在此添加、编辑、删除、复制和移动测试用例。

![image](/docs/user-guide/test/image/IssueManage/IssueManage-06.png)

1. 添加测试步骤：点击添加测试信息按钮，出现测试步骤表格行。
2. 编辑测试步骤：在多出的一列测试步骤行中填写测试步骤、测试数据、预期结果，上传附件，点击小勾图标完成添加测试步骤。
3. 删除测试步骤：点击该删除图标可以删除对应的测试步骤。
4. 复制测试步骤：点击复制图标，复制一条测试步骤。
5. 移动测试步骤：点击移动图标拖动测试步骤进行测试步骤排序

### 2.2 编辑测试用例详情

点击测试用例概要下方的`详情`，可查看和编辑该测试用例详情，如图所示。

![image](/docs/user-guide/test/image/IssueManage/IssueManage-07.png)

- 详情包括：状态、阶段名称、执行人、执行日期
- 描述：在用例详情的描述中可以编辑用例的前置条件，角色等。
- 附件：可以在此下载和上传用例所用的附件
- 问题链接：可查看和该用例有关系的问题，可对用例添加和问题的关系


### 2.3 查看测试用例记录

点击测试用例概要下方的`记录`，可查看该测试用例活动日志，如图所示。

![image](/docs/user-guide/test/image/IssueManage/IssueManage-08.png)

## 3. 测试用例树的结构

用例库用测试用例树来分类测试用例，您可以自定义测试用例文件夹的目录结构（最多可有9层目录结构）。

### 3.1 添加一级目录

点击操作栏`添加一级目录`，可以添加测试用例一级目录。

### 3.2 编辑文件夹

点击文件夹右侧![三点](/docs/user-guide/manager-guide/image/more-vert.png)按钮标识，可对该文件夹进行重命名或者删除操作。

![image](/docs/user-guide/test/image/IssueManage/IssueManage-09.png)

### 3.3 添加子文件夹

点击文件夹上添加文件夹按钮，填写文件夹名称，点击空白处或者回车即可向该文件夹添加子文件夹；

<blockquote class="note">
         文件夹下无用例时可添加子文件夹；
      </blockquote>



## 4. 查看文件夹对应测试用例

点击测试用例树的对应项将展示其内部的测试用例。

- 点击单个目录，加载该目录下所有测试用例
- 点击单个文件夹，加载文件夹内内测试用例

## 5. 测试用例移动/复制

测试用例可以进行拖动来改变文件夹。

选中测试用例直接拖动鼠标即可将其移动至目标文件夹。同时该用例会显示当前拖动状态，包含移动/复制。

>可使用shift或ctrl进行多选

## 6. 阅读更多

- [导出测试用例](../import)
- [创建测试计划](../create)