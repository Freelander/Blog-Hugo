---
title: 如何利用 Android App Bundle 进行 APK 瘦身 28%
date: 2019-10-20 16:37:26
tags: [Android]
draft: false
---


## 介绍  

Android App Bundle 是 Google 在 2018 年 IO 大会上推出一种新发布格式，通过此格式发布的应用，Google Play 商店会根据用户设备屏幕密度，CPU 架构 下发其对应的 APK，这就意味着用户下载的 APK 不会存在与自己设备不匹配的格式资源，从而也达到了大幅度减少 APK 大小。另外利用此格式还可以实现 ``动态组件化`` 的技术，此文不做该技术的过多介绍，感兴趣的可以到 [官方文档](https://developer.android.google.cn/studio/projects/dynamic-delivery) 了解。

## 条件

如果使用 Android App Bundle 发布格式，必须加入 ``Google Play 应用签名计划``。关于如何加入，可以详细查看这份 [官方指南](https://developer.android.com/studio/publish/app-signing#enroll)。

**注意：**

一旦你的应用使用了 Google 应用签名，用户下载的 APK 签名将会是 Google 生成的签名，而不是你自己为应用的签名。因此需要检查你应用使用到的第三方平台 SDK，如果有需要配置签名信息的，记得将 Google 生成的签名信息配置到其对应的地方。

## 原理

生成的 Android App Bundle 包，是不可以直接安装的，App Bundle 会根据设备屏幕密度、CPU 架构、Language 多个维度进行拆分，组装成对各种组合的 Apk；然后 Play Store 会根据用户设备配置，提供对应的组合 APK。通过下图可以促进你理解：

![image](/images/2019/10/01.png)

例如，用户的设备配置如下：arm，xhdpi，en_US，则最终用户下载的 APK 将是（base + arm + xhdpi + en）.apk的组合。

如果用户在设备设置中更改/添加了新语言，则 Play Store 会检测到此情况，并尝试为在设备下载新的其他语言分类 APK。如果互联网不可用，则稍后将下载。

## 如何构建 Android App Bundle

### Android Studio 3.2 版本及以上

>Android Studio --> Build --> Generate Signed Bundle/APK

![image](/images/2019/10/02.png)

![image](/images/2019/10/03.png)

### 通过命令行构建

```
./gradlew bundleRelease
```

## 如何测试生成出来的 Android App Bundle

### 本地使用 [bundletool](https://github.com/google/bundletool) 命令行工具

bundletool 是一种底层工具，可供 Gradle、Android Studio 和 Google Play 用于编译 Android App Bundle 或将 App Bundle 转换为部署到设备的各种 APK。您也可以将 bundletool 作为一种命令行工具，用于重新创建、检查和验证应用 APK 的 Google Play 服务器端版本。

下载地址：[GitHub 代码库](https://github.com/google/bundletool/releases)

#### 1. 将 App Bundle 生成一组 APK

将你从 Android Studio 构建生成的 App Bundle 为应用支持的所有设备生成一组 APK，用一个以 ``.apks`` 为文件扩展名的容器装起来。

>如果您未指定签名信息，bundletool 会尝试使用调试密钥为 APK 签名，这里我为了方便我将这些 APK 部署到设备，添加了应用的签名信息。

```
java -jar bundletool.jar build-apks 
--bundle=/MyApp/my_app.aab
--output=/MyApp/my_app.apks
--ks=/MyApp/keystore.jks
--ks-pass=file:/MyApp/keystore.pwd
--ks-key-alias=MyKeyAlias
--key-pass=file:/MyApp/key.pwd
```

#### 2. 将 APK 安装到连接的设备

生成一组 APK 后，bundletool 可以将该组中适当 APK 的组合安装到连接的设备。

```
java -jar bundletool.jar install-apks --apks=/MyApp/my_app.apks
```

>如果连接了多个设备，请添加 --device-id=serial-id 标记来指定目标设备。

#### 测试

通过上面两条命令，已经为你即将发布的应用安装到你的设备上，最后检查下 app 运行情况，没问题就可以将刚才生成的 aab 上传到商店后台进行版本发布。

#### 更多

想了解更多关于 bundletool 工具的使用方法，可以到 [这里](https://developer.android.com/studio/command-line/bundletool) 了解。

### 直接将 App Bundle 上传到应用 Play 商店后台

这个操作很简单，跟平常发布版本操作差不多，具体如下：

> 版本管理 --> 应用版本 --> 创建版本 --> 添加 Android App Bundle --> 保存

此时想检查刚上传的 App Bundle 生成的 APK 情况，以及下载生成的 APK 到本地进行测试，操作步骤如下：

> 版本管理 --> 软件工件库 --> 软件工件库草稿找到刚才上传的版本 --> 探索 --> 下载设备专属 APK

![image](/images/2019/10/06.png)

![image](/images/2019/10/04.png)

## 实验

### 本地语言包

如果本身 App 是支持多语言的，在应用内切换语言，Play Store 无法检测应用语言改变，因此需要禁止 App Bundle 根据语言拆分安装包。

在 App 下的 build.gradle 文件添加以下配置：

```
android {
    bundle {
        language {
            // Specifies that the app bundle should not support
            // configuration APKs for language resources. These
            // resources are instead packaged with each base and
            // dynamic feature APK.
            enableSplit = false
        }
    }
}
```

### 图片资源

目前大部分应用图片资源只适配一些高分辨率 xxxhdpi，xxhdpi。

验证 App Bundle 拆分出来的低分辨率包，是否会不存在高分辨率的图片资源？

> 将生成 "apks" 的文件更改为 "zip" 格式 --> 解压 --> standalones --> 选一个低分辨率的安装包 --> 更改为 "zip" 格式 --> 解压 --> 查看资源文件

![image](/images/2019/10/07.png)

结果：低分辨率包依然存在我们项目高分辨率的图片。

## 最后

最后来看下使用 App Bundle 生成的 APK，与上个版本相比的数据。

![image](/images/2019/10/05.png)

## 资料

- [官方文档介绍](https://developer.android.com/guide/app-bundle/)
- [缩减应用大小](https://developer.android.com/topic/performance/reduce-apk-size)
- [如何测试构建出来的 aab 包](https://developer.android.com/studio/command-line/bundletool)
- [Android App瘦身新姿势——Android App Bundle](https://blog.csdn.net/u012124438/article/details/83508944)

## 推荐阅读

- [终极 Apk 瘦身优化](https://gojun.me/2019/09/10/android-reduce-apk-size/)
