+++
title = "状态机方案"
weight = 3
description = "问题类型和状态机的组合并应用于项目，决定项目中所有问题类型流转的方案"
+++

# 状态机方案

不同的问题类型及不同的状态机组合在一起形成状态机方案。本版本，项目初始化时，给项目一套默认的状态机方案，若不想使用默认方案，组织管理员可在此处编辑状态机方案使项目中问题的流转方案发生改变。

- **菜单层次**：组织层
- **菜单路径**：问题设置 > 状态机设置 > 状态机方案
- **默认角色**：组织管理员

## 状态机方案列表
<p>状态机列表中显示出所有的状态机名称及状态机关联的状态机方案</p>

![enter description here](/docs/user-guide/system-configuration/issue-configuration/image/state-machine-scheme-list.png "state-machine-scheme-list")

1. 名称：状态机方案的名称
2. 描述：状态机方案具体作用的描述
3. 项目：状态机方案关联的项目
     <blockquote class="note">
          一个项目默认初始化两个状态机方案（敏捷和测试）
     </blockquote>
4. 类型：状态机方案包含的问题类型（一个状态机方案中包含多个问题类型）
5. 状态机：状态机方案包含的状态机（一个状态机方案包含多个状态机）
6. 操作：编辑，可在状态机方案中添加，移除状态机，默认状态机无法被移除

## 编辑状态机方案
在编辑状态机方案页面下，可以对状态机方案进行编辑，包括状态机的添加移除和问题类型与状态机的关联
 <p>
     <blockquote class="note">
           若对状态机方案进行了编辑，编辑的是草稿状态机方案，想要此状态机方案再项目中生效，需要对草稿状态机方案进行发布，否则项目中使用的还是原来的状态机方案
     </blockquote>
</p>

![enter description here](/docs/user-guide/system-configuration/issue-configuration/image/edit-state-machine-scheme.png "state-machine-scheme-list")

1. 添加状态机：点击`添加状态机`按钮在添加状态机页面选择已有的状态机并关联到问题类型添加到此状态机方案中
2. 修改状态机关联的问题类型：在状态机侧面点击`编辑`按钮，在`问题类型关联到状态机`将状态机和问题类型进行关联
3. 在此页面中可以移除草稿状态机方案中手动添加的状态机
4. 对状态机方案进行编辑后，会出现此处按钮。在此处中点击`发布`按钮，草稿状态机方案被发布，项目使用发布后的状态机方案；点击`删除草稿`按钮，删除草稿状态机方案（若退出编辑页面，再次进入`编辑状态机方案`页面，页面中显示的是草稿状态机方案）；点击`查看原件`可以查看原状态机方案（草稿状态机方案仍存在，在`查看原件`页面点击`编辑草稿`按钮就能回到草稿编辑页面看到草稿状态机）。
