# benchmarking graph neural network

## 数据预处理方式

动态网络的表示方式：快照序列，时间窗口，连续型，时间存续的边。

1. 如何将静态GNN应用于动态网络：

   + 将快照序列中的所有边求并集；

   + 将滑动时间窗口内的快照所有边求并集；

   + 直接使用最后一刻的快照；

2. 离散GNN应用于动态网络：本身就支持快照序列表示的动态图，但是不同数据集的快照长度是不同的，所以使用滑动窗口的方式。

