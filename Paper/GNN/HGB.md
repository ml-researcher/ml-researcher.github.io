# HGB

## 相关工作

### 同构

频域和空域的方法。

#### 频域

ChebNet

GCN

SGC

GWNN

#### 空域

空域主要包括：

* 聚合方式的不同设计：注意力、GIN
* 适应大图的inductive：GraphSAGE、FastGCN
* 正则化：GRAND、DropEdge、GCNII

GAT

GraphSAGE

FastGCN：这篇文章我觉得数学上还算优美（除了最后一步近似）。

DropEdge：我觉得没回答我最关心的几个问题

* 训练的时候如何保证scale的期望不变？（这就涉及到dropout的操作了）
* 预测的时候是否还drop？
* GCN可以用规范化，GAT怎么办？
* 对称地采样吗？双向边都drop？

这篇文章太不优雅了……明明是有偏的硬要说无偏，如果只是单纯求均值确实是无偏的，所以GAT和均值GCN可以，但是对称规范化的GCN就不行了。

而且结论居然就是直接随机drop边，相当于丢掉了图的信息啊……这算什么解决方案。

摘要也再完善一下。