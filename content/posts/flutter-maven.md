---
title: Flutter 自动打包并将 aar 文件上传到 Maven
date: 2020-04-26 22:42:04
tags: [Flutter]
categories: Flutter
draft: false
---

### 思路
 
1. 先执行 ``flutter pub get`` 获取项目最新依赖插件。
2. 对 flutter module 进行打包成 aar，因为是上传到 maven 的，只要 release 版本即可。
3. 对打包出来的 aar 文件，简单粗暴使用 ``mvn deploy`` 指令进行上传到 maven。
4. 上传完之后，提示如何引入到项目。


### 完整构建脚本

创建一个 ``android_build.sh`` 文件

```shell
#!/usr/bin/env bash

###############################################################
# 脚本是 shell 文件，只适用于 mac 系统
# 执行脚本时，版本号为必填参数
# 在项目路径下执行命令：./android_build.sh 版本号
# 若出现提示 `zsh: command not found: android_build.sh`
# 解决方法：
# 1. vim ~/.zshrc
# 2. 找到 “# User configuration” 在其位置下面加 `source ~/.bash_profile`
# 3. 保存退出
# 4. source ~/.zshrc
###############################################################

# 在执行出错时结束程序
set -e

# 读取版本号
version=$1

###### 1. 检查参数
if [ ! -n  "$version" ]; then

echo ">>> 版本号参数不能为空!"

# 结束脚本运行
exit
fi

###### 2. 获取/更新包

echo ">>> 执行 flutter pub get!"

flutter pub get

###### 3. 构建依赖项目的AAR

echo ">>> 执行构建 ${version} release 版本 aar !"

flutter build aar --no-debug --no-profile --build-number ${version}

echo ">>> 构建成功"

###### 4. 将文件上传 maven

echo ">>> 执行上传 Maven"

# 打包出来的 aar 文件存放路径
file_path="build/host/outputs/repo/com/chicdeals/flutter/flutter_release/$version/flutter_release-$version"

mvn deploy:deploy-file \
-s="mvn-settings.xml" \
-DpomFile="$file_path.pom" \
-DgeneratePom=true \
-Dfile="$file_path.aar" \
-Durl="https://oss.gojun.me/repository/flutter-release" \
-DrepositoryId="com.chicdeals.flutter" \
-DgroupId="com.chicdeals.flutter" \
-Dpackaging=aar

echo ">>> 上传成功"

###### 5. 提示如何引入

echo ">>> 项目接入地址："

echo ">>> implementation 'com.chicdeals.flutter:flutter_release:$version'"

```

与 ``android_build.sh`` 文件同目录下创建 maven 账号密码配置文件 ``mvn-settings.xml``。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
          
  <pluginGroups></pluginGroups>

  <proxies></proxies>

  <servers>
    <!-- server
     | Specifies the authentication information to use when connecting to a particular server, identified by
     | a unique name within the system (referred to by the 'id' attribute below).
     |
     | NOTE: You should either specify username/password OR privateKey/passphrase, since these pairings are
     |       used together.
     |
     -->
    <server>
      <id>com.chicdeals.flutter</id>
      <username>Maven账号</username>
      <password>Maven密码</password>
    </server>
    
  </servers>

  </profiles>

</settings>

```

### 解释

上面脚本的注释已经做了比较详细的解释了，在这里对几个比较重点的地方再做一遍解释说明。

1. 执行命令是 ``./android_build.sh 版本号`` 版本号为必传参数。
2. 执行 mvn 命令的前提需要 Mac 系统安装 Maven，安装方法建议通过 ``brew install maven`` 安装。
3. Maven 账号密码配置文件在 ``mvn-settings.xml``，其中文件配置的 ``id`` 需要与 ``DgroupId`` 和 ``DrepositoryId`` 保持一致。


### 拓展

1. 如何验证安装成功 Maven，执行以下命令：

```
$ mvn -version
```

成功时，输出的日志如下：

```
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: /usr/local/maven/apache-maven-3.6.3
Java version: 1.8.0_172, vendor: Oracle Corporation, runtime: /Library/Java/JavaVirtualMachines/jdk1.8.0_172.jdk/Contents/Home/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "mac os x", version: "10.15.3", arch: "x86_64", family: "mac"
```

2. 想查看详细 Maven 上传日志，在命令行加多 ``--debug`` 即可。

```
mvn --debug deploy:deploy-file \
-s="mvn-settings.xml" \
-DpomFile="build/host/outputs/repo/com/chicdeals/flutter/flutter_release/1.0/flutter_release-1.0.pom" \
-DgeneratePom=true \
-Dfile="build/host/outputs/repo/com/chicdeals/flutter/flutter_release/1.0/flutter_release-1.0.aar" \
-Durl="https://oss.gojun.me/repository/flutter-release" \
-DrepositoryId="com.chicdeals.flutter" \
-DgroupId="com.chicdeals.flutter" \
-Dpackaging=aar
```

### 参考资料

* [Flutter 自动打包依赖aar上传Maven仓库( Shell+Gradle 脚本)](https://blog.csdn.net/qizewei123/article/details/102768038)
* [Flutter混编一键打包并上传maven](https://galaxybruce.github.io/flutter/Flutter%E6%B7%B7%E7%BC%96%E4%B8%80%E9%94%AE%E6%89%93%E5%8C%85%E5%B9%B6%E4%B8%8A%E4%BC%A0maven.html)
* [Flutter 1.12后 上传aar至maven私服](https://kikt.top/posts/flutter/exists/upload-aar-to-maven/)

