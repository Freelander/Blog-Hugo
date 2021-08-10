---
title: Mac 如何让终端使用代理
date: 2020-05-10 23:23:29
tags: [工具,终端,代理,翻墙,VPN]
categories: VPN
draft: false
---

### 前提

iTerm2 终端使用的是 zsh 命令，翻墙代理工具使用 Surge2。

### 步骤


1. 在 surge2 复制终端代理命令

![image](/images/2020/05/mac-terminal-proxy-01.png)

得到：
```
export https_proxy=http://127.0.0.1:1080;export http_proxy=http://127.0.0.1:1080;export all_proxy=socks5://127.0.0.1:1081
```

2. 编辑 .zshrc 文件
```
vim ~/.zshrc
```

3. 在文件最后添加以下信息

```
# proxy list
alias proxy='export https_proxy=http://127.0.0.1:1080;export http_proxy=http://127.0.0.1:1080;export all_proxy=socks5://127.0.0.1:1081'
alias unproxy='unset all_proxy'
```

4. 保存退出，应用文件修改内容

```
source ~/.zshrc
```

5. 开启使用代理模式

```
proxy
```

6. 确定终端是否走代理

```
curl cip.cc
```

6. 关闭使用代理模式

```
unproxy
```
