---
title: cute::Tensor 学习笔记
plugins:
  - mathjax
date: 2025-02-26 12:47:09
updated: 2025-02-26 12:47:09
tags:
---
> 本笔记为个人学习记录，仅供参考
>
> 推荐学习连接：
> * https://zhuanlan.zhihu.com/p/661182311
> * https://zhuanlan.zhihu.com/p/662089556
> * https://zhuanlan.zhihu.com/p/663093816


## Layout

用来表述一个复杂的高维数据结构

一个layout由两部分组成：shape 和 stride，前者表示数据结构的形状，后者描述这个数据结构拍扁成一维后应该如何寻址。

其中，shape和stride均为一系列二元组的嵌套，行在前，列在后。如`shape:(3,2)`表示一个3行2列的矩阵。

具体应该如何理解shape和stride请看下图（图片出自：https://zhuanlan.zhihu.com/p/661182311）

其中，矩阵里的数字表示这个位置的元素在矩阵被拍扁为1维数组后对应的下标（位置）；矩阵的描述中上半部分为shape、下半部分为stride

![](/images/cute-Tensor-1.png)

## Tensor

Layout与其对应的数据的组合。