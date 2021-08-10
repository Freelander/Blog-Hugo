---
title: Android 中东阿拉伯语适配，看这一篇够了
date: 2017-06-06 00:07:52
tags: [Android]
categories: Android
draft: false
---

### RTL 语言由来

RTL 是 Right-to-left(从右向左) 的缩写。其意为人们书写阅读习惯是从右向左，朝左继续的，常见的 RTL 语言有阿拉伯语，希伯来语等。

看一下对比：

LTR | RTL
----|----
![](/images/2017/06/01-2.jpg) | ![](/images/2017/06/01-3.jpg)

那么对于这种从右到左的习惯，在 Android 布局中有没有支持呢？

答案是有的：从 Android 4.2 即 SDK 17 开始，提供了全面的本地布局支持，允许镜像布局，可以同时支持 RTL 和 LTR。

接下来我将介绍如何一步一步适配阿拉伯语。

### 属性

name | desc | chinese
---- | ---- | ---
android:layoutDirection | attribute for setting the direction of a component's layout | 设置组件的布局排列方向
android:textDirection | attribute for setting the direction of a component's text | 设置组件的文字排列方向
android:textAlignment | attribute for setting the alignment of a component's text | 设置文字的对齐方式
getLayoutDirectionFromLocale() | method for getting the Locale-specified direction | 获取指定地区的惯用布局方式

### 前提条件

在 AndroidManifest.xml 文件中 application 节点添加支持从右到左布局方式代码

```
 <application
        ...
        android:supportsRtl="true" >
        ...
</application>
```

### 切换语言

