# cogdl

https://pytorch.org/docs/stable/sparse.html#functionality-overview

我觉得cogdl对于pyg，相当于pytorch-lightning对于pytorch。

dgl和pyg都是专注于模型的搭建。

实际上GNN要关注的不仅有model，还有data, task, trainer, sample, heterogeneous, metric, loss。

梳理一下作者的思路：

* 首先，model的作用肯定是输出embedding，这一点对于network embedding和gnn都是一样的。只不过ne直接输入graph，gnn输入graph和x。对于model，作者没有任何的抽象和限制，纯手写实现。这样的好处是非常自由，并且也可以将效率发挥到极致。（GCN举例）而且，纯手写也不难，因为上期视频也说了，scatter就可以实现message passing。


累了，我觉得cogdl封装的有点过于high level了，封装越高层，越需要详细的文档，然而文档并没有跟上……有机会再看吧。
