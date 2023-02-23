# cuda

## 自定义c++算子

* https://pytorch.org/tutorials/advanced/cpp_extension.html
* https://developer.nvidia.com/blog/even-easier-introduction-cuda/

所以写一个cuda kernel和写一个torch算子还不是一回事。

https://zhuanlan.zhihu.com/p/48463543

## cuda编程

https://face2ai.com/program-blog/#GPU编程（CUDA）

Professional CUDA C Programming

https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html

https://nichijou.co/cuda1/

* 为什么__syncthreads没有设计成像pytorch一样，只要不同进程都执行了就能过去呢
* 每个SM能容纳的block数量受限于：每个block需要的shared memory、每个block包含的最大线程数
* 懂了stream的概念对trt engine的运行很有帮助，同一个stream内部sequential，不同stream可以并行。

比如TensorRT里有engine和context概念，一个engine可以创建多个context，同一个engine的context会共享一些参数权重来节省显存。不同的context之间完全可以并行，也就是用不同的stream。（其实感觉这里肯定有更加细粒度的并行控制，比如onnx拓扑图，不同分支可以并行）

实际上cuda很有可能被triton取代，但是triton文档实在太少了，基本上就是黑科技，所以暂时只能学cuda了。

## 核心概念

### 物理层

一个GPU有多个SM（multithreaded streaming multiprocessors），每个SM以32个线程（warp）为单位创建、管理、调度、执行线程。线程束的执行单元是SP，一个SM可以有多个SP。

warp的分配原则就是连续32个thread id被分配到同一个warp，(x, y) is (x + y Dx)，(x, y, z) is (x + y Dx + z Dx Dy)。

执行架构是SIMT，尽量不要让线程束分化是写程序需要考虑的。

每个block有自己需要的shared memory size，每个warp有自己的execution context（占用on-chip register）。因此每个SM可以同时容纳的block和warp是有限的。

这里需要理解为什么要这样设计：

* 硬件制造成本
* 低耗电量
* 内存访问合并：这是由DRAM的特性决定的，访问连续内存会快，因此一个线程束可以将多条访存合并为一次传输。（可以理解为同时的连续访存合并，不同时不能合并，而我们能控制的同时的就只有warp）


### 逻辑层

一个kernel grid有多个block，一个block有多个thread。不同block之间完全并行，同一个block之内也是并行的，但是因为有同步接口所以可以同步。此外，同一个block的thread还可以共享内存。

### 数据搬运

数据搬运同样存在物理层和逻辑层，如果只考虑正确性，那就逻辑层随便写，但是如果想高效，物理层特性必须考虑进来。例如：

* coalesce
* DRAM，SRAM

题外话：物理层特性考虑进来确实可以高效，但是物理层并不是一成不变的，因此下层的变化会影响上层……