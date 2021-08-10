---
title: 如何利用 Google Cloud 搭建 VPS
date: 2018-06-06 00:25:23
tags: [翻墙,Google Cloud,VPN]
categories: VPN
draft: false
---

### Google Cloud 官网

https://cloud.google.com/

### 活动

现在申请 Google Cloud 赠送免费使用 300美元一年的有效期，使⽤用最低配的话，可以玩一年，每个⽉有 86G 的流量，个人使⽤足够了吧......

### 准备

- 一张 VISA信⽤用卡
- Google 账户
- 由于 Google 在中国是不存在的，所以先要有代理
- 下载 ShadowsocksX-NG，[地址](https://github.com/shadowsocks/ShadowsocksX-NG)

### 开始申请

首先需要登录你的 Google 账号，如果没有的话，自己去创建一个，这就不多说了，登录成功之后，进入到 Google Cloud [官网](https://cloud.google.com/)

此时你会看到右上角有个 免费试用 的入口，点击进去

![](/images/2018/06/01-01.png)

进去之后选择国家为 美国，为什么不选中国呢？因为没有中国可选；同意并继续

![](/images/2018/06/01-2.png)

接下来就是下面的界面了，账号选择个人，填写好注册信息，个人信息地址可以虚拟一个美国身份，推荐随机生成美国身份网站 [地址](http://www.haoweichi.com/Index/random)；

填写你的一张 VISA 信用卡，信用卡会扣除 1 美元，这不用担心只是验证你这张信用卡是否可用，过一会就会退还给你

![](/images/2018/06/01-3.png)

信息填完后，点击开始免费试用，至此你已成功创建了 Google cloud 账号，并且获得免费赠送的 300 美元

### 开始部署

**1. 创建一个项目**

完成上面步骤之后，进入到大概下面的界面，点击箭头位置开始创建一个项目

![](/images/2018/06/01-4.png)

进入到这个界面，项目名称随便你起，位置不用关，直接点创建

![](/images/2018/06/01-5.png)

**2. 设定防火墙**

入口：菜单依次点击【网络】--【防火墙规则】--【创建防火墙规则】

![](/images/2018/06/01-6.png)

先来看下我的配置

![](/images/2018/06/01-7.png)

主要修改点：

- 流量方向：入站  
- 对匹配项执行的操作：允许  
- 目标：网络中所有实例  
- 来源IP地址范围：0.0.0.0/0  
- 协议和端口：全部允许  
- 其他默认即可

**3. 申请静态IP**

这个步骤可有可⽆，如果是拿来做 SS 服务器，最好还是申请一个，不然的话每次重启ip地址都会变化，客户端又得重新配置了

菜单依次点击【网络】--【外部IP地址】--【保留静态IP】

![](/images/2018/06/01-8.png)

据说区域选择亚洲东部，国内访问会比较快，这个我不知道没有验证，反正我选择了美国

> 注意：静态 IP 只能申请一个

### 创建计算引擎

菜单一次点击【计算】--【Compute Engine】--【VM 实例】

![](/images/2018/06/01-9.png)

![](/images/2018/06/01-10.png)

![](/images/2018/06/01-11.png)

主要修改点：

- 机器类型：微型(一个共享 vCPU) （右边会有每个月的金额估算值，根据需要吧，选这个是5美元/月，能用一年！）
- 选择启动磁盘（系统），我选择的是 Debian
- 访问权限范围：允许默认访问权限
-（打开管理、磁盘、网络、ssh 密钥选项）选择【网络】
网络--外部IP选择临时

至此，Google cloud 服务器已经配置完成，可以像正常使用 VPS 一样操作了。

### 搭建 SS

使用 Google Cloud 自带的浏览器 SSH，推荐使用！

![](/images/2018/06/01-12.png)

1. 先输入 ``sudo -i`` 命令进入到 root 账户
2. 搭建 Shadow-socks，推荐秋水的一键go版本，输入下面命令

```
wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-go.sh
chmod +x shadowsocks-go.sh
./shadowsocks-go.sh 2>&1 | tee shadowsocks-go.log
```

输入命令后，一路回车 Enter 就 Ok，当然这个过程你可以自己定义 端口、密码、加密方式这些信息，执行完之后就会得到如下面信息

![](/images/2018/06/01-13.png)

> 该代理已经失效~~

### SS 服务相关命令

Shadowsocks-Go 版（install | uninstall | start | stop | restart | status）

```
# 使用 root 用户登录，运行安装以及卸载的命令：
# 进入 root 用户
sudo -i
# 安装
./shadowsocks-go.sh install
# 卸载
./shadowsocks-go.sh uninstall

# 安装完成后
# 重启
/etc/init.d/shadowsocks restart
# 停止
/etc/init.d/shadowsocks stop
# 启动
/etc/init.d/shadowsocks start
# 查看运行状态
/etc/init.d/shadowsocks status

# 查看配置信息
vim /etc/shadowsocks/config.json

```


### 参考链接

- [Google Cloud服务免费申请试用以及使用教程](https://51.ruyo.net/2144.html)
- [用Google Cloud搭建免费一年的SS](https://www.jianshu.com/p/6bd66829a1ce)
- [Shadowsocks-go一键安装脚本](https://teddysun.com/392.html)





