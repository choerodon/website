+++
title = "配置列和状态"
description = ""
weight = 3
+++

# 配置列和状态

## 1. 概述

看板支持用户根据自己的需求进行自定义配置，包括列和状态的设置、工作日志配置、看板名称修改等。

通过此页面，您将了解到如何配置列和状态。

点击工具栏的`配置看板`按钮，即可进入配置看板页面。

![image](/docs/user-guide/cooperation/iteration-plan/image/scrumboard-14.png)

## 2. 配置列

包括添加列、删除列、移动列以及对列的问题数进行约束设置，您可以根据需要修改列的名称，但是系统会默认列状态只有待处理、处理中、已完成3种类别，关于列的含义、类型和状态，请查看[什么是列](../whatisboard)。

### 2.1 添加列

1. 单击页面左上角的添加列按钮

    ![image](/docs/user-guide/cooperation/iteration-plan/image/scrumboard-15.png)

2. 输入列名称，选择列类型

- 创建待处理的列：若看板中已存在待处理列，则系统会跳过创建待处理的列，改为创建处理中的列。
- 创建处理中的列：处理中的列可以有多个，所以创建好的处理中的列会直接放在已完成列之前。
- 创建已完成的列：若看板中已存在已完成列，则系统会跳过创建已完成的列，改为创建处理中的列。

### 2.2 删除列

在列的右上角点击删除按钮，将删除此列。

![image](/docs/user-guide/cooperation/iteration-plan/image/scrumboard-16.png)

> 若列中存在状态，删除此列后，列中的状态将归到未对应的状态列中，状态中的问题不变。 

### 2.3 移动列

将鼠标悬停在列名称顶部的移动按钮，可将列向左或向右拖动到它的新位置。

- 列之间可任意调整顺序。
- 无论如何调整，起始列的类别始终是待处理，终止列的类别是已完成，起始列与终止列之间的类别是处理中。

### 2.4 设置列约束

列约束是指对该列的问题个数进行设置（在制品限制），这是看板的关键部分，这样您就可以管理问题在不同列中的流动。

如果开启列约束，则可以针对每一列对问题的最大数量和最小数量进行设置，您可以为所有列指定约束，或者只指定其中的一些列。如果问题数量超出了此列的约束，在看板中，此列将限制问题的移除或移入。

![image](/docs/user-guide/cooperation/iteration-plan/image/scrumboard-17.png)

列约束下拉框有三个选项，分别是：无、问题计数、问题计数，不包括子任务。

- 无选项表示列对问题数量没有任何约束，问题可任意拖拽。
- 问题计数选项可以为每一列配置问题数量的最大值和最小值，即当我们要从一列中拖拽出一个问题时，会判断当前列的数量是否小于等于最小值，如果是，则拖出问题的列变为黄色；同理，一个问题拖入某一列，会判断当前列的问题数量是否大于等于最大值，如果是，则该列变为红色。
- 问题计数，不包括子任务与问题计数同理，唯一不同的是列的问题计数不包括子任务在内。 

>- 列约束的值将出现在您为其设置的每个列的顶部。
>- 列约束适用于列中问题的总数，而不管问题是否由快速筛选器隐藏。


## 3. 配置状态

此时的状态配置是指修改状态的名称，系统默认状态类型（待处理、处理中和已完成）不可修改，详情可查看[状态](../whatisboard)。

状态名称支持用户自定义，但是每个状态必须归属于上述三种状态类别中。

比如当流程中出现审核步骤时，可添加待审核状态和审核完成状态，其中待审核状态归属于在处理中，审核完成归属于已完成。

### 3.1 添加状态

1. 单击页面左上角的”添加状态”按钮。

    ![image](/docs/user-guide/cooperation/iteration-plan/image/scrumboard-18.png)

2. 输入状态名称与类别

    如果状态存在于组织层的状态列表中，就会自动识别出类别，点击确定即可创建一个新的状态。

3. 新建的状态会放在未对应区域中，需要自己拖动到指定的列中。

> 一个列可以包含一个或多个状态，列里的状态可拖拽到其他任意列中，也可拖拽到未对应区域中，若拖拽到未对应区域中则该状态不会在看板上显示；状态名称已在项目中存在时，则无法重复创建状态。 

### 3.2 管理状态

状态可拖拽到其他列中，此时状态的类型与名称不会发生改变。

状态`设置已完成`功能：状态若勾选了“设置已完成”，当问题移入该状态后，表示该问题已解决。反之，未勾选表示未解决。

当一个版本发布时，问题统计信息中会依据问题是否勾选了`设置已完成`的状态来判断一个问题是否确定已解决。

### 3.3 删除状态

单击删除按钮即可删除该状态，但该状态需要满足以下条件：

- 状态位于未对应的状态列中；
- 状态未关联任何问题。

若状态位于对应的列中，则将其移动至页面最右端的未对应状态列中。

![image](/docs/user-guide/cooperation/iteration-plan/image/scrumboard-19.png)

## 阅读更多

- [问题状态](../whatisboard)
- [多看板](../multi-board)
- [定义工作日历](../jounal)