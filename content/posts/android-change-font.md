---
title: Android 如何快速全局更换字体
date: 2019-07-06 22:58:05
tags: [Android,Font]
categories: Android
draft: false
---


在 Android 8.0（API 26）和 Support 包 26 以上版本提供了一种可将自定义字体声明为 XML 布局中的资源更快更方便的方式，并且该种方式兼容 Android 4.1 版本以上，官方文档介绍 [地址](https://developer.android.google.cn/guide/topics/ui/look-and-feel/fonts-in-xml.html#java)。
 
网上搜索了很多资料，在未有此特性时处理是相当麻烦的，当然也有一些第三方开源库来帮助处理，其中最多人推荐使用的是 [Calligraphy](https://github.com/InflationX/Calligraphy)。现在有了此特性，我们就可以非常容易全局更改 App 字体。

## 如何引入第三方字体包

1. 右键单击资源 res 文件夹，选择 New > Android resource directory；

2. 在资源类型列表中选择 font，然后点击 OK；

![image](/images/2019/07/01.png)

3. 将你的第三方字体包放到添加到刚创建的 font 文件夹；

![image](/images/2019/07/02.png)

4. Android Studio 支持预览字体，你可以双击字体包即可预览

![image](/images/2019/07/03.png)

至此你已成功引入第三方字体包了，接下来介绍如何使用以及一些技巧。

## 如何使用

在 xml 文件中给所需要更换字体的控件增加一个属性 fontFamily

```
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Hello World!"
    android:fontFamily="@font/hind_siliguri_regular" />
```

在代码中动态更改控件的字体

```
Typeface typeface = ResourcesCompat.getFont(context, R.font.hind_siliguri_regular);
// 若是粗体使用 Typeface.BOLD
textView.setTypeface(typeface, Typeface.NORMAL);
```

## 如何全局字体替换更改

在 styles.xml 文件中找到 App 主题增加自定义字体属性；
```
<!-- Base application theme. -->
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">

    ...

    <!--自定义字体-->
    <item name="android:fontFamily">@font/hind_siliguri_regular</item>
</style>
```
然后在 AndroidManifest.xml 文件的程序定义里，应用此主题；

```
<application
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true"
    android:theme="@style/AppTheme">
    
    ...
    
</application>
```

这样就全局更换字体样式了。

## 如何实现不同语言使用不同字体

通过上面方法可以实现了 App 使用第三方字体，可这时候如果产品要求在不同语言下使用不同的字体；

那么如何实现呢？很简单只需要复制一份 styles.xml 文件在对应的语言 values 文件下，更换所对应的字体即可。

1. 右键单击资源 res 文件夹，选择 New > Android resource directory；

2. 在资源类型列表中选择 values，左边边选择 Locale，然后选择你对应的语言，然后点击 OK；

![image](/images/2019/07/04.png)

3. 这时候会看到新建了一个文件夹 values-ar

![image](/images/2019/07/05.png)

4. 复制一份 styles.xml 文件到 values-ar 文件夹下，修改主题下的字体配置;

```
<!-- Base application theme. -->
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
    
    ...
    
    <item name="android:fontFamily">@font/ar_regular</item>
</style>
```

到这里当你切换语言到中东阿拉伯语的时候，就会显示对应字体了。

## 推荐阅读

[Android 中东阿拉伯语适配，看这一篇够了](https://gojun.me/2017/06/06/android-rtl/)







