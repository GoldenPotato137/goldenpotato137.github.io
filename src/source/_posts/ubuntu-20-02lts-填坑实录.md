---
title: Linux小寄巧
tags: []
id: '492'
categories:
  - - uncategorized
date: 2021-02-25 22:44:43
updated: 2025-01-25 22:44:43
---
**Ubuntu启动时如何显示或隐藏启动消息？** https://qastack.cn/ubuntu/248/how-can-i-show-or-hide-boot-messages-when-ubuntu-starts

**cuda版本查看** https://blog.csdn.net/qq\_41368074/article/details/107785536

**cudnn安装** https://blog.csdn.net/weixin\_44002829/article/details/111500287

> 注：在运行demon前需安装freeimage库 sudo apt-get install libfreeimage3 libfreeimage-dev

**cli连接wifi**: https://ubuntu.com/core/docs/networkmanager/configure-wifi-connections

**cuda测试程序（自用）**
```cuda
#include <cstdio>

__global__ void test()
{
    printf("%d\n", threadIdx.x);
}

int main()
{
    test <<<1, 1>>>();
    cudaDeviceSynchronize();
    auto last_err = cudaGetLastError();
    if (last_err != cudaSuccess)
    {
        char s[200];
        sprintf(s, "CUDA error %s", cudaGetErrorString(last_err));
        printf("%s\n", s);
        exit(1);
    }
    return 0;
}
```

**查看某个端口被什么占用了**
```shell
sudo lsof -i :8080
```

**赋予promtail docker用户访问/var/log/caddy/.log的权限**

1. 为日志文件添加对其他用户 (o) 的读权限：  
   ```bash
   chmod o+r /var/log/caddy/*.log
   ```
2. 或者使用 ACL（Access Control List）让 UID 10001（容器中的 loki 用户）有读权限：  
   ```bash
   setfacl -m u:10001:r /var/log/caddy/*.log
   ```