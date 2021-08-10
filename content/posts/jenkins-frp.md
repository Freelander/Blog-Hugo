---
title: 如何利用 frp 实现 Mac 的内网穿透
date: 2019-09-23 21:34:48
tags: [Jenkins]
draft: false
---

这里服务端我用的是阿里云的轻量应用服务器，系统配置的 Ubuntu18.04；客户端是自己开发的 Mac 电脑。

下面记录的是如何利用 frp 来实现本地的 web 端口可以使用外网来访问。

## 服务端

1. 下载并移动文件；
```
wget https://github.com/fatedier/frp/releases/download/v0.29.0/frp_0.29.0_linux_amd64.tar.gz
# 如果系统未安装 wget，可以先安装 wget
yum -y install wget

# 解压 frp 包
tar -zxvf frp_0.29.0_linux_amd64.tar.gz
# 重新命名文件夹为 frp
mv frp_0.29.0_linux_amd64 frp
```
2. 给运行文件赋予权限，并修改配置文件
```
cd frp
chmod +x frps
# 编辑服务器配置文件
vim frps.ini
```

3. 以下为 frps.ini 配置
```
[common]
bind_port = 7000
auto_token=12345678
vhost_http_port = 8080
vhost_https_port = 443

dashboard_port = 7500
# dashboard 用户名密码，默认都为 admin
dashboard_user = admin
dashboard_pwd = admin
```

###  使用supervisor设置开机自启, 安装 supervisor

```
apt-cache search supervisor
apt-get install supervisor
```

**安装 Supervisor 报错**

```
Reading package lists... Done
Building dependency tree       
Reading state information... Done
E: Unable to locate package supervisor
```
查询资料后，发现是是apt需要更新
```
sudo apt-get update
```

8. 创建并编辑 frp.conf 设置开机自启
```
cd /etc/supervisor/conf.d/
touch frp.conf
vim frp.conf

# 以下为 frp.conf 配置
[program:frp]
command = /root/frp/frps -c /root/frp/frps.ini 
autostart = true
autorestart = true
startsecs=0

# systemctl 查看开机启动项
systemctl list-unit-files | grep supervisor
```

9. 访问 服务端ip:7500，查看服务端是否配置成功

配置成功是可以看到

如果访问不成功，阿里云服务器后台检查防火墙配置，需要添加刚才两个端口到规则里面

![image](/images/2019/09/06.png)

## 客户端

客户端和服务端的版本号要一致，另外 Mac 下载的是 darwin_amd64 这个格式，[下载地址](https://github.com/fatedier/frp/releases/download/v0.29.0/frp_0.29.0_darwin_amd64.tar.gz)。

1. 下载文件回来后通过命令解压并移动文件；
```
# 解压并移动文件夹
tar zxvf frp_0.29.0_darwin_amd64.tar.gz
# 在 /usr/local/bin 目录下创建一个 frpc 文件夹
mkdir /usr/local/bin/frpc
# 将刚才解压的文件内容移动
mv frp_0.27.0_darwin_amd64/* /usr/local/bin/frpc
# 删除压缩包
rm -rf frp_0.29.0_darwin_amd64
```

2. 给运行文件权限，并编辑配置文件；
```
cd /usr/local/bin/frpc
# 赋予权限
chmod +x frpc
# 修改配置文件
vim frpc.ini
```

3. 下面为 frpc.ini 配置。
```
[common]
# 这里修改为 frp 服务端的 ip 地址
server_addr = x.x.x.x
# 与服务端绑定的进行通信的端口，跟服务端配置的端口一致
server_port = 7000
auto_token = 123456

[jenkins]
type = http
# 本地映射的端口，比如开了个tomcat apache，端口为8080
local_port = 8080
# 域名必须要有，并解析到你的服务器地址
# 记得域名要解析到服务器地址，否则会穿透失败
custom_domains = test.com
```

### 配置 frpc.plist 实现开机自启

1. 创建开机自启文件并编辑；
```
touch ~/Library/LaunchAgents/frpc.plist
vim ~/Library/LaunchAgents/frpc.plist
```
2. 以下为 frpc.plist 配置；
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC -//Apple Computer//DTD PLIST 1.0//EN
http://www.apple.com/DTDs/PropertyList-1.0.dtd >
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>frpc</string>
    <key>ProgramArguments</key>
    <array>
         <string>/usr/local/bin/frpc/frpc</string>
         <string>-c</string>
         <string>/usr/local/bin/frpc/frpc.ini</string>
    </array>
    <key>KeepAlive</key>
    <true/>
    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```

3. 加载生效。
```
# 赋权限
sudo chown root ~/Library/LaunchAgents/frpc.plist
# 启动
sudo launchctl load -w ~/Library/LaunchAgents/frpc.plist
```

## 测试
验证是否穿透成功，在浏览器输入上文配置的域名，比如 test.com:8080 ，如果能访问即配置成功。

## 参考资料

* [搭建frp实现内网穿透](https://blog.csdn.net/zengd0/article/details/79522253)
* [Mac 下使用 FRP 实现内网穿透](https://streamelody.github.io/2019/05/mac-frp-tutorial/)
* [阿里DNS：DNS迁移到阿里云，这一篇就够了](https://zhuanlan.zhihu.com/p/42574259)
* [阿里云 域名解析](https://dns.console.aliyun.com/#/dns/domainList)