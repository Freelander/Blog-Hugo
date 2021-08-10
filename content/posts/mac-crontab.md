---
title: Mac 使用 Crontab 定时工具
date: 2018-03-24 22:29:52
tags: [Tools]
categories: Tools
draft: false
---

## 相关命令

1. `sudo /usr/sbin/cron start` (启动)
2. `sudo /usr/sbin/cron restart` (重启)
3. `sudo /usr/sbin/cron stop` (停止)
4. `sudo launchctl list | grep cron` (查看是否启动)
5. `man crontab` (查看crontab命令的用法)
6. `man 5 crontab` (查看crontab定时任务的格式)
7. `sudo crontab -e` (进入编辑器，添加或修改定时任务)
8. `sudo crontab -l` (查看已经添加的定时任务)

## 添加定时任务

``55 23 * * * python3 /Users/hugo/Development/GooglePlayReview/app_reviews.py``

```
$ sudo crontab -e
# 此时会进入 vi 编辑器！注意到，每项工作都是一行。
# 基本格式：* * * * * command 
0 12 * * * mail dmtsai -s "at 12:00" < /home/dmtsai/.bashrc
#分 时 日 月 周 |<==============指令串========================>|
$ sudo crontab -l
# 查看已经添加的定时任务
```

前面五个参数(星号)代表的意义：

意义 | 分钟 | 小时 | 日 | 月 | 星期
-----|-----|------|------|------|----
数字范文 | 0~59 | 0~23 | 1~31 | 1~12 | 0~7（0:周日)

## Crontab 时区与系统不一致
```
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime 

```

## 参考链接

[OSX系统添加定时任务](https://honglu.me/2014/09/20/OSX%E7%B3%BB%E7%BB%9F%E6%B7%BB%E5%8A%A0%E5%AE%9A%E6%97%B6%E4%BB%BB%E5%8A%A1/)
[Mac中的定时任务利器：launchctl](https://www.jianshu.com/p/4addd9b455f2)
[Mac 开启 crontab 定时任务调试](https://blog.csdn.net/biyongyao/article/details/77791238)

