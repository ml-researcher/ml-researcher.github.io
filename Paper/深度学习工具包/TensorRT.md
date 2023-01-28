# TensorRT

## Nvidia-TensorRT

https://docs.nvidia.com/deeplearning/tensorrt/quick-start-guide/index.html#ecosystem

我理解TensorRT有自己的一套定义网络的方法和格式。onnx可以用来中转。怎么中转其实只是工具的问题，甚至可以直接pytorch model中转成trt。

需要注意的是TensorRT的Engine是硬件相关的。

所以TensorRT的Engine是什么格式？怎么运行？

trtexec可以把onnx转成trt。

## Github样例

https://github.com/NVIDIA/TensorRT/tree/main/samples/python/network_api_pytorch_mnist

pytorch每创建一个tensor也需要先在cpu再copy到gpu吗？pycuda是创建numpy再memcpy到gpu。

https://github.com/NVIDIA/TensorRT/blob/ef7713ca67435690f0b28dc9a50ee4021ae3651d/quickstart/IntroNotebooks/4.%20Using%20PyTorch%20through%20ONNX.ipynb

trt文件直接用需要配合pycuda。

## B站教程

https://www.bilibili.com/video/BV1Xw411f7FW

## Torch-TensorRT

https://www.youtube.com/watch?v=TU5BMU6iYZ0

从视频来看torch-tensorrt可以直接把pytorch model转成trt model运行在python里？那还用onnx干嘛？（能否理解为torch-trt是torchscript基础上做了优化？）

https://pytorch.org/TensorRT/

PTQ：给定测试集以后量化

https://pytorch.org/TensorRT/tutorials/use_from_pytorch.html#use-from-pytorch

把我绕晕了，又把ts转成了个啥？

难道说ts会调用tensorrt engine？

https://developer.nvidia.com/blog/accelerating-inference-up-to-6x-faster-in-pytorch-with-torch-tensorrt/

* torchtrtc可以把trt转成ts
* torch._C._jit_to_backend("tensorrt")可以把ts转成trt？但是这里的trt和raw trt还是不太一样，因为它可以以torch.tensor作为输入。这里转出来的是torchscript，所以相当于torch-tensorrt提供了ts跑trt的引擎？还是说提供了trt格式和ts格式之间的转化？

https://zhuanlan.zhihu.com/p/593161199