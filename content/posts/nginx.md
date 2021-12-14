---
title: "Mac 上 Nginx 的使用及入门"
date: 2021-10-12T19:43:01+08:00
draft: false
tags: [Nginx]
---

### 安装

推荐 Mac 电脑上内置 homebrew 工具安装。

安装 Nginx：

``brew install nginx``

卸载 Nginx：

``brew uninstall nginx``

### 常用操作

启动 Nginx：

``sudo nginx``

停止 Nginx：

``sudo nginx -s stop``

热重启 Nginx：

``sudo nginx -s reload``

强制停止 Nginx：

``sudo pkill -9 nginx``

### 配置文件

经常要用到的几个文件路径：

1. ``/usr/local/etc/nginx/nginx.conf`` （nginx 配置文件路径）
2. ``/usr/local/Cellar/nginx/1.21.0``（nginx 的安装路径）
3. ``/usr/local/var/log/nginx`` (nginx 默认的日志路径)
4. ``/usr/local/var/www`` （nginx 服务器默认的根目录）

### 使用场景

#### 一、将域名转发到本地端口

##### 1. 配置 nginx 文件规则

```
server {
    listen 80;
    # 访问的域名
    server_name jenkins.gojun.me;
    
    location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-NginX-Proxy true;
        # 转发本地端口地址
        proxy_pass http://127.0.0.1:8080;
        proxy_ssl_session_reuse off;
        proxy_set_header Host $http_host;
        proxy_redirect off;
    }
}
```
##### 2. 修改 hosts 文件

``sudo vi /etc/hosts``

修改内容如下：

```conf
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1       localhost
# 本机的 IP 对应 nginx 配置的 server_name
192.168.1.103   jenkins.gojun.me
255.255.255.255 broadcasthost
::1             localhost
```

##### 3. 操作完记得重启 Nginx

```s
sudo nginx -s reload
```

### 效果

配置完成后看看效果：

#### 本地端口访问

![img](/images/2021/09/nginx-01.png)

#### 自定义域名访问

![img](/images/2021/09/nginx-02.png)

### 局域网访问


### 给域名进行加密签名


### 学习资料

* [8分钟带你深入浅出搞懂 Nginx](https://zhuanlan.zhihu.com/p/34943332)
* [连前端都看得懂的《Nginx 入门指南》](https://juejin.cn/post/6844904129987526663)
* [用Nginx做端口转发（反向代理）](https://zhuanlan.zhihu.com/p/108740468)
* [nginx域名配置](https://www.jianshu.com/p/1372a097a770)
