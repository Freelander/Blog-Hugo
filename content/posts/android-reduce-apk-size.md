---
title: 终极 Apk 瘦身优化
date: 2019-09-10 23:19:55
tags: [Android]
draft: false
---

# 前言

Apk 随着我们开发迭代逐渐的增大，越来越臃肿肥大，在下载安装过程中，它们耗费的流量会越多，安装等待时间也会越长，这就意味着下载转化率越低，进而影响到广告效果。

因此 Apk 瘦身是优化的重要一部分，开发有必要定期对 Apk 进行瘦身优化。

# 分析

使用 Android Studio Analyze APK 进行分析，首先构建出一个 release 包，然后拖到 AS 开始分析。

![image](/images/2019/09/01.png)

从上图分析结果，可以明显看出 Apk 的大小主要是 So 文件以及资源文件占据。

# 开始瘦身

## Apk 瘦身之 So 文件

还没有了解 So 文件的同学，可以看看这篇文章 [地址](http://allenfeng.com/2016/11/06/what-you-should-know-about-android-abi-and-so/)。

再看看这篇文章 [地址](https://www.cnblogs.com/didikee/p/6683415.html)，采集了市场主流多款 App 进行分析。


指令集 | 厂商 | 位数 | 描述
---|---|---|---
arm64-v8a | ARM | 64 | 第 8 代，64 位 ARM 处理器，很少设备，如：三星 Galaxy S6、华为 Mate 8
armeabi-v7a | ARM | 32 | 第 7 代及以上的 ARM 处理器。2011 年以后，大部分的生产的 Android 设备都使用它（目前主流）
armeabi | ARM | 32 | 第 5、6 代的 ARM 处理器，早期的手机使用的比较多
x86 | Intel | 32 | 平板、模拟器（x86设备也支持armeabi-v7a和armeabi）
x86_64 | Intel | 64 | 64位的平板

通过上面查找到的资料，综合评估了下，决定项目只保留 armeabi-v7a 格式的 So 文件。

修改主工程 build.gradle 下的 abiFilters：

```
android {
    // Some other configuration here...
    defaultConfig {
        ndk {
            abiFilters 'armeabi-v7a'
        }
    }
}
```

## Apk 瘦身之资源

### **移除无用的资源文件**

随着版本迭代，会产生很多一些无用资源，``虽然在 build.gradle 中设置 shrinkResources 为 true 后，每次打包的时候就会自动排除无用的资源。但好像如果一些没用的代码对某个资源引用，貌似不会被移除``。

Android Studio 提供了一键清除无用资源文件功能

> 在项目 app 文件下右键，Refactor --> Remove Unused Resources..

![image](/images/2019/09/02.png)

我尝试勾选 ``Delete unused @id declarations too`` 发现布局有使用的 id 被检查到没有使用，因此我建议不要勾选这个选项。

为了安全建议大家执行操作前先 Preivew 下。

单独对某个文件夹下的资源进行检查方法：

> 将光标定位在检查的文件夹 --> Analyze --> Run Inspection by name --> unused resource

![image](/images/2019/09/03.png)

``在检查的时候，发现没有检查出未使用的图片资源，但是有些图片确实是没有使用，尝试了好几次，最后发现原来是我自定义的 lint 配置，禁止检查 UnusedResources 资源了，因此在使用此功能时先注释掉原来自定义的 lint 规则。``

```
lintOptions {
        lintConfig file("gen/bgquality/lint/lint.xml")
        disable 'UnusedResources'
    }
```

``使用 Lint 工具检查``

详细使用介绍可看这篇文章 [地址](https://juejin.im/entry/586f7ec1ac502e006c0c2070#android-studio-%E4%B8%AD%E4%BD%BF%E7%94%A8-lint)

### **压缩图片资源**

有些设计师从 PS 导出图后就直接发给开发了，因此需要我们自己压缩一次。推荐使用一个简单实用压缩工具 [Tinypng](https://tinypng.com/)。

另外有一个比较专业压缩工具 [Squoosh](https://squoosh.app/)，该工具是 Google 推出的一款免费开源图片压缩工具，有很多可定制化功能，例如图片尺寸调整、压缩比例调整、图片格式调整等等，如果你对压缩图片有比较多要求的时候，可选择这个工具。

### **使用 WebP 图片**

可以到知乎看下这篇文章 [WebP 相对于 PNG、JPG 有什么优势？](https://www.zhihu.com/question/27201061)

``需要注意：``
> Android 从 4.0 才开始 WebP 的原生支持，意味着要兼容 4.0 以下机型需要添加适配库；当然现在市面上适配 4.0 以下的应用已经很少了。
Android 4.2.1+ 才支持显示含透明度的 WebP，因此最低版本小于 4.2.1 的 App 也不是想用就能用的。

建议大家在 Google Analytics 平台查看自己应用在 4.2.1 以下版本用户量多少，评估是否可以升上去。

**如何转换**

> 在 AS 里面提供了支持一键转换 WebP 格式功能，需要转换的图片右键 --> Convert to Webp...

![image](/images/2019/09/04.png)

当然你也可以外部工具对图片进行 WebP 格式转换，推荐使用 [Squoosh](https://squoosh.app/)。

### **适配多套分辨率图片**

根据应用用户量使用的屏幕分辨率数据来，来决定你最终使用哪几套图片，从数据上来看目前设备分辨率大多数都是 1080x2094 和 1080x1920，因此这里我只用了 ``xxhdpi`` 和 ``xxxhdpi`` 两套资源。

下面是各套资源对应设备的分辨率以及像素信息表格：

类型 | 分辨率(px) | 屏幕像素密度(dpi)
---|---|----
ldpi |  200x320 | 120
mdpi | 320x480 | 160
hdpi | 480x800 | 240
xhdpi | 720x1280 | 320
xxhdpi | 960x1600 | 480
xxxhdpi | 1440x2560 | 640

数据来自：http://iconhandbook.co.uk/reference/chart/android/

## Apk 瘦身之代码混淆

开启代码混淆能将代码中的类、字段、方法的名字改为简短、无意义的名字；移除未使用的类、字段、方法、属性等，因此可以减少 Dex 文件的大小。

```
android {
    buildTypes {
        release {
            // 开启混淆
            minifyEnabled true
            proguardFiles getDefaultProguardFile(‘proguard-android.txt'),
                    'proguard-rules.pro'
        }
    }
    ...
}
```

## Apk 瘦身之开启 shrinkResources

开启 shrinkResources 可以移除无用资源，依赖于 minifyEnabled，必须和 minifyEnabled 一起用，就是打开 shrinkResources 也必须打开 minifyEnabled。

```
android {
    buildTypes {
        release {
            // 开启混淆
            minifyEnabled true
            // 移除无用资源
            shrinkResources true
            
            proguardFiles getDefaultProguardFile(‘proguard-android.txt'),
                    'proguard-rules.pro'
        }
    }
    ...
}
```

这里开启我发现有些未使用的资源无法移除的，可能是一些没用的代码对一些资源进行引用，导致没有移除，建议大家还是使用 ``Remove Unused Resources`` 来检查。

## Apk 瘦身之配置 resConfigs

配置应用使用哪些资源，例如你的应用只支持英语和阿拉伯语，那么可以这样配置：

```
android {
    defaultConfig {
        ...
        // 语言资源，只支持英语和阿拉伯语
        resConfigs "en","ar"
        // 图片资源，只支持xhdpi和xxhdpi屏幕的资源
        resConfigs "xxhdpi","xxxhdpi" 
    }
}
```

# 最后

看下瘦身后的效果，比之前减少了 2.5MB。

![image](/images/2019/09/05.png)

# 参考链接

更多瘦身优化的方法：

https://juejin.im/post/59113583ac502e450280e5f3#heading-25


