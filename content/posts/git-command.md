---
title: 常用 Git 相关命令清单
date: 2017-01-01 00:07:52
tags: [Git]
categories: Git
draft: false
---


### Git

1. `git config --global user.name "Your Name"` (安装完 git 后，设置用户名与邮箱)

2. `git config --global user.email "Your email"` (设置邮箱)

3. `ssh-keygen -t rsa -C "youremail@example.com"` (创建 SSH key)

4. `ssh-agent -s` (查看 ssh 是否启用)

5. `ssh -T git@github.com` (验证)

6. `git init` (初始化一个本地仓库)

7. `git add . `(将所有文件加入到跟踪列表)

8. `git commit -m "write something title"` (提交到本地仓库)

9. `git status` (查看 git 文件哪处发生改动)

10. `git remote add origin git@github.com:github账户名/github创建的库.git` (将本地仓库关联到 github 上远程仓库)

11. `git push -u origin master` (将本地仓库的所有内容推送到远程库上，下一次推送时直接 git push)

12. `git clone URL` (克隆远程仓库)

13. `git branch` (查看当前分支)

15. `git checkout -- filename` (文件恢复到最新版本，但丢失最近一次提交后你修改的内容)

16. `git checkout name` (切换分支，name 为分支名称)

17. `git reset --hard commitNumber` (回滚到某个 commit 不保存之前改动过的内容文件)

18. `git push --force` (上条命令回滚之后的变更强行推送至服务器)

19. `git tag` (查看本地 tag 列表)

20. `git tag -d tagName` (删除本地 tag)

21. `git push origin --tags` (将本地 tag 提交到远程)

### Git-Flow

1. `git flow init` (初始化)

2. `git flow feature start MYFEATURE` (开始新 Feature)

3. `git flow feature publish MYFEATURE` (Publish 一个 Feature (也就是 push 到远程))

4. `git flow feature pull origin MYFEATURE` (获取 Publish 的 Feature)

5. `git flow feature finish MYFEATURE` (完成一个 Feature)

6. `git flow release start RELEASE [BASE]` (开始一个 Release)

7. `git flow release publish RELEASE` (Publish 一个 Release)

8. `git flow release finish RELEASE ` (发布 Release)

9. `git push --tags` (发布 Release 后进行，push 到远程)

10. `git flow hotfix start VERSION [BASENAME]` (开始一个 Hotfix)

11. `git flow hotfix finish VERSION` (发布一个 Hotfix)


### Git-Submodule

1. `git submodule add git@github.com:github账户名/github创建的库.git` (在已存在 git 仓库添加子仓库)

2. `git submodule init` (初始化 Submodule)

3. `git submodule update` (更新 Submodule)

### vim

1. `vim filename` (打开编辑文件)

2. 按一下 `i` 键 (进行编辑文件)

3. 按一下 `Esc` 键 (退出 `i` (插入)命令进行其它命令使用)

4. `:wq` (保存文件并退出)

5. `:q!` (强制退出,不保存)

6. `:w filename` (将文件以指定的文件名 filename 保存)

### Git-Flow 规范理解与延伸

![](/images/2017/01/03-1.png)

### Git Commit 规范

为了更加方便查找 git commit 的日志，因此有必要在 commit 的时候进行备注说明类型  

参考阮老师的文章：[地址](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)

与团队成员约定在 commit 填写 message 的时候，在前面加多一个 type，具体的类型如下：

类型 | 描述
----|----
feat | 新功能（Feature）
fix | 修补 bug
style | 界面样式变动（不影响原有业务代码逻辑）
build | Gradle 构建的变动，例如第三方库版本号变动

> 例子：feat: 完成登录注册功能

### 参考链接

* [常用 Git 命令清单](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)
* [git-flow流程规范](https://www.zybuluo.com/Roy270490837/note/835720)
* [Git 工具 - 子模块](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)
* [Git submodule的坑](http://blog.devtang.com/2013/05/08/git-submodule-issues/)

