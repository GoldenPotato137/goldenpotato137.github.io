---
title: CUB 学习笔记
date: 2024-09-10 09:40:10
tags: HPC
plugins:
  - mathjax
---
> 本文中所有的例程改编于[CUB文档](https://nvidia.github.io/cccl/cub/index.html)

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

    // block级的cub函数需要某种shared memory来作为临时变量，这里使用union来让不同的cub函数复用
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

## CUB食用方法
### CUB 方法分类
CUB方法分为四层，分别是thread级、warp级、block级与device级。一般来说，thread级的函数是内部实现使用的，不会作为API暴露出来。

下表展示了各个层级方法的特点:
| 层级   | 合作调用 | 并行执行 | 最大线程数 | 结果存于   |
| ------ | -------- | -------- | ---------- | ---------- |
| thread | -        | -        | 1          | 该线程     |
| warp   | +        | +        | 32         | 第一个线程 |
| block  | +        | +        | 1024       | 第一个线程 |
| device | -        | +        | $\infty$   | 全局内存   |

表中，合作调用指的是某个方法是否会一次性调用一大堆。比如说warp级与block级的方法，本质上都是每个thread都要执行某些函数并合作完成这个方法（一个典型例子为block级的归并排序，每个thread都要完成自己thread的内部归并，再一层层把结果归并到第一个thread上）。调用device层级的方法就像调用了一个cuda核函数，不太可能一次性调用多个（多流情况暂不考虑）。

并行执行为字面义，指某个方法是否为并行的。对于thread级的方法，显然其是串行执行的（比如说thread级的reduce，就是某个线程把值从头加到尾（和的reduce））。

结果存于第一个线程指的是对于warp和block层级的方法，其第一个线程拿的变量才代表真正的结果（一个典型的例子是shuffle_down，显然结果会被规约到第一个线程）。对于device层级的方法，其结果会被作为一个实参输出出来（这个实参会被先放到全局显存中，调用者再自行选择是否cudaMemcpy到host）。

值得注意的是，CUB中的warp与硬件上的warp（一个warp32线程）并不完全一样。CUB中的warp为一个虚拟概念，可以自由分配1-32个线程。当线程数正好为32时（这也是默认值），任务会被交由一个真实的硬件warp执行。其他情况下，该warp并不直接对应任何的硬件单元。

### CUB 基本知识
+ 所有CUB方法都是按照模板类来提供的，例如block级的reduce方法：
```cpp
template<typename T, int BLOCK_DIM_X, BlockReduceAlgorithm ALGORITHM = BLOCK_REDUCE_WARP_REDUCTIONS, int BLOCK_DIM_Y = 1, int BLOCK_DIM_Z = 1, int LEGACY_PTX_ARCH = 0>
class BlockReduce
...

//调用：
__global__ void ExampleKernel(...)
{
    // Specialize BlockReduce for a 1D block of 128 threads of type int
    using BlockReduce = cub::BlockReduce<int, 128>;

    // Allocate shared memory for BlockReduce
    __shared__ typename BlockReduce::TempStorage temp_storage;

    // Obtain a segment of consecutive items that are blocked across threads
    int thread_data[4];
    ...

    // Compute the block-wide max for thread0
    int aggregate = BlockReduce(temp_storage).Reduce(thread_data, cub::Max());
```       

+ 方法（block级、warp级）都需要临时的shared变量（该变量会用一个struct打包所有需要的临时值，可以用 `方法类名::TempStorage`获取该方法所需的临时变量的struct）

+ 根据传入参数的不同（比如说warp级的方法中的线程数是不是2的偶数倍）会在编译期或运行期选用不同的实现。

+ warp级、block级方法的线程数需要在编译期确定下来。

### CUB 使用流程
无论是什么层级的CUB方法，基本上都按照下面的流程进行调用：

1. 选择要使用的方法类（并确定参数）：如`  using BlockReduce = cub::BlockReduce<int, 128>;
2. 查询该方法需要的临时shared变量的类型（某种struct），如：` using TempStorage = typename BlockReduce::TempStorage;`
3. 创建该临时变量，如：`__shared__ TempStorage temp_storage;`
4. 创建方法类并调用，如：`auto result = block_reduce{temp_storage}.Sum(data); //这个result只在第一个线程中才是真的，参考本大节第一小节`

以下为一个完整的block级方法的调用例子：
```cpp
__global__ void kernel(int* per_block_results)
{
  // (1) Select the desired class
  // `cub::BlockReduce` is a class template that must be instantiated for the
  // input data type and the number of threads. Internally the class is
  // specialized depending on the data type, number of threads, and hardware
  // architecture. Type aliases are often used for convenience:
  using BlockReduce = cub::BlockReduce<int, 128>;
  // (2) Query the temporary storage
  // The type and amount of temporary storage depends on the selected instantiation
  using TempStorage = typename BlockReduce::TempStorage;
  // (3) Allocate the temporary storage
  __shared__ TempStorage temp_storage;
  // (4) Pass the temporary storage
  // Temporary storage is passed to the constructor of the `BlockReduce` class
  BlockReduce block_reduce{temp_storage};
  // (5) Invoke the algorithm
  // The `Sum()` member function performs the sum reduction of `thread_data` across all 128 threads
  int thread_data[4] = {1, 2, 3, 4};
  int block_result = block_reduce.Sum(thread_data);

  per_block_results[blockIdx.x] = block_result;
}
```