相关链接：[change-language-programmatically-in-android](https://stackoverflow.com/questions/2900023/change-language-programmatically-in-android)  
对应国家语言代码: [what-is-the-list-of-supported-languages-locales-on-android](https://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android)  
下面切换语言方式 updateConfiguration 方法在 Api 25 已经过时, 新的切换方式实例 [地址](https://github.com/DroidKaigi/conference-app-2017/pull/315/files)
```
String languageToLoad  = "ar"; // your language
Resources res = getResources(); 
DisplayMetrics dm = res.getDisplayMetrics();
Locale locale = new Locale(languageToLoad); 
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
res.updateConfiguration(conf, dm); 
```

### AS 一键适配

AS 支持一键适配 RTL，主要是在原来 Layout 中设置 Left 和 Right 属性的补充添加 Start 和 End 属性（你们在写布局的时候是不是很少用到 paddingStart、marginStart？接下来你们写布局的时候可不能再偷懒了，该加的还是得加上)

Start 属性在 LTR 中对应 Left，在 RTL 中对应 Right，在API 17开始支持，为了兼容低版本，需要同时有 Left 和 Start。从市场来看，Android 4.2 系统以下的手机用户已经不多了，我的建议是可以不兼容，具体还得你们看自家产品在 4.2 系统以下用户数。

> Refactor > Add RTL Support Where Possible...

<img src="/images/2017/06/01-1.png" width="516px" height="379px">

### 利用第三方插件

名称 | 描述
------ | ------
[gradle-android-rtl](https://github.com/znyang/gradle-android-rtl) | 自动修复布局文件中未添加RTL支持的标签 

与 AS 插件的比较：

- 性能更好。在处理大批量文件修改时，用AS工具会出现卡顿
- 支持padding标签的补齐

### 使用全局样式

#### EditText

发现 EditText 控件基本都需要设置下面两个属性  
相关链接：[set-a-consistent-style-to-all-edittext-for-e-g](https://stackoverflow.com/questions/10903647/set-a-consistent-style-to-all-edittext-for-e-g
)

```
android:textAlignment="viewStart"
android:gravity="start"
```
那我们就可以在 style.xml 样式中全部 EditText 都设置

```
<style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
       ...
       <item name="editTextStyle">@style/EditTextStyle.Alignment</item>
       ...
</style>

<style name="EditTextStyle.Alignment" parent="@android:style/Widget.EditText">
        <item name="android:textAlignment">viewStart</item>
        <item name="android:gravity">start</item>
        <item name="android:textDirection">locale</item>
</style>
```

#### TextView

全局给所有 TextView 添加一个 RTL 属性  
相关链接:[setting-global-styles-for-views-in-android](https://stackoverflow.com/questions/3078081/setting-global-styles-for-views-in-android)

```
<style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
       ...
       <item name="android:textViewStyle">@style/TextViewStyle.TextDirection</item>
       ...
</style>

<style name="TextViewStyle.TextDirection" parent="android:Widget.TextView">
        <item name="android:textDirection">locale</item>
</style>
```

### 判断是否是 RTL 布局

```
TextUtilsCompat.getLayoutDirectionFromLocale(Locale.getDefault()) == LayoutDirection.RTL
```

### 对集合进行倒序处理

在某些场合下, 这个方法很有用
```
Collections.reverse(List<?> list);
```

### 代码动态设置控件 setMargins

```
FrameLayout.LayoutParams params = new FrameLayout.LayoutParams(
                ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT);
params.setMargins(10, 0, 10, 0);
params.setMarginEnd(10);
```

### ViewPager

相关链接: [tabs-swipe-direction-in-right-to-left-android-app](https://stackoverflow.com/questions/37196714/tabs-swipe-direction-in-right-to-left-android-app)

> Android 官方控件大多支持 RTL，ViewPager 除外，GitHub 上面有人对 ViewPager 进行修改支持 RTL, [地址](https://github.com/diego-gomez-olvera/RtlViewPager)

### WebView

在 Android7.0 以上系统，阿语环境下，第一次打开存在 WebView 页面，会导致页面其他控件镜像失败。

重现的步骤：先将 App 切换成 阿语 --> 完全退出 App --> 打开 App --> 点击一个 web 活动页 --> 页面 toolbar 返回按钮会看到镜像失败

解决方法：``在存在 WebView 的 Activity 切换语言之前加多行代码 new WebView(this).destroy()。``

参考链接：[android-webview-language-changes-abruptly-on-android-7-0-and-above](https://stackoverflow.com/questions/40398528/android-webview-language-changes-abruptly-on-android-7-0-and-above)

### 适配总结

1. 横向布局 LinearLayout ，可以使用 FrameLayout，控件需要靠左或靠右可以使用 layout_gravity 设置对应属性
2. 切换阿拉伯语时，网格布局 item 之间的距离会出现增大问题，处理方法是：网格分割线 ItemDecoration 需要加入语言来判断，调换原来设置左右的边距即可
3. 禁止掉之前的侧滑返回，以免出现冲突
4. 一些方向图标，重新做一个相对方向的放到 mipmap-ldrtl-xxxhdpi 包下
5. 动画翻转, 放在 anim-ldrtl 将对应的动画进行反向处理
6. 布局里如果设置了 paddingLeft、drawableLeft 等等这些属性更改为一个支持 RTL 的属性 paddingStart、drawableStart；但是有些地方可以不加的，例如：购物车上的数量徽章，加了之后感觉怪怪的，所以还是不加了
7. 利用在 AS 右边的预览布局工具中的语言切换工具，切换成阿拉伯语，能实时看到布局的效果图
8. EditText 添加 android:layoutDirection="locale" ，如果外面有 TextInputLayout 的需给它设置 android:textDirection="locale" ，如果输入类型时密码时还需添加一个属性 android:textAlignment="viewStart"
9. TextView 需要加上 android:textAlignment="viewStart 或 viewEnd" 以及 android:textDirection="locale"
10. RecyclerView 网络布局的可以考虑使用 StaggeredGridLayoutManager ，如果数量太多的网格布局，不太建议使用，可能会出现滑动混乱
11. 阿拉伯语目录下的 String.xml 文件, 出现占位符 d% 需要注意改为 %d, 但又并不是所有都改成这样, 目前我发现当代码中使用了 Toast 和 SpannableString 属性的就需要更改为 %d

### 建议计划

1. 从基础类开始入手，判断是否是阿拉伯语，如果是需要将界面设置为从右到左的显示方式
2. 分模块进行适配
3. 复杂的模块，可以放到 layout-ldrtl 包下，单独做一个布局来适配阿拉伯语，例如详情页

### 参考资料

1. https://android-developers.googleblog.com/2013/03/native-rtl-support-in-android-42.html
2. https://medium.com/@zhangqichuan/rtl-support-in-android-898e11f31561
3. https://mobikul.com/just-few-steps-to-make-your-app-rtl-supportable/
4. https://blog.robustastudio.com/featured/android-rtl-support/
5. http://blog.csdn.net/figo0423/article/details/50241363
6. http://blog.csdn.net/wxx614817/article/details/50586388
7. http://jiajixin.cn/2016/10/08/android_adapt_rtl/
8. http://www.apkbus.com/blog-327085-57866.html
9. http://droidyue.com/blog/2014/07/07/support-rtl-in-android/index.html
10. http://chuansong.me/n/920084451521