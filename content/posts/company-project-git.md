---
title: 公司项目 Git 管理流程及规范
date: 2021-07-05T00:57:08+08:00
tags: [Git]
categories: Git
draft: false
---

目前公司项目 Git 管理规范是基于 git-flow 扩展而来。

git-flow 最先来源于 Vincent Driessen 一篇名为[“一种成功的Git分支模型”](https://nvie.com/posts/a-successful-git-branching-model/)的博文，后来 git-flow 被誉为在 Git 之上构建的一项软件开发最佳实践。

## 工具

通过安装工具可以帮助我们更方便按照 git-flow 工作流程进行项目管理。

### Mac

> brew install git-flow

**其他终端安装方法，可以到 [这里](https://github.com/nvie/gitflow/wiki/Installation) 查看。**

## 常用命令

* **初始化使用：** ``git flow init``
* **创建 Feature 分支：** ``git flow feature start branch_name``
* **创建 Release 分支：** ``git flow release start branch_name``
* **结束 Release 分支：** ``git flow release finish branch_name``
* **将结束 Release 分支时产生的 tag 推到远程：** ``git push --tags``

## 公司项目 Git 管理流程

下面的流程图，展示了目前项目基于 git-flow 并结合公司业务拓展定义的 git 管理流程规范。

![Local Image](/images/2021/07/company-project-git-01.png)

## 分支命名规范

### 需求开发分支命名规范

> 版本号_开发人名称首字母_需求简述

例如：feature/6.15.0_hgj_优化AfterPay介绍页面

若使用 git-flow 工具命令创建 

```bash
git flow feature start 6.15.0_hgj_优化AfterPay介绍页面
```

### 发版分支命名规范

> 发版的版本号

例如：release/6.15.0

若使用 git-flow 工具命令创建 

```bash
git flow release start 6.15.0
```
### 效果展示

![Local Image](/images/2021/07/company-project-git-02.png)

## Git Commit 规范

为了更加方便查找 git commit 的日志，因此有必要在 commit 的时候进行备注说明类型。

参考阮老师的文章：[地址](https://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)

与团队成员约定在 commit 填写 message 的时候，在前面加多一个 type，具体的类型如下：


类型 | 说明
---|---
feat | 新功能（feature）
fix | 修补 bug
docs | 文档（documentation）
style | 格式（不影响代码运行的变动）
refactor | 重构（即不是新增功能，也不是修改bug的代码变动）
test | 增加测试
chore | 构建过程或辅助工具的变动

> 例子：feat: 划线价逻辑调整 

**注意冒号是使用英文的，且后面加多个空格。**

## 资料

* [git-flow 备忘清单](http://danielkummer.github.io/git-flow-cheatsheet/index.zh_CN.html)
* [git-flow 流程规范](https://www.zybuluo.com/Roy270490837/note/835720)


