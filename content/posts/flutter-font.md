---
title: Flutter 如何使用自定义字体
date: 2020-02-01 20:52:25
tags: [Flutter]
categories: Flutter
draft: false
---

### 1. 导入字体包 

在项目路径新创建 ``fonts`` 文件夹，里面存放自定义字体包。

> 注意这个文件夹的路径需要放到主目录下，如果在 ``asstes`` 文件夹下创建在 iOS 那边打出来的产物会出现无法应用到自定义字体的问题。

![image](/images/2020/02/flutter_fonts_01.png)

### 2. 在 pubspec.yaml 中声明字体

```
flutter:
  fonts:
   - family: Hind Siliguri
     fonts:
       - asset: assets/fonts/Hind-Siliguri-Bold.ttf
       - asset: assets/fonts/Hind-Siliguri-Regular.ttf
```

``family`` 属性决定了字体的名称，将会在 ``TextStyle`` 的 ``fontFamily`` 属性中用到。

### 3. 全局设置默认字体

在主入口 ``main.dart`` 文件配置：

```
runApp(MaterialApp(
    theme: ThemeData(
      fontFamily: 'Hind Siliguri',
      ...
    ),
));
```

设置的 ``fontFamily`` 的值必须与 ``pubspec.yaml``  中声明的名称相匹配。

### 4. 抽取封装通用的字体样式（可选）

```
import 'package:flutter/material.dart';

class Styles {

  static TextStyle boldTextStyle(Color color, double fontSize, {Color backgroundColor, double lineHeight}) {
    return TextStyle(
      fontSize: fontSize,
      color: color,
      fontWeight: FontWeight.bold,
      backgroundColor: backgroundColor,
      height: lineHeight,
    );
  }

  static TextStyle regularTextStyle(Color color, double fontSize, {Color backgroundColor, double lineHeight}) {
    return TextStyle(
      fontSize: fontSize,
      color: color,
      backgroundColor: backgroundColor,
      height: lineHeight,
    );
  }
}

```
### 5. 使用

```
Text(
      'Contact Us',
      style: Styles.boldTextStyle(Colour.color_333333, 18),
    ),
```

### 6. 效果

![image](/images/2020/02/flutter_fonts_02.png)

### 资料

[官方文档-使用自定义字体](https://flutter.dev/docs/cookbook/design/fonts)


