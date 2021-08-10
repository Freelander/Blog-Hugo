---
title: 使用 Python3 对在 Play 商店应用的评论爬取调研
date: 2018-03-02 00:06:26
tags: [爬虫, Python]
categories: Python
draft: false
---

## 背景/需求

## 背景/需求

方便团队方便查看每天收到来自 Play 商店用户的评论，及时从用户的反馈中发现问题，故写个脚本每天定时爬取评论数据，并且进行统计，爬取到的数据存进数据，后面提供接口给前端开发页面进行展示。

两个数据表：
1. 评论明细表（评论时间，评论内容，设备信息等）
2. 评论汇总统计(每天有多少1星，多少2星等)

## 调研

### 方式一

使用官方 Api 接口进行抓取，经查看文档，文档提到接口请求得到的数据不是实时的，获取到的数据不是最新，延迟了两天数据，没法拿到最新一天的评论因此放弃该方式。

相关链接：  
https://developers.google.com/android-publisher/api-ref/reviews/list  
https://stackoverflow.com/questions/11348025/api-to-get-android-google-play-reviewsgetting-device-name-and-app-version

### 方式二

网上查找下有没有 demo，在 GitHub 上面找到一个例子。

相关链接：https://github.com/amrrs/Google-Play-Store-Review-Extractor

查看源码不符合需求。

### 方式三

试图从google应用商店入手，发现商店上面的评论并不是实时的，而且貌似是会根据访问的 ip 是什么来返回评论数据，并且提供的筛选功能不能满足我们的需求。

### 方式四

从应用后台入手，只有后台的评论数据才是实时的，并具备满足我们要求的筛选，以及统计数据。可以使用 Python 中的无头浏览器进行爬取。


## 最后

使用``方式四``完成需求

大概步骤：

1. 设置代理
2. 模拟登陆 Google 发布应用后台
3. 进入评价模块
4. 开始抓取评论数据
5. 解析评论数据
6. 数据进库

