---
title: Flutter 如何实现国际化多语言
date: 2020-02-18 21:22:51
tags: [Flutter]
categories: Flutter
draft: false
---

关于 Flutter 国际化实现方案，网上查看了很多资料，主要有两种。
 
1. 基于 intl package 的国际化实现（官方推荐）
2. 创建支持语种的 Json 文件，存储及读取里面的语言

> 本文介绍的是第一种实现方案，毕竟都是官方推荐使用的。

### 前言

采用第一种方案，不得不提的一个插件 [Flutter Intl](https://plugins.jetbrains.com/plugin/13666-flutter-intl/)，安装使用该插件之后实现国际化非常简单。

* 该插件更新维护频率高，也是目前大部分人使用的。
* 并且支持 Android Studio 和 VS Code 开发工具

下面是以 Android Studio 为例，详细讲解使用过程：

### 1.安装插件

在 Preferences > Plugins > Marketplace > 搜索 ``Flutter Intl`` > 安装完重启 AS

![image](/images/2020/02/flutter_language_01.png)

### 2. 初始化项目 Intl

![image](/images/2020/02/flutter_language_02.png)

完成上面操作之后会自动生成以下文件目录：

![image](/images/2020/02/flutter_language_03.png)

* generated 是自动生成的 dart 代码。
* I10n 是对应的 arb 文件目录，项目的文案都是在这里新增。

在 ``pubspec.yaml`` 文件底部也会新增配置：

```
flutter_intl:
  enabled: true
```

### 3. 项目增加国际化支持

在项目的 ``pubspec.yaml`` 下添加依赖 ``flutter_localizations``

```dart
dependencies:
  flutter:
    sdk: flutter
  flutter_localizations:
    sdk: flutter
```

添加完之后执行下命令 ``flutter pub get``，回到 ``main.dart`` 文件配置项目支持的语种

```dart
runApp(MaterialApp(
    localizationsDelegates: [
      S.delegate,
      GlobalMaterialLocalizations.delegate,
      GlobalWidgetsLocalizations.delegate,
      GlobalCupertinoLocalizations.delegate,
    ],
    // 支持语种
    supportedLocales: S.delegate.supportedLocales,
    // 设置当前项目的语言
    locale: Local('en', 'GB'),
));
```

在主入口前初始化框架当前语言：

```dart
S.load(Locale('en', 'GB'));
```

### 4. 如何使用

在刚通过插件生成的文件 ``intl_en.arb`` 下，新增文案：

```
{
  "contactUs": "ContactUs"
}
```

添加完之后 ``command+s`` 保存，插件就会执行命令，在 ``I10n.dart`` 和 ``messages_en.dart`` 文件下生成对应的代码。

I10n.dart:

```
class S {
  ...

  /// `ContactUs`
  String get contactUs {
    return Intl.message(
      'ContactUs',
      name: 'contactUs',
      desc: '',
      args: [],
    );
  }
}
```

messages_en.dart:

```
class MessageLookup extends MessageLookupByLibrary {
  String get localeName => 'en';

  final messages = _notInlinedMessages(_notInlinedMessages);
  static _notInlinedMessages(_) => <String, Function> {
    "contactUs" : MessageLookupByLibrary.simpleMessage("ContactUs")
  };
}
```

**在代码中使用新增文案**

```
S.of(context).contactUs
```

### 5. 如何新增语种

>点击 Tools > Flutter Intl > Add Locale

![image](/images/2020/02/flutter_language_04.png)

新增 中东阿拉伯语：

![image](/images/2020/02/flutter_language_05.png)

点击 OK 之后，插件会新创建两个文件：

![image](/images/2020/02/flutter_language_06.png)

然后在新创建的 ``intl_ar.arb`` 文件，增加对应的语种即可：

```
{
  "contactUs": "اتصل بنا"
}
```

### 6. Arb 文件更多语法使用

占位符：

```
{
    "hello": "Hello {name}"
}
```

使用：

```
S.of(contxt).hello("GoJun");
```


### 资料

* [Flutter国际化的三种方式-->json](https://www.jianshu.com/p/1960d34e54ae)
* [官方文档 Flutter 应用国际化](https://flutter.dev/docs/development/accessibility-and-localization/internationalization)
* [Fluter Intl 插件](https://plugins.jetbrains.com/plugin/13666-flutter-intl/)