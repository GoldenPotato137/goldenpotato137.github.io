---
title: Ubuntu 20.02LTS 填坑实录
tags: []
id: '492'
categories:
  - - uncategorized
date: 2021-02-25 22:44:43
updated: 2021-02-25 22:44:43
---

**1.Ubuntu启动时如何显示或隐藏启动消息？** https://qastack.cn/ubuntu/248/how-can-i-show-or-hide-boot-messages-when-ubuntu-starts 
**2.cuda版本查看** https://blog.csdn.net/qq\_41368074/article/details/107785536 
**3.cudnn安装** https://blog.csdn.net/weixin\_44002829/article/details/111500287

> 注：在运行demon前需安装freeimage库 sudo apt-get install libfreeimage3 libfreeimage-dev

4. cli连接wifi: https://ubuntu.com/core/docs/networkmanager/configure-wifi-connections

5. cuda测试程序（自用）
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