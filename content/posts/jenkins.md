---
title: MacOs 最全的 Jenkins Android 自动打包构建教程
date: 2019-09-24 21:51:40
tags: [Jenkins, MacOs]
draft: false
---

## 安装

1. 下载安装可以到 Jenkins [官网](https://jenkins.io/zh/) 下载Mac版本的安装包，然后进行手动安装（坑多，不推荐）
2. 使用 brew 下载安装Jenkins（推荐，简单傻瓜式，下载安装配置一步到位）具体做法如下：
``brew install jenkins``

## 启动

安装完成在终端输入 ``jenkins`` 启动

然后在浏览器打开 ``http://localhost:8080``

首次启动会看到一个界面要求你输入管理员密码，如果你是命令安装的，在终端启动的时候会输出此密码。

![image](/images/2019/09/07.png)

按它页面提示的路径找到该文件，复制里面的一串字符到输入框回车即可。

会出现找不到此文件的情况，原因是 secrets 文件夹未授权当前用户访问。

解决方法：https://stackoverflow.com/a/38943610

## 基本配置

第一次启动要求你创建一个管理员账号。

## 插件的安装

进入后，会让你选择推荐插件安装还是自定义插件安装，对于小白可以直接点击推荐安装。

## Jenkins的环境配置

配置 JDK 路径

Mac 查看 JDK 路径方法：
``打开终端，执行    /usr/libexec/java_home -V``

![image](/images/2019/09/08.png)


## 环境变量配置（ANDROID_HOME的配置）

![image](/images/2019/09/09.png)

## 创建项目

1）输入项目名称，选择自定义构建项目

![image](/images/2019/09/10.png)

2）配置 Git

![image](/images/2019/09/11.png)

3）配置对项目有写权限的账户

> 进入 GitHub --> Settings --> Developer settings --> Personal Access Token --> Generate new token

![image](/images/2019/09/12.png)

复制刚才生成的 token

![image](/images/2019/09/13.png)

回到 Jenkins 配置

![image](/images/2019/09/14.png)

选择``Secret text``，``Secret`` 填入前面在 GitHub 上生成的 Personal access tokens，Description 随便写一些描述信息，如下图：

![image](/images/2019/09/15.png)

3）Gradle 构建脚本

![image](/images/2019/09/16.png)

4）构建完成存档的文件

![image](/images/2019/09/17.png)

## 构建

1）开始

![image](/images/2019/09/18.png)

2）查看控制台输出日志

![image](/images/2019/09/19.png)

![image](/images/2019/09/20.png)

3）主页面显示的安装包

![image](/images/2019/09/21.png)

## 向 GitHub 提交代码时触发自动构建

需要在 GitHub 对应项目上配置 Webhooks。

> GitHub 项目 --> Settings --> Webhooks --> Add webhooks

如下图：填入 ``Payload URL``

![image](/images/2019/09/22.png)

**解释：** Webhook 是通知 Jenkins 时的请求地址，用来填写到 GitHub 上，这样 GitHub 就能通过该地址通知到 Jenkins；
假设 Jenkins 所在服务器的地址是：192.168.0.1，端口为8080，项目是 github-webhook，那么 Webhook 地址就是 http://192.168.0.1:8080/github-webhook。

``再次提醒，上述地址必须是外网也能访问的，否则GitHub无法访问到Jenkins。``

## 测试

至此配置已经完成，将 GitHub 上的项目克隆下来，修改某个文件提交，测试看 Jenkins 是否能自动构建。

**Good Luck!**

## 推荐阅读

* [如何利用 frp 实现 Mac 的内网穿透](https://gojun.me/2019/09/23/jenkins-frp/)

## 参考链接

1. [这应该是最全的 Jenkins Android 自动打包构建教程](https://juejin.im/post/5b6a542b5188251a9e171bf2)
2. [MAC 上搭建Jenkins实现 Android 自动打包](https://blog.csdn.net/ATangSir/article/details/71699403)
3. [实战：向 GitHub 提交代码时触发 Jenkins 自动构建](https://blog.csdn.net/boling_cavalry/article/details/78943061)
4. [搭建 frp 实现内网穿透](https://blog.csdn.net/zengd0/article/details/79522253)
5. [利用 frp 为 jenkins 配置内网打包节点](https://alphagao.com/2018/05/05/config-native-package-node-for-jenkins-server/)
