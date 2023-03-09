# mindspore


https://pytorch.org/docs/stable/notes/extending.html

自定义算子，我觉得会涉及计算图，Function就是计算图，有输入有输出有正向有反向。

## jittor

我想先学一学jittor的思路。

元算子：https://github.com/Jittor/jittor/blob/master/python/jittor/notebook/meta_op.src.md

用元算子实现的矩阵乘法不会占用更多内存吗？（动态编译，循环优化！）

我觉得jittor是一个kernel generator！从这个角度来看的话它和Triton很像啊。

