---
title: 小米手机 Application Installation Failed
date: 2018-01-13 22:34:56
tags: 
categories: 随笔
draft: false
---

今天在开发中遇到了一个比较奇葩的问题，手机连接上电脑并且已经在 Android Studio 运行窗口看到设备，但是 App 就是一直安装不到 小米6 手机上, 也不能通过 adb 命令安装。

![](/images/2018/01/01.png)

然后疯狂查找了一波资料，顺便在这里做了下解决过程的记录。

### 解决步骤：

1. 确认打开开发者选项模式（设置->关于手机->MIUI版本,连续点击MIUI版本）
2. 开启开发者选项以及 USB 调试
3. 再运行项目看下是否可以

**如果还是不行**

4. 关闭 “开启MIUI优化”（设置->更多设置->开发者选项->启动MIUI优化）
5. 重启手机，再次运行项目看看

**如果还是不行**

回到 Android Studio 开发工具

6. 在 File > Settings > Build,Execution,Deployment > Instant Run >取消选择 (Enable Instant Run to hot swap code)
7. 再次运行项目看看

**如果还是不行**

8. 尝试 Clen Project >> Rebuild Project >> 再尝试下运行项目

> 还是不行，兄dei 那就换手机吧
> 还有一种情况是手机的存储太低了，导致无法安装 app

### 参考链接

1. https://blog.csdn.net/forwardyzk/article/details/68489711
2. https://stackoverflow.com/questions/42687607/application-installation-failed-in-android-studio

