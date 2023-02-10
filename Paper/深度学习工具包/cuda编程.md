# cuda

## 自定义c++算子

* https://pytorch.org/tutorials/advanced/cpp_extension.html
* https://developer.nvidia.com/blog/even-easier-introduction-cuda/

所以写一个cuda kernel和写一个torch算子还不是一回事。

https://zhuanlan.zhihu.com/p/48463543

## cuda编程

https://face2ai.com/program-blog/#GPU编程（CUDA）

Professional CUDA C Programming

我觉得现在应该分清主次，要深挖下去没有头，还是得high level地去学，用到再查。（虽然真的很有意思，延迟隐藏还没研究明白）

我现在主要的问题：

* 如何把flash attention用起来
* 如何继续优化onnx for unet
* 如何把flash attention的onnx和tensorrt打通

发现打通不了……文档和例子太少了。

先把TensorRT里现有的bertQKVToContextPlugin插件用起来。

