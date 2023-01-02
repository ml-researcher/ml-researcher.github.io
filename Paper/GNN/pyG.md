# pyG

scatter ops

sparse matrix

总感觉没有一个统一的graph类管理会有点奇怪，比如edge feature没有地方放。

第一个感觉就是pyg的设计会更加自由。比如Data不会加入合法检查，比起dgl.graph更像一个普通的字典……

第二个感觉是底层算子更多，可扩展性更强，文档也更丰富。虽然dgl.ops里也有不少算子，但是文档太少了。

第三个就是pyg升级不完全，向下兼容原来的矩阵实现。

从代码里可以看出，pyg不仅支持message passing，还支持sparse tensor。

第一部分：怎么message func和aggregate func，怎么apply_edges。

第二部分：底层算子scatter库的使用和源码研究！！！

遇到一个问题：nn.Module的__call__和forward的关系！

ptr怎么用？aggr函数额外的参数怎么传？

dgl像是一个统一框架，一套组合拳覆盖所有GNN。pyg像是一个工具包，提供了一些底层算子，怎么实现有多种选择。

torch-scatter torch-sparse torch-cluster torch-spline-conv

怎么设置LSTM聚合函数？
