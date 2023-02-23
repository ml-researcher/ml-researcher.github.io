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



### 逻辑层

一个kernel grid有多个block，一个block有多个thread。不同block之间完全并行，同一个block之内也是并行的，但是因为有同步接口所以可以同步。此外，同一个block的thread还可以共享内存。

### 数据搬运

DRAM，SRAM