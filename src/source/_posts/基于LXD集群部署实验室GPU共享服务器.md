---
title: 基于LXD集群部署实验室GPU共享服务器
plugins:
  - mathjax
date: 2025-02-24 15:32:41
updated: 2025-02-24 15:32:41
tags:
---
> 注： 本文内容部分来自GPT生成，实锤有效

> 部署LXD的知识很多来自于[yuanyu dalao](https://blog.yuany3721.site/blogs/blog/2023-06-23.html#%E5%A6%82%E4%BD%95%E8%AE%BF%E9%97%AE%E5%AE%BF%E4%B8%BB%E6%9C%BA)，推荐各位有需要部署单机LXC/LXD的小伙伴参考他的文章~

### 动机 & LXD集群好处

很多实验室由于各种因素（没钱）的制约，很难给所有成员各自分配一台乃至多台服务器，导致共用服务器的需求产生。

很不幸的是，每个人对环境的需求是千差万别的，有的人可能需要新版本的GCC，有的人可能需要老版本的GCC，这种需求不同尚可解决，明确声明版本即可处理。但有些情况下不同需求是没法调和的，比如说对显卡驱动版本的需求。再者，有些人需要安装一些软件，这些软件可能会在各种地方与现有的东西产生矛盾，产生很多问题。

机器不够用，但又想实现“一人一台机器”，很容易让人想到虚拟机的解决方案。linux上常见的硬件级虚拟方案为KVM。但这种虚拟方法有严重的问题：资源无法共享，分配给一台虚拟机的资源（比如说某块显卡）没法被另一台机器占用，容易造成严重的资源浪费。

基于以上的一人一虚拟机，且虚拟机资源能共享的需求，我找到的解决方案为LXD（集群），它可以让每个人都有一个虚拟机的同时共享硬件资源。使得不同用户能不互相干扰，和谐共享服务器资源。

以下为LXD（集群）的好处：
* 一人一台虚拟机，各位成员的行为不互相干扰，同时能较大程度的确保宿主机稳定
* （集群好处）：不同机器可以通过网络共享镜像、配置，从而不需要在每台服务器上都进行繁琐的配置过程，新机器可以快速加入集群上岗。同时可以透过网络在任意机器（节点）管理所有机器，大幅减少网管工作量。

以下我将介绍如何从零开始配置一个LXD集群。

### 开始操作之前

选择一台机器作为一号节点，这台机器将会成为我们第一个配置的节点，也将成为LXD集群leader。（之后下线也不要紧，集群会自动推选leader节点）

### 配置宿主机网络

在内网中，我们有的是ip，完全没必要通过端口转发去访问容器。因此，我这里将介绍使用网络桥接的方案，让容器直接找路由器dhcp获取ip来上网。

首先，确认你的网络端口，输入
```bash
ip addr
```
可能获得形如以下的输出，找到其中的有内网ip的那个，那个就是你机器上用来上网的网卡，记住它的名字，并确认你的内网网段（我们待会要给宿主机设置静态ipv4地址）

比如说在我这里的例子中，网卡名为`eno1np0`，内网网段为`192.168.66.0/24`
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: eno1np0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 10:ff:e0:a9:8c:1d brd ff:ff:ff:ff:ff:ff
    altname enp97s0f0np0
    inet 192.168.66.172/24 brd 192.168.66.255 scope global dynamic noprefixroute eno1np0
       valid_lft 86367sec preferred_lft 86367sec
    inet6 fd77:9bdf:765e::ddb/128 scope global noprefixroute
       valid_lft forever preferred_lft forever
    inet6 fd77:9bdf:765e:0:d3f7:c207:d6b3:baa6/64 scope global temporary dynamic
       valid_lft 604768sec preferred_lft 85838sec
    inet6 fd77:9bdf:765e:0:ef89:f3cc:72fe:359b/64 scope global mngtmpaddr noprefixroute
       valid_lft forever preferred_lft forever
    inet6 fe80::d213:e835:1202:7e04/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
...
```
编辑文件`etc/netplan/50-cloud-init.yaml ` （有可能不是这个名字，请自行tab）

修改为形如以下的内容：
```yml
network:
  version: 2
  ethernets:
    eno1np0:                  # 这里要修改为你刚刚看到的网卡端口名
      dhcp4: no               
      optional: true          
  bridges:
    br0:                      # 给新建的桥接端口起个名，我这里以br0为例，后续将沿用这个名字
      interfaces: [eno1np0]   # 和上面的网卡端口名保持一致
      dhcp4: no
      addresses: [192.168.66.41/24] # 根据你的内网网段自行选择一个静态ipv4，请提前确认这个ip无其他设备使用
      routes:
        - to: default
          via: 192.168.66.1   # 网关，一般为把你的内网ip最后一位改成1，如果不一致请自行确认
          metric: 100
      nameservers:
        addresses: [1.1.1.1, 8.8.8.8]
```

如果你发现你的网络配置是cloudinit下发的，可以选择关闭cloudinit，或是自行修改cloudinit配置，这里以关闭cloudinit为例：

```bash
sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg <<EOF
network: {config: disabled}
EOF
```

输入以下命令，以检查配置是否有效并应用网络配置。
```bash
sudo netplan try
```

### 安装LXC与LXD
```bash
sudo apt update
sudo apt install lxc
sudo snap install lxd
```

### 配置一号节点

输入以下命令开启交互式配置，系统会一句一句问你需要什么样的配置，以下给出我的回答案例供参考
```
sudo lxd init
```

```bash
Would you like to use LXD clustering? (yes/no) [default=no]: yes  #是否要启用集群，yes
What IP address or DNS name should be used to reach this server? [default=100.64.0.14]:   100.64.0.14 # 这里填入要访问这台机器控制端api的域名或ip，对于普通情况下，填入内网ip即可
Are you joining an existing cluster? (yes/no) [default=no]: no
What member name should be used to identify this server in the cluster? [default=4080-2]:
Do you want to configure a new local storage pool? (yes/no) [default=yes]:
Name of the storage backend to use (zfs, btrfs, dir, lvm) [default=zfs]:
Create a new ZFS pool? (yes/no) [default=yes]:
Would you like to use an existing empty block device (e.g. a disk or partition)? (yes/no) [default=no]: # 是否要用一个单独的硬盘或分区来作为存储池，按实际需求填写即可
Size in GiB of the new loop device (1GiB minimum) [default=30GiB]: 1000GiB
Do you want to configure a new remote storage pool? (yes/no) [default=no]:
Would you like to connect to a MAAS server? (yes/no) [default=no]:
Would you like to configure LXD to use an existing bridge or host interface? (yes/no) [default=no]: yes #是否使用配好的网络接口，yes（我们刚刚配了桥接接口）
Name of the existing bridge or host interface: br0 #这里填入刚刚给桥接接口起的名
Would you like stale cached images to be updated automatically? (yes/no) [default=yes]:
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]:
```

### 其他节点加入集群

首先，要在一号节点上（事实上，任何节点都能执行节点配置命令，只不过目前我们只有一号节点配置好了）设置集群的加入密码

```bash
lxc config set core.trust_password 你的密码
```
在执行以下操作之前，请在每台机器上都完成`配置宿主机网络`与`安装LXC和LXD`这两步。

类似的，输入`sudo lxd init`进入交互式配置

以下以我的交互为例：
```bash
Would you like to use LXD clustering? (yes/no) [default=no]: yes
What IP address or DNS name should be used to reach this server? [default=172.17.0.1]: 100.64.0.15 #同上，一般填这台机器的内网地址
Are you joining an existing cluster? (yes/no) [default=no]: yes
Do you have a join token? (yes/no/[token]) [default=no]: no
What member name should be used to identify this server in the cluster? [default=ubuntu]: 3080-2
IP address or FQDN of an existing cluster member (may include port): 100.64.0.14 #这里填一号节点刚刚填入的访问地址
Cluster fingerprint: fb04d9240aef4ce4dc0137afc50fc2bbe29dbaca1d9c587b42882d21400c07bb
You can validate this fingerprint by running "lxc info" locally on an existing cluster member.
Is this the correct fingerprint? (yes/no/[fingerprint]) [default=no]: yes
Cluster trust password:
All existing data is lost when joining a cluster, continue? (yes/no) [default=no] yes
Choose "size" property for storage pool "local":
Choose "source" property for storage pool "local":
Choose "zfs.pool_name" property for storage pool "local":
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]:
```

### 使用你的LXD集群

到此，我们集群就算配置好啦 *★,°*:.☆(￣▽￣)/$:*.°★* 。

接下来我们需要配置一个容器模板（或者说如果你喜欢直接用最原生态的什么都没装的容器（比如说原版ubuntu-server）并直接分发给组员，倒也不是不行（逃）

首先，创建一个基于原版ubuntu server的容器，版本以24.04为例：
```bash
# 以下命令在任意LXD节点执行均可，LXD集群的最大魅力就是一处配置处处同步
# 启动一个基于ubuntu 24.04的容器，容器名为template，在名为node1的宿主机上启动（不填这个--target node1也行，会自动选一个最合适的宿主机）
sudo lxc launch ubuntu:24.04 template --target node1

