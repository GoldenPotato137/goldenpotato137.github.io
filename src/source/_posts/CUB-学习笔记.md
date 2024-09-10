---
title: CUB 学习笔记
date: 2024-09-10 09:40:10
tags: HPC
plugins:
  - mathjax
---

## 什么是CUB
一个提供了一系列基于thread、warp、block、device等维度的便利函数的库，如：block级的基数排序（可以把一个block能访问到的数据都排好序）、warp级的读入函数（按照warp从核函数实参中的数组读入数据到thread的数组上）。

以下为一个使用block级的基数排序的例子：
```cpp
#include <cstdio>
#include <cub/cub.cuh>

//
// Block级的基数排序（aka只能排序同个block的数据）
//
template<int BLOCK_THREADS, int ITEMS_PER_THREAD>
__global__ void BlockSortKernel(int *d_in, int *d_out)
{
    // 使用using缩写需要用到的cub函数（类）
    using BlockLoadT = cub::BlockLoad<
            int, BLOCK_THREADS, ITEMS_PER_THREAD, cub::BLOCK_LOAD_TRANSPOSE>;
    using BlockStoreT = cub::BlockStore<
            int, BLOCK_THREADS, ITEMS_PER_THREAD, cub::BLOCK_STORE_TRANSPOSE>;
    using BlockRadixSortT = cub::BlockRadixSort<
            int, BLOCK_THREADS, ITEMS_PER_THREAD>;

    // block级的cub函数需要一个shared memory来作为临时变量，这里使用union来让不同的cub函数复用
    __shared__ union
    {
        typename BlockLoadT::TempStorage load;
        typename BlockStoreT::TempStorage store;
        typename BlockRadixSortT::TempStorage sort;
    } temp_storage;

    // 使用cub的block级的load函数读取数据到thread的数组里
    int thread_keys[ITEMS_PER_THREAD];
    int block_offset = blockIdx.x * (BLOCK_THREADS * ITEMS_PER_THREAD);
    BlockLoadT(temp_storage.load).Load(d_in + block_offset, thread_keys);

    __syncthreads();        // 因为要重用临时的shared变量，需要同步，下同

    // block级基数排序，如果原先每个thread里的数据分别为：[1,4,6],[8,7,3],[9,4,7]，排序过后就会变为：[1,3,4],[4,6,7],[7,8,9]
    BlockRadixSortT(temp_storage.sort).Sort(thread_keys);

    __syncthreads();        // 临时变量重用所需

    // 使用cub的block级别的保存函数把数据输出到实参数组里
    BlockStoreT(temp_storage.store).Store(d_out + block_offset, thread_keys);
}

int main()
{
    int *in_d, *out_d;
    static const int n = 128 * 16;
    cudaMallocManaged(&in_d, sizeof(int) * n);
    cudaMallocManaged(&out_d, sizeof(int) * n);

    for (auto i = 0; i < n; i++)
        in_d[i] = n - i;

    BlockSortKernel<128, 16><<<1, 128>>>(in_d, out_d); //需要注意每个thread的线程数和cub函数里设置的线程数（在本例子中使用模板参数传入）应该保持一致，因为cub本质上也是给手写的方法包一层，并不会凭空增加或减少内存
    cudaDeviceSynchronize();

    for (auto i = 0; i < n; i++)
        printf("%d ", out_d[i]);
    cudaFree(in_d);
    cudaFree(out_d);
    return 0;
}
```