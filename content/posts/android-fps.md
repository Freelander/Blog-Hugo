---
title: 面试官问我：如何对 Android  App 进行 fps 分析
date: 2021-01-06 21:58:23
tags: [Android]
draft: false
---

### 背景

界面呈现是指从应用生成帧并将其显示在屏幕上的动作。要确保用户能够流畅地与您的应用互动，您的应用呈现每帧的时间不应超过 16ms，以达到每秒 60 帧的呈现速度。

> 结论动画控制在每帧耗时 16ms 以下，因此需要进行 FPS 分析。

### 前提

进行分析前，手机先开启 ``GPU呈现模式分析``。

> 打开手机“设置”-“更多设置”-“开发者选项”-“GPU 呈现模式分析”，选中“在adb shell dumpsys gfinfo”中

设置路径基本都一样，都是在开发者选项里，部分手机是叫做 “HWUI 呈现模式分析”，下图是小米手机截图。

![image](/images/2021/01/android_fps_01.png)

### 打开测试的界面

先打开你要进行分析的界面，然后再终端输入以下命令。

### 命令

利用 [dumpsys](https://developer.android.com/studio/command-line/dumpsys?hl=zh-cn#ui) 获取当前指定 App 发生的动画帧相关的性能信息，并以 txt 文件保存下来。

```
adb shell dumpsys gfxinfo package-name > loading.txt 
```

#### 遇到问题

执行命令出现错误

```
adb E  1250 22834 usb_osx.cpp:327] Could not open interface: e00002c5
adb E  1250 22834 usb_osx.cpp:289] Could not find device interface
error: could not install *smartsocket* listener: Address already in use
ADB server didn't ACK
* failed to start daemon *
error: cannot connect to daemon
```

> 解决：打开 Mac 活动监视器在 内存 栏，找到 adb 进程，对它进行强制退出


### 导入 Excel

#### 第一步导入文本

![image](/images/2021/01/android_fps_02.png)

#### 第二步完成导入

这一步直接点击 **完成** 即可，然后点击 **确定** 。

![image](/images/2021/01/android_fps_03.png)

![image](/images/2021/01/android_fps_04.png)

#### 第三步复制耗帧数据

找到对应需要分析的界面类的数据，比如我这边的：

![image](/images/2021/01/android_fps_05.png)

将这块数据复制到新建一份 **工作表**。

![image](/images/2021/01/android_fps_06.png)

### 折线图分析

在新建的工作表最后新增一栏命名为 **每秒耗帧（毫秒）**

进行自动求和操作，选中新增栏的标题下一格，然后点击 Excel 表中的 **开始 》 自动求和**，这时会出现计算公式，直接按下回车键即计算出当前行的值总和，接着往下拉会自动进行每行的求和操作。

对  **每秒耗帧（毫秒）** 栏进行图表分析：

![image](/images/2021/01/android_fps_07.png)

接着就会自动生成一份图表：

![image](/images/2021/01/android_fps_08.png)

> 分析图表的趋势，如果每帧耗时在 **16ms** 以上，那么即需要优化。

### 相关链接

* [渲染速度缓慢](https://developer.android.com/topic/performance/vitals/render.html)
* [分析 GPU 渲染速度](https://developer.android.com/topic/performance/rendering/inspect-gpu-rendering#profile_rendering)
* [面试官问我：Android APP中如何测试FPS？看我如何分析京东，拼多多App的FPS](https://juejin.im/post/6844904161746616334)
* [Lottie 动画预览](https://lottiefiles.com/)
* [dumpsys](https://developer.android.com/studio/command-line/dumpsys?hl=zh-cn)
* [测试界面性能](https://developer.android.com/training/testing/performance?hl=zh-cn)
