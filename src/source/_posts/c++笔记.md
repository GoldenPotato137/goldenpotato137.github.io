---
title: c++笔记
date: 2024-07-24 11:19:29
updated: 2024-09-14 11:19:29
plugins:
  - mathjax
tags:
---
## cmake
https://subingwen.cn/cmake/CMake-primer/index.html

## consteval与constexpr
https://tjsw.medium.com/%E6%BD%AE-c-20-consteval-constexpr-%E7%9A%84%E5%A5%BD%E5%85%84%E5%BC%9F-bfbcfdd4c763

## 运算小寄巧
虽然跟c++没啥关系，但懒得开新post了，就放这吧。

1. 向上取整到整数$x$的某个最近倍数上：`num = (num + x - 1) & (0-x)`。

2. 检查某个数$x$是不是2的幂次方：`!(x & (x-1))`

3. 获取某个数$x$的最低位的1以及其之后所有的0：`x & (0-x)`，如：$x =(11100100)_2, x\ \& \ (0-x) = (00000100)_2$

将2,3配合到一起可以把一个数变为一个比该数小的最近2的幂次方的数：
```cpp
while(x & (x-1))
    x -= x & (0-x);
```