---
title: 欢迎来到我的新博客
date: 2019-06-12 00:07:52
tags: [Blog]
categories: Blog
draft: false
---

## 教程

1. [手把手教你使用Hexo + Github Pages搭建个人独立博客](https://segmentfault.com/a/1190000004947261#articleHeader1)
2. [使用Hexo在Github上搭建专属博客之技术总结](https://www.jianshu.com/p/42aa637f614a)
3. [主题配置](http://forsigner.com/2016/03/10/fexo-doc-zh-cn/#%E5%BC%80%E5%A7%8B)

## 遇到问题

```
FATAL Port 4000 has been used. Try other port instead.
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
```
解决方案：

```
sudo hexo s -p 5000
```

## Hexo 命令

1. ``hexo generate (hexo g)`` 生成静态文件，会在当前目录下生成一个新的叫做public的文件夹
2. ``hexo server (hexo s)`` 启动本地web服务，用于博客的预览
3. ``hexo deploy (hexo d)`` 部署播客到远端（比如github, heroku等平台）
4. ``hexo new "postName"`` #新建文章
5. ``hexo new page "pageName"`` #新建页面

```
$ hexo n == hexo new
$ hexo g == hexo generate
$ hexo s == hexo server
$ hexo d == hexo deploy
```

## 如何关联上 GitHub

1. Install hexo-deployer-git.

```
$ npm install hexo-deployer-git --save
```

2. Edit ``_config.yml`` (with example values shown below as comments):

```
deploy:
  type: git   
  repo: <repository url>  #https://bitbucket.org/JohnSmith/johnsmith.bitbucket.io
  branch: [branch] #published
  message: [message]  #leave this blank
```

## 绑定个人域名

注册域名地址推荐：[namesilo](https://www.namesilo.com)

然后在 GitHub 你的博客仓库 Setting 页面配置域名

![image](/images/2019/06/01.png)

保存过几分钟就可以访问。

### Hexo 主题模板

https://hexo.io/themes/

## 看中的博客模版

1. https://fuzhouxxdong.github.io/hexo-theme-dxx/
2. http://forsigner.com/
3. https://blog.devzeng.com/
4. https://paugram.com/
5. http://qiubaiying.top/





