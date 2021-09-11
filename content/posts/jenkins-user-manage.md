---
title: "Jenkins 用户权限管理指南"
date: 2021-09-11T18:04:21+08:00
tags: [Jenkins]
draft: false
---

### 新增用户

路径：Manage Jenkins --》Manage User --》Create User

![img](/images/2021/09/jenkins-user-manage-01.png)

### 安装权限管理插件

插件名称：Role-based Authorization Strategy

![img](/images/2021/09/jenkins-user-manage-02.png)

### 全局配置

路径：Manage Jenkins --》Configure Global Security --》Authorization

将授权策略更改为 Role-Based Strategy 选项，该选项是插件安装完成后才有的。

![img](/images/2021/09/jenkins-user-manage-03.png)

### 管理角色

#### 全局角色
路径：Manage Jenkins --》Manage and Assign Roles --》Manage Roles

默认有个管理员，根据实际情况来创建不同角色，然后分配不同权限，值得注意的是创建的角色都要勾选上 ``Overall Read`` 权限，否则无法

比如我创建一个developer 角色，给予部分权限：

![img](/images/2021/09/jenkins-user-manage-04.png)

#### 项目角色

根据现有的项目进行分配，比如我这里创建了三个，用户分配到指定项目，那么他就只能操作指定项目。

![img](/images/2021/09/jenkins-user-manage-05.png)

项目匹配使用正则，比如我这里 ``Kotlin.*``，匹配 Kotlin 开头的项目。

添加完之后可以点击匹配规则哪里，会弹起弹窗显示匹配到的项目。

![img](/images/2021/09/jenkins-user-manage-08.png)

### 分配角色

对上面新创建的 Test User 用户进行权限配置：

分配 developer 权限：

![img](/images/2021/09/jenkins-user-manage-06.png)

分配到 Kotlin 项目：

![img](/images/2021/09/jenkins-user-manage-07.png)

### 登录 TestUser 账号确认

![img](/images/2021/09/jenkins-user-manage-09.png)







