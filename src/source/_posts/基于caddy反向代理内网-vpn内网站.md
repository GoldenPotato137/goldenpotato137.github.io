---
title: 基于caddy反向代理内网/vpn内网站
plugins:
  - mathjax
date: 2025-02-23 15:32:41
updated: 2025-02-23 15:32:41
tags:
---
要反向代理内网的网站，主要难点来自于caddy默认的HTTP-01证书获取模式是没法使用的（因为域名指向内网，而认证服务器显然没法访问内网地址），需要走DNS-01模式。本文主要以腾讯云（dnspod）为例，介绍如何将腾讯云dns模块编译进caddy中并完成认证过程。

### 科学上网

腾讯云dns模块在github上。总所周知，裸连github是个非常玄学的事情，强烈建议提前准备魔法上网并将http/https代理写入source中以避免天朝特有的网络问题。

```bash
# 编辑文件 ~/.bashrc
# 在最底下添加这几句
export http_proxy="http://127.0.0.1:7890"
export https_proxy="http://127.0.0.1:7890"
export HTTP_PROXY="$http_proxy"
export HTTPS_PROXY="$https_proxy"
export no_proxy="localhost,127.0.0.1,.example.com"
export NO_PROXY="$no_proxy"
```

加载source

```bash
source ~/.bashrc
```

### 安装Go && 编译工具 （xcaddy）

Caddy官方的编译工具 `xcaddy`需要go语言环境，请参考[https://go.dev/wiki/Ubuntu](https://go.dev/wiki/Ubuntu)安装。


```bash
go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest
```

### 指定dns模块编译caddy

```bash
~/go/bin/xcaddy build --with github.com/caddy-dns/tencentcloud
```

其中，命令里--with后填入要编译的模块仓库，请自行替换为你需要的dns挑战模块。

可同时添加多个模块，如：
```bash
xcaddy build --with github.com/user/module1 --with github.com/user/module2
```

### 验证编译结果
```bash
~/go/bin/caddy list-modules | grep tencentcloud #替换成你的模块名
```

如果出现了你要的模块，证明编译成功了，接下来就是把这个二进制文件替换原有的二进制文件。

### 二进制替换
```bash
sudo systemctl stop caddy  # 停止caddy服务
# 可以考虑替换前备份原有的二进制文件
sudo cp ~/go/bin/caddy /usr/local/bin/caddy
sudo cp /usr/local/bin/caddy /usr/bin/caddy
```

### 修改你的caddy配置
请你参考你的dns模块README修改你的caddy配置文件，如腾讯云（dnspod）是这样填的：


```caddy
# 修改这里的全局配置，如果没有全局配置自行添加即可
{
  acme_dns tencentcloud {
    secret_id 你的腾讯云SECRET_ID
    secret_key 你的腾讯云SECRET_KEY
  }
}

# 以下为你的各个网站
your.site {
  reverse_proxy 192.168.66.1:80
}
```

搞好啦，撒花~ *★,°*:.☆(￣▽￣)/$:*.°★* 。