# 进入容器（使用刚刚创建的template容器的bash）
sudo lxc exec template bash
```

输入完上述命令后会进入容器`template`的bash，接下来是配置这个容器的环节，之后这个容器会被导出成镜像作为新容器的创建模板，按照你的喜好来做即可

以下为我的操作，仅供参考
```bash
timedatectl set-timezone Asia/Shanghai # 修改时区
apt update && apt install ssh openssh-server #安装ssh-server
# 在我的实践中，发现这样安装的ssh会被cloud init设置为不允许密码登录，需要手动取消。在我这里，这个设置的文件是etc/ssh/sshd_config.d/60-cloudimg-settings.conf，把里面的PasswordAuthentication no 去掉即可
nano /etc/ssh/sshd_config.d/60-cloudimg-settings.conf

# 安装显卡驱动
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
dpkg -i cuda-keyring_1.1-1_all.deb
apt-get update
apt-get install -y nvidia-open-570 #显卡驱动版本要和宿主机一模一样，注意调整这里的版本号

reboot #重启容器让驱动生效
nvidia-smi #检查驱动是否生效
# 重新连接容器，在任意宿主机上输入：sudo lxc exec template bash

apt-get -y install cuda-toolkit-12-8 # 安装cuda
adduser user # 创建一个新用户给之后别人使用（或者直接用root也行）
usermod -G sudo user # 赋予管理员权限（在容器内别人爱怎么搞都没事）
nano /home/user/.bashrc # 往bashrc里写cuda的地址，具体怎么搞就不在这篇文章中赘述了
```

完成模板容器配置后，在任意节点输入
```bash
lxc stop template #停止刚刚修改的容器
lxc publish template --alias templateName --public #把刚刚的容器打包成一个名为templateName的模板发布
```
这些模板是跨节点的，可以在任意节点使用，要分配容器时，只需：
```bash
sudo lxc launch templateName containerName --target node1 #在节点node1上使用templateName模板启动一个名为containerName的容器
```
找到容器ip（看路由器/`lxc list`找均可），把这个ip（以及创建的默认账户密码）告诉你的师门，搞定~

### 配置共享目录 （选做）

有的时候部分数据是需要跨容器共享的，需要在宿主机上划分一个目录以供各个容器挂载并共享。如果你没有多容器（同一台宿主机上的，若需要跨宿主机共享一些存储，可以考虑进一步的网络数据盘方案）共享目录的需求，可以跳过本小节。

#### 在所有节点上准备同名目录

要在每台机器的硬盘上都划分一个区域（目录）用来存放共享数据，并将这个位置挂载到容器里。那么首先，需要在集群每个节点上都以**相同的路径**创建一个目录，比如：

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
# 假如你有一个叫 ubuntu:20.04 的镜像，并希望它在node1节点启动
lxc launch ubuntu:20.04 mycontainer -p default -p myshared --target node1
```

这样创建出来的容器就会自动在 `/data` 挂载宿主机的 `/srv/data` 目录。

> 只要每个节点（宿主机）都具备同名目录 `/srv/data`，这个 profile 在不同节点上都能生效，而容器本身用的镜像模板是同一个，不需要为每个节点做单独的镜像。
