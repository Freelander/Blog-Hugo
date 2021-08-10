---
title: Flutter 环境搭建（Mac）
date: 2020-01-20 23:43:21
tags: [Flutter]
categories: Flutter
draft: false
---

### 下载 Flutter SDK

1. 获取Flutter SDK的最新稳定版本：

将 SDK 下载到指定文件夹下，建议创建一个专门开发 Flutter 项目的文件夹下。

直接到[官网](https://flutter.dev/docs/development/tools/sdk/releases?tab=macos)下载稳定版回来。

也可以通过 git 克隆对网络环境要求比较高，建议是从官网下载。

```
git clone https://github.com/flutter/flutter.git
```

2. 配置环境变量

```
 export PATH="$PATH:[PATH_TO_FLUTTER_GIT_DIRECTORY]/flutter/bin"
```

>[PATH_TO_FLUTTER_GIT_DIRECTORY] 指上面你下载回来 SDK 存放的路径。

将上面的代码放到 ``.bash_profile`` 文件中

```
vim ~/.bash_profile
```

生效配置环境

```
source ~/.bash_profile
```

3. 检查环境变量是否配置成功

```
flutter -h
```
![image](/images/2020/01/flutter_build_01.png)

如果出现这个信息说明环境已经配置成功了。

4. 检查 Flutter 环境配置是否完成

```
flutter doctor
```

![image](/images/2020/01/flutter_build_02.png)

>留意输出的信息，并且需要处理 ``[x]`` 与 ``[!]`` 的提示信息。

![image](/images/2020/01/flutter_build_03.png)

如果配置全没问题后，执行 ``flutter doctor`` 命令输出信息以上情况。

### 下载 Dart SDK

Mac 通过 HomeBrew 安装

```
brew tap dart-lang/dart
brew install dart
```

其他实用命令

```
#更新Dart版本
brew upgrade dart

#切换本地指定版本
brew switch dart 2.1.0

#查看当前Dart版本信息
brew info dart
```

### 文档

* [Flutter 官方安装指南](https://flutter.dev/docs/get-started/install/macos)
* [Dart 安装指南](https://dart.dev/get-dart)
