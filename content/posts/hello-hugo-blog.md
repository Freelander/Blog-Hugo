---
title: "这可能是我最喜欢的博客主题了"
date: 2021-07-18T14:13:41+08:00
tags: [Blog]
categories: Blog
draft: false
---

之前博客用到的是 Hexo 框架搭建，感兴趣的同学可以到我 [欢迎来到我的新博客](https://gojun.me/posts/hello-blog/) 这篇文章看看。

在一次偶然的机会我在逛 GitHub 的时候，发现了一款我一看到就喜欢上的博客主题，无论字体字号、代码风格、排版、浏览大图等等我都很喜欢，
果断将自己目前的博客主题换成它这个，话不多说奉上我喜欢上的博客主题 GitHub 地址：[hugo-theme-zozo](https://github.com/varkai/hugo-theme-zozo)。

![img](/images/2021/07/hello-hugo-blog-01.png)

由于该主题是使用 Hugo 框架搭建的，因此我也要从原来 Hexo 框架切换过来，初步评估了下切换过来并不麻烦，于是就开始用 Hugo 框架来搭建博客之路了。

### 安装 Hugo

Hugo 是由 Go 语言实现的静态网站生成器。简单、易用、高效、易扩展、快速部署。

项目地址：[https://github.com/gohugoio/hugo](https://github.com/gohugoio/hugo)

Mac 安装方法：

```
brew install hugo
```

更多终端安装方法，请到 Hugo 官方文档查看 [Install Hugo](https://gohugo.io/getting-started/installing/)

### 创建站点

即在你指定路径下，创建存放博客的文件夹，执行的命令：

```shell
hugo new site My-Blog-Hugo
```

站点目录结构

```
▸ archetypes/
▸ content/
▸ layouts/
▸ static/
▸ data/
▸ themes/
  config.toml
```

其中 ``config.toml`` 是配置文件，``themes`` 是存放博客主题目录，``content`` 是存放博客文章及本地图片目录，其他目录作用还有待挖掘。

### 安装喜欢的主题

终于到要安装我喜欢的主题环节了，迫不及待按照 [README](https://github.com/varkai/hugo-theme-zozo) 文件的指导安装。

进入到刚创建的 ``My-Blog-Hugo`` 目录下，将主题克隆下来：

```shell
git clone https://github.com/varkai/hugo-theme-zozo themes/zozo
```

### 创建第一篇文章

```shell
hugo new posts/hello-blog.md
```

执行命令之后，会在 ``content`` 目录下新创建 ``posts`` 目录，专门存放文章的文件。

打开 ``hello-blog.md`` 文件

```md
---
title: "Hello Blog"
date: 2021-08-08T15:10:30+08:00
draft: true
---
```
发现日期格式跟之前 hexo 框架的 ``2019-10-20 16:37:26`` 不太一样了，有可能不兼容后面再看看是否需要调整，
另外发现 ``draft`` 参数，注意了这个参数，若是要将写好的文章进行发布，值需要改为 ``false``。

### 迁移博客

安装完主题并按文档配置好相关信息后，就要开始从原来文件夹里的文章拷贝到 ``posts`` 目录下，并新增 ``draft`` 参数到文件配置头，值设置为 ``false``。

还有之前本地存放的图片资源，在 content 目录下新建 images 目录全部拷贝过来。

### 本地预览

```
hugo server --port=3000 -D
```

执行完命令之后，浏览器输入 [http://localhost:3000](http://localhost:3000) 即可预览了，预览中发现有什么修改地方，本地文件修改完后，回到浏览器会自动刷新。

### 构建生成网站内容

```shell
hugo -d public
```

执行完命令之后，会在当前站点目录下，生成一个 ``public`` 文件夹，里面就是站点的文件。

### 关联 Git 并提交

进入到 ``public`` 文件夹下，关联上我的博客 git 仓库，

注意：这里操作由于生成文件与远程仓库可能会有冲突，建议操作步骤是 

本地先创建 ``public`` 文件夹，关联上远程仓库，拉去代码回来，将文件全部删除提交 commit 后 push 上去，然后再执行 hugo -d public 生成网站内容命令，之后再将新生成的内容提交到远程仓库。

```shell
# 初始化 Git
git init

# 关联远程仓库
git remote add origin git@github.com:Freelander/freelander.github.io.git

# 拉取代码回来将文件删除又 push 上去

... 省略 git 操作命令

# 再执行 hugo 生成网站内容命令
hugo -d public

# 查看当前修改状态
git status 

# 添加所有修改过的文件。你也可以只添加某个文件。
git add .  

# commit 信息
git commit -m "New blog style"

# 提交代码
git push origin master
```

### 给博客增加图片浏览功能

这里利用了 [fancybox](https://github.com/fancyapps/fancybox) 来实现图片浏览大图，克隆的主题里已有了 fancybox 的 js 及 css 文件，就是还需要另外配置下才能支持

1. 在根目录下的 ``config.toml`` 配置文件里 ``[param]`` 下追加参数 ``fancybox = true``；
2. 在 ``themes/zozo/layouts/_default/_markup/`` 路径下现金一个文件 ``render-image.html``；
3. 贴上文件内容：

```html
{{if .Page.Site.Params.fancybox }}

<div class="fancybox">
<a data-fancybox="gallery" href="{{ .Destination | safeURL }}">
<img src="{{ .Destination | safeURL }}" alt="{{ .Text }}" {{ with .Title}} title="{{ . }}"{{ end }} />
</a>
</div>

{{ end }}
```

### 最后奉上效果图

![img](/images/2021/07/hello-hugo-blog-02.png)

![img](/images/2021/07/hello-hugo-blog-03.png)

### 参考链接

* [如何利用 GitHub Pages 和 Hugo 轻松搭建个人博客？](https://zhuanlan.zhihu.com/p/57361697)
* [Hugo-Mainroad主題修改流程](https://www.blog.off-record.net/20/12/hugo-mainroad-custom/)
* [使用hugo搭建博客](https://www.lishuai.fun/2020/09/01/hugo-build-blog/)




