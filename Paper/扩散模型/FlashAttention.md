# FlashAttention

## Shared Memory

* https://developer.nvidia.com/blog/using-shared-memory-cuda-cc/

shared memory是以block为单位共享的，同一个block里的thread共享同一片shared memory。

* 谁来加载数据？总不能每个thread加载一次
* 如何避免计算冲突？
* 谁来搬运计算结果？总不能每个thread搬运一次


# x-formers

https://github.com/facebookresearch/xformers

