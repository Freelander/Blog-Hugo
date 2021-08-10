---
title: Android 原生项目如何引入 Flutter
date: 2020-03-05 23:26:26
tags: [Flutter]
categories: Flutter
draft: false
---

有两种方式创建，一种使用 Android Studio 创建集成 Flutter 模块，一种通过命令创建手动集成。

最开始使用 Android Studio 创建，但是一直卡在 ``Creating Flutter Project `` 网上查了很多解决方法均无法解决，因此我是通过手动集成的。

### 一、创建 Flutter 模块

打开终端选择自己指定的路径下，通过以下命令创建模块：

```
flutter create - t module --org com.chicdeals.flutter ky_flutter
```

创建完之后，打开 ``pubspec.yaml`` 文件拉到最后看到比平常创建的 Flutter 项目多出下面信息：

```
  # This section identifies your Flutter project as a module meant for
  # embedding in a native host app.  These identifiers should _not_ ordinarily
  # be changed after generation - they are used to ensure that the tooling can
  # maintain consistency when adding or modifying assets and plugins.
  # They also do not have any bearing on your native host application's
  # identifiers, which may be completely independent or the same as these.
  module:
    androidX: true
    androidPackage: com.chicdeals.flutter.ky_flutter
    iosBundleIdentifier: com.chicdeals.flutter.ky_flutter
```

修改下信息，去掉包名后面的 ``.ky_flutter`` 简短下包名。

### 二、Android 项目引入 Java8

在尝试将 Flutter 模块项目集成到宿主 Android 应用之前，请先确保宿主 Android 应用的 ``build.gradle`` 文件的 ``android { }`` 块中声明了以下源兼容性，例如：

```
android {
  //...
  compileOptions {
    sourceCompatibility 1.8
    targetCompatibility 1.8
  }
}
```

### 三、将 Flutter module 作为依赖项

#### 方案A - 依赖 AAR

这种方式会将 Flutter 库打包成由 AAR 和 POM artifacts 组成的本地 Maven 存储库。这种方案可以使你的团队不需要安装 Flutter SDK 即可编译宿主应用。

1. 在你的 Flutter 模块路径下，执行如下命令：

```
flutter build aar
```

构建完成后，终端会输出如何完成集成操作的提示。

![image](/images/2020/03/native_flutter_01.png)

需要注意的是以下配置是放在最外层位置：

```
repositories {
        maven {
            url '/Users/apple/FlutterDev/project/ky_flutter/build/host/outputs/repo'
        }
        maven {
            url 'https://storage.googleapis.com/download.flutter.io'
        }
      }
```

应用只配置 debug、pre、release 构建模式，因此在引用的时候，可以这样配置：

```gradle
debugImplementation 'com.chicdeals.flutter:flutter_debug:1.0'
preImplementation 'com.chicdeals.flutter:flutter_profile:1.0'
releaseImplementation 'com.chicdeals.flutter:flutter_release:1.0'
```

> 如果你是使用 Android Studio 开发工具，你也可以直接点击 Android Studio 菜单中的 Build > Flutter > Build AAR 为 Flutter 模块构建 AAR。

![image](/images/2020/03/native_flutter_02.png)

#### 方案 B - 依赖模块的源码

该方式可以使你的 Android 项目和 Flutter 项目能够同步一键式构建。当你需要同时在这两个项目中进行快速迭代时，这种方案非常方便，但是此时，你的团队必须安装 Flutter SDK 才能构建宿主应用程序。

具体实现方式，如有需要请看官方指导文档，在这里我就不做简述了，因为我是使用方案A，没有选择方案 B。

### 参考链接

* [将 Flutter module 集成到 Android 项目](https://flutter.dev/docs/development/add-to-app/android/project-setup)
