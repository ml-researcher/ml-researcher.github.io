# mindspore


https://pytorch.org/docs/stable/notes/extending.html

自定义算子，我觉得会涉及计算图，Function就是计算图，有输入有输出有正向有反向。

## jittor

我想先学一学jittor的思路。

元算子：https://github.com/Jittor/jittor/blob/master/python/jittor/notebook/meta_op.src.md

用元算子实现的矩阵乘法不会占用更多内存吗？（动态编译，循环优化！）

我觉得jittor是一个kernel generator！从这个角度来看的话它和Triton很像啊。

主要贡献：

* 元算子
* 动态编译、实时优化、算子融合
* 统一图运行（区别于动态图和静态图）

## mindspore自动微分

source transformation，感觉貌似也不是太懂……不过这样做肯定有他的道理吧，估计是为了多平台运行？还不确定。