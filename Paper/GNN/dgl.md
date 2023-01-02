# dgl

前言：我认为深度学习的门槛在逐渐升高，开发厉害的工具包、想新奇的点子、数学、工程实践。作为硕士，我想开发使用工具包这个赛道。

## Episode 1

大家好，这期视频我们开启一个新的篇章——图神经网络工具包的源码分析。计划会研读dgl、pyG、cogdl这三个包的源码。在学习GNN模型的同时，学习模型实现的技巧。

首先是dgl。dgl的教程确实很好，就是对新手会有些门槛。因为太多交叉引用了，而且一些解释也不太全，需要自己读源码。但是又让人欲罢不能啊，因为很多代码写的太妙了，让我自己写根本写不出来！

这个系列的视频力求两点：既能够保证零基础入门，又能够深入分析实现原理。（当然也不会挖地太深，只要能够理解使用逻辑就够了。因为太难了。。越看到底层，发现会有自定义的算子，甚至自定义的cuda kernel。咋办。（暂时不管，学会高阶算子的用法就完事）研究了dgl代码感觉自己就是个只会应用层的弟中弟。anyway，知道自己不会什么也算是一种进步吧……）

### Part 1: dgl.graph

这里主要梳理一下逻辑。message function, reduce function, update function。

#### 图的构造

同构图的表示。本质上就是一个矩阵（通常是稀疏矩阵）。

现在有很多库都可以支持图的表示，比如numpy, torch.sparse, scipy, networkx。

但是本质上这些矩阵表示法就是几种：adj, coo, csr, csc, lil, bsr, bsc

这些可能更多的是一些规则的学习，现用现查。最常用的就是adj, coo, csr。

梳理一下同构图和稀疏矩阵的关系：稀疏矩阵是表示同构图的一种方式。稀疏矩阵其实更广义一下，因为不一定是方阵。

* dgl.graph直接构造：https://docs.dgl.ai/en/latest/generated/dgl.graph.html
* dgl.from_scipy：https://docs.dgl.ai/en/latest/generated/dgl.from_scipy.html#dgl.from_scipy
* dgl.from_networkx：https://docs.dgl.ai/en/latest/generated/dgl.from_networkx.html#dgl.from_networkx

异构图的表示。

https://docs.dgl.ai/en/latest/generated/dgl.heterograph.html#dgl.heterograph

异构图用矩阵表示的时候需要小心，因为有可能有重边（同样的端点不同的边类型）比如torch.coalesce()

异构图直接传播不常用，顶多就是构造好异构图然后to_homogeneous。

另外还有两种dgl特有的图：subgraph block和bipartite graph。

#### 图信息的访问

同构图：g.edges() g.nodes() g.adj() g.edata g.ndata

异构图：

* g.edata[dgl.EID] g.edata[dgl.ETYPE]
* g.ndata[dgl.NID] g.ndata[dgl.NTYPE]
* g.ntypes g.etypes g.canonical_etypes
* g[etype]
* g.srcdata g.dstdata g.srctypes g.dsttypes
* g.nodes() g.edges() g.num_nodes() g.num_edges()
* g.metagraph().edges

异构图转同构图dgl.to_homogeneous()会产生dgl.ETYPE和dgl.NTYPE。

#### 信息在图上的传播

这应该是dgl最最核心的一个设计。那就是把**所有**的GNN都抽象为了这一范式。

https://docs.dgl.ai/tutorials/blitz/3_message_passing.html

* https://docs.dgl.ai/en/latest/api/python/udf.html
* https://docs.dgl.ai/en/latest/api/python/dgl.function.html#api-built-in
* https://docs.dgl.ai/en/latest/guide/message-efficient.html
* https://docs.dgl.ai/en/latest/generated/dgl.DGLGraph.update_all.html

reduce_func会根据入度分batch，如果入度是0，默认是不会调用reduce的，直接对输出置零。

另外就是local_scope：https://docs.dgl.ai/en/latest/generated/dgl.DGLGraph.local_scope.html

* https://docs.dgl.ai/en/latest/guide/nn-forward.html

heterograph的支持还不是太好，只能一次update一种edge type，multi_update_all也只能先内部聚合再跨种类聚合。

上面的介绍只适用于节点分类全图。下面会有链接预测、图分类、子图采样。

此外，除了update_all以外还有apply_edges。

在链接预测里面看到了一种用法，本质上就是apply_edges：https://docs.dgl.ai/en/latest/tutorials/blitz/4_link_predict.html#sphx-glr-tutorials-blitz-4-link-predict-py

### Part 1.5

#### Special Graph

在图分类里面也会学到dataloader：https://docs.dgl.ai/en/latest/tutorials/blitz/5_graph_classification.html#sphx-glr-tutorials-blitz-5-graph-classification-py

里面有一个概念是batched graph，和block不是一个意思。

g.batch_num_nodes() g.batch_num_edges() dgl.unbatch(g) dgl.mean_nodes(g, field)

另一个概念是block，用于minibatch training：https://docs.dgl.ai/en/latest/guide/minibatch-node.html#guide-minibatch-node-classification-sampler

https://docs.dgl.ai/generated/dgl.to_block.html#dgl-to-block

block就是一个二分图。expand_as_pair里面有判断是否is_block。而且用sampler采样出来的block默认dst nodes是前面的序号。另外看一下srcdata和dstdata是不是block特有的？（不是！还可以构造手动的异构二分图！）

#### dgl.data.utils

里面的download和extract_archive也挺有用的。

### Part 2: GraphConv (GCN)

dgl里的实现是归一化的时候没管edge weight。

### Part 3: RelGraphConv (RGCN)

自己实现算子！！！

### Part 4: GATConv (GAT)

edge_softmax

https://docs.dgl.ai/en/0.8.x/generated/dgl.nn.functional.edge_softmax.html

https://github.com/THUDM/HGB/blob/master/NC/benchmark/methods/baseline/conv.py

## GraphSAGE

## MPNN

## GIN

## SGC

## PNA
