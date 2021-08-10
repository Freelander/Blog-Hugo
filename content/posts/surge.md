---
title: 带你玩转 Surge 网络调试神器
date: 2017-12-12 00:25:13
tags: [翻墙,Surge,VPN]
categories: VPN
draft: false
---

### 简单了解

Surge 是基于 iOS 9 新增的“网络扩展”（Network Extension）API[3] 开发的一款网络调试工具。作为首个借用此软件可使用 Shadowsocks 的 iOS 9 应用，中国大陆网友将其广泛运用于突破网络审查。

Surge 可做很多事情，比如阻挡广告、网络调试、流量跟踪、代理上网等等。

### 安装

Mac 版下载 [地址](https://nssurge.com/)（Windows 暂不支持），下载完成后解压文件夹得到 Surge 程序，将其拖放到应用程序中打开。

启动 Surge 后勾选下拉菜单中的 **Set as System Proxy** 即可自动向系统网络设置添加必要的参数，因为需要修改系统网络设置，首次勾选时需要输入管理员密码进行确认，去掉 Set as System Proxy 的勾选，会清除网络设置中的代理相关设置。

打开「系统偏好设置 — 网络 - Wi-Fi高级」设置 HTTP 和 HTTPS 代理： 127.0.0.1:6152 ，以及 SOCKS 代理中设置为 127.0.0.1:6153，
并添加忽略这些主机与域的代理设置（Bypass Proxy）：127.0.0.1, 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12, localhost, *.local

![](/images/2017/12/02-1.png)

### 配置

Surge 默认配置文件的路径在 /Users/Freelander/Documents/Surge 下 Default.conf，文件夹还提供简单中文配置例子，里面有很多注释教你如何配置。

配置文件基础最终我选择了在一份 [开源配置文件](https://raw.githubusercontent.com/lhie1/Surge/master/Surge.conf) 基础上进行修改。已上传到 [GitHub](https://github.com/Freelander/Blog/blob/master/201708/code/MySurge.conf)。

将其内容复制到上面所说的路径 Default.conf 文件中，然后保存点击下拉菜单中的 Reload Profile from File，此时配置文件中的配置就生效了。

首先来看如何配置 ss 代理

```
[Proxy]
 🇺🇸US-l = custom,${ss-server host},${ss-server port},${ss encrypt type},${ss-password},tls=true
 🇺🇸US-m = custom,${ss-server host},${ss-server port},${ss encrypt type},${ss-password},tls=true
 🇯🇵JP  = custom,${ss-server host},${ss-server port},${ss encrypt type},${ss-password},tls=true
 🇸🇬SG  = custom,${ss-server host},${ss-server port},${ss encrypt type},${ss-password},tls=true
 🇩🇪DE  = custom,${ss-server host},${ss-server port},${ss encrypt type},${ss-password},tls=true
   
[Proxy Group]
Proxy = select,🇺🇸US-l,🇺🇸US-m,🇯🇵 JP,🇸🇬SG,🇩🇪DE
```
[Proxy]是具体的代理配置，它下面就是 5 个 ss 服务器节点, 这里只是 5 个国家例子，根据你拥有的 ss 账号数来决定，拥有几个账号就留下其中几个对应国旗配置就好。表面上看就是 5 个键值对(键可以自定义)，键中加上国旗可以很直观的区分不同国家的 ss 节点，值中的变量分别表示：
ss-server host: ss 主机，ip 或域名
ss-server port: ss 主机端口
ss encrypt type: 加密方式
ss-password: ss 密码
custom 不能改，是一个约定类型(ss 协议)。

保存上面配置，点击下拉菜单中的 Reload Profile from File，配置生效，就会出现下图

![](/images/2017/12/02-2.png)

接着打开浏览器输入 https://www.youtube.com/ 回车
你会发现外面的世界如此精彩

### 共享上网

在局域网内，其他设备无须再安装任何软件，即可与我一起到外面世界遨游。其他设备只需要简单的在 wifi 设置中添加一个 HTTP 代理即可

图片来源 [地址](https://medium.com/@scomper/%E5%B1%80%E5%9F%9F%E7%BD%91%E5%85%B6%E4%BB%96%E8%AE%BE%E5%A4%87%E5%85%B1%E4%BA%AB%E4%B8%8A%E7%BD%91-dd29e18853da)

![](/images/2017/12/02-3.png)

### 同时使用 Surge + Charles 分析网络

打开 Charles 的 External Proxy Settings 设置 Charles 的外部代理为 Surge 的端口，即与上面系统网络设置的代理一致

![](/images/2017/12/02-4.png)

### 语法

```
// 基于域名判断并屏蔽（REJECT）请求  
DOMAIN,pingma.qq.com,REJECT  
// 基于域名后缀判断屏蔽（REJECT）请求  
DOMAIN-SUFFIX,flurry.com,REJECT  
// 基于关键词后缀判断走代理（Proxy），强制不尊重系统代理的请求走
Packet-Tunnel-Provider DOMAIN-KEYWORD,google,Proxy,force-remote-dns  
// 基于域名后缀判断请求走直连（DIRECT）  
DOMAIN-SUFFIX,126.net,DIRECT  
// Telegram.app 指定“no-resolve”Surge 忽略这个规则与域的请求。  
IP-CIDR,91.108.56.0/22,Proxy,no-resolve  
// 判断是否是局域网，如果是，走直连  
IP-CIDR,192.168.0.0/16,DIRECT  
// 判断服务器所在地，如果是国内，走直连  
GEOIP,CN,DIRECT  
// 其他的全部走代理  
FINAL,Proxy  
// 其他的全部不走代理  
FINAL,DIRECT  
```

### 注意坑

写完 [Rule] 后面需要加
```
[Rule] 

....

GEOIP,CN,DIRECT
FINAL,Proxy
```

### 参考链接
* [Surge 原理与实现](https://medium.com/@Blankwonder/surge-%E5%8E%9F%E7%90%86%E4%B8%8E%E5%AE%9E%E7%8E%B0-8aa3304fb3bb)
* [Surge 新手使用指南](https://medium.com/@scomper/surge-%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6-a1533c10e80b)
* [Surge 官方配置教程](https://manual.nssurge.com/overview/configuration.html)
* [Surge for Mac 2 初体验与教程](http://cloudstone.xin/2016/10/31/Mac-Surge-2-%E5%88%9D%E4%BD%93%E9%AA%8C/)
* [Surge config](https://github.com/lhie1/Surge)
* [Surge for Mac 简明指南](https://medium.com/@scomper/surge-for-mac-%E7%AE%80%E6%98%8E%E6%8C%87%E5%8D%97-f6f357b8f09c)
* [局域网其他设备共享上网](https://medium.com/@scomper/%E5%B1%80%E5%9F%9F%E7%BD%91%E5%85%B6%E4%BB%96%E8%AE%BE%E5%A4%87%E5%85%B1%E4%BA%AB%E4%B8%8A%E7%BD%91-dd29e18853da)
* [解决Surge for Mac+Charles同时分析网络请求的办法 ](https://github.com/wujunchuan/wujunchuan.github.io/issues/1)