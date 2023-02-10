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