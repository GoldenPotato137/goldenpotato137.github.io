---
title: 基于LXD集群部署实验室GPU共享服务器
plugins:
  - mathjax
date: 2025-02-24 15:32:41
updated: 2025-02-24 15:32:41
tags:
---
> 注： 本文内容部分来自GPT生成，实锤有效

### 配置共享目录

#### 在所有节点上准备同名目录

你希望“每台机器的硬盘上都划分一个区域（目录）用来存放共享数据，并将这个位置挂载到容器里”。那么首先，需要在集群每个节点上都以**相同的路径**创建一个目录，比如：

```bash
sudo mkdir -p /srv/data
# 赋予容器读写权限需要的权限/用户组
sudo chown root:root /srv/data
sudo chmod 755 /srv/data
```

> 该目录名称 `/srv/data` 只是示例，你也可以用其他路径，只要各节点一致即可。

若你真的想要所有节点“共享”同一份数据（即完全相同的内容），则需要在各节点上把 `/srv/data` 这个目录挂载同一个网络卷，例如 NFS、GlusterFS、CephFS 等。那时容器里看到的就是同一份数据。

如果你只想让每台节点的 `/srv/data` 独立存放各自的数据（但容器模板统一），只需在各节点上创建相同的目录即可，无需共享存储。

#### 通过 LXD 配置文件/Profiles 的方式挂载目录

在 LXD 中，可以通过创建一个新的 profile，将主机上的目录挂载到容器里。这样你在给容器应用该 profile 时，就能自动加载挂载点。

1. 创建一个新的 profile，假设叫做 `myshared`：

```bash
lxc profile create myshared
```

2. 使用编辑器打开该 profile：

```bash
lxc profile edit myshared
```

3. 在 profile 的配置文件里添加一个 `devices` 节点，把 `/srv/data`（主机路径）映射到 `/data`（容器内路径）：

```yaml
config: {}
description: "profile for mounting local /srv/data into container"
devices:
  mydata:
    path: /data
    source: /srv/data
    type: disk
name: myshared
```

保存退出之后，profile 就创建好了。

4. 当你要基于同一个容器镜像创建容器时，直接指定要使用 `myshared` profile：

```bash
# 假如你有一个叫 ubuntu:20.04 的镜像
lxc launch ubuntu:20.04 mycontainer -p default -p myshared
```

这样创建出来的容器就会自动在 `/data` 挂载宿主机的 `/srv/data` 目录。

> 只要每个节点（宿主机）都具备同名目录 `/srv/data`，这个 profile 在不同节点上都能生效，而容器本身用的镜像模板是同一个，不需要为每个节点做单独的镜像。
