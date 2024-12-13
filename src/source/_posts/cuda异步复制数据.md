---
title: cuda异步复制数据
plugins:
  - mathjax
date: 2024-12-13 10:28:38
updated: 2024-12-13 10:28:38
tags: hpc
---
本文主要记录如何在device代码内异步把数据从全局内存复制至shared内存，有关如何异步把数据从主机端拷贝到设备端，可以参考[How to Overlap Data Transfers in CUDA C/C++ | NVIDIA Technical Blog](https://developer.nvidia.com/blog/how-overlap-data-transfers-cuda-cc/)

>  本文主要内容来源于英伟达博客：[Controlling Data Movement to Boost Performance on the NVIDIA Ampere Architecture | NVIDIA Technical Blog](https://developer.nvidia.com/blog/controlling-data-movement-to-boost-performance-on-ampere-architecture/)

## 引言

早期，使用[CudaDMA](http://lightsighter.github.io/CudaDMA/)可以通过多分配一个warp用来搬运数据，剩余的计算warp异步执行计算动作。随着Ampere架构（30系）的推出，借助新硬件架构的支持，英伟达推出了新的数据异步搬运方案：`cuda::memcpy_async`，无需多余的搬运线程即可异步从global内存往shared内存搬运数据。

使用传统的每个线程搬数据的方案 `some_shared_memory[threadIdx.x] = some_global_memory[threadIdx.x]`时，数据会经过L2缓存->L1缓存->寄存器->Shared这一流程。其中需要借用到寄存器来中转数据。但使用 `cuda::memcpy_async`无需把数据临时放置到寄存器中，在部分情况下可以有效提高并发性（有时warp的启动数目会受到寄存器不足的限制，这种情况多半是每个thread（或warp）使用了过多的寄存器）。

## 参考资料

[Controlling Data Movement to Boost Performance on the NVIDIA Ampere Architecture | NVIDIA Technical Blog](https://developer.nvidia.com/blog/controlling-data-movement-to-boost-performance-on-ampere-architecture/)
