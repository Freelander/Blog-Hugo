---
title: "使用 Python 脚本将安装包上传Fir或蒲公英平台"
date: 2022-01-23T22:34:38+08:00
draft: false
tags: [Python,Fir,Pgyer]
categories: 开发平台
---

国内知名的两大应用托管内测平台，使用 Python 脚本的方式实现安装包上传。

### Fir

Fir 有三种方式上传：

1. 使用官方 Api 方式，[文档](https://www.betaqr.com/docs/publish)
2. 通过 fir-cli 命令行方式, [文档](https://github.com/FIRHQ/fir-cli/blob/master/README.md#%E6%96%87%E6%A1%A3)
3. Fastlane 插件方式，[文档](https://github.com/FIRHQ/fastlane-plugin-fir_cli)

由于 1、3 方式处理过程比较繁琐复杂，经考虑选择通过 fir-cli 命令行上传应用，即方式 2；

#### 安装 fir-cli

安装文档地址：[https://github.com/FIRHQ/fir-cli/blob/master/doc/install.md](https://github.com/FIRHQ/fir-cli/blob/master/doc/install.md)

这里有个比较坑的地方，按文档安装会有个报错信息：

```
(Gem::FilePermissionError)

    You don't have write permissions for the /usr/bin directory.
```

**注意：** 即使在前面加上sudo gem install fir-cli 也没啥用。

需要更改一下安装目录，正确执行安装命令：

```
sudo gem install -n /usr/local/bin fir-cli
```

参考文档：[mac系统安装fir-cli(最新有效)](https://www.jianshu.com/p/0b93a47b265c)

#### 脚本

```python
import os
import re

api_token = '点击头像可获得 api token 信息'
file_path = '安装包文件路径'

# 执行的命令
cmd = 'fir p {} -T {}'.format(file_path, api_token)
# python 脚本执行命令，并输出信息
output = os.popen(cmd).read()
print(output)

# 利用正则匹配到 fir 下载的地址及对应版本的 release id
release_id_regex = '(?<=Release id is )[a-zA-Z0-9_]*'
url_regex = '(?<=Published succeed: )[a-zA-Z]+://[^\s]*'
release_id = re.search(release_id_regex, output).group(0).strip()
url = re.search(url_regex, output).group(0).strip()

# 拼接组合成下载地址，例如：http://d.maps9.com/59exxx?release_id=61ecdeexxxxxxx
fir_download_url = '{}?release_id={}'.format(url, release_id)
print(fir_download_url)

```

### Pgyer

蒲公英相对 Fir 来说简单很多，直接使用 Api 方式上传。

文档地址：[https://www.pgyer.com/doc/view/api#uploadApp](https://www.pgyer.com/doc/view/api#uploadApp)

#### 脚本

```python
import json
import os

file_path = '安装包文件路径'
api_key = '36ce6f5facxxxxxxxxxxxxxxxxxxx'

# 执行的命令
cmd = 'curl -F \'file=@{}\' -F \'_api_key={}\' https://www.pgyer.com/apiv2/app/upload'.format(file_path, api_key)
# 执行命令并输出信息
output = os.popen(cmd).read()
print(output)

# 上传成功之后是返回一段 json 字符串，将其转换成 json
result = json.loads(output)
# 获取 build key
build_key = result['data']['buildKey']
pgyer_download_url = 'https://www.pgyer.com/{}'.format(build_key)
print(pgyer_download_url)
```